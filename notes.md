# Notes

## personal reminders for splunk

to start
```
cd /opt/splunk/bin
sudo ./splunk start --accept-license
```

to end

```
sudo ./splunk stop
```

## first stage - installing and prepping dataset
- I have splunk installed and set up on unbuntu
- The Botsv3 dataset has been downloaded and ingested into splunk (placed in /apps and enabled via splunk web interface)
- I ran checks to verify that the dataset was ingested properly, searching for some of the data sourcetypes listed on the repo. Event counts across indexes to confirm successful ingestion. AWS CloudTrail logs validated using the aws:cloudtrail source type. S3 access logs, endpoint logs, and hardware data included
- Screenshots for this stage is under ss/00

## second stage - understanding SOC context and roles
- I have written background sections (mostly a draft for now with references) on what a SOC is, why they exist and what they do in practice
- I reviewed the SOC tiered model, of which there are 3, that is monitoring and triage, investigation and correlation and threat hunting and detection engineering
- I've drafted a reflection on incident handling lifecycle (which is detection, analysis, containment, eradication, recovery and lessons learned)
- At this point I haven't done much that is technical in Splunk, just setting up a frame for guided answers and stuff I'll find during the SOC investigation

## third stage - IAM activity analysis (Q1, Q2)

### Q1 - identifying IAM users

- Here I used CloudTrail logs to find out which IAM users accessed AWS services
- I first verified the source type with: ```sourcetype=aws:cloudtrail```
- I looked through to find which IAM usernames appear under userIdentity.userName with: ```sourcetype=aws:cloudtrail | stats count by userIdentity.userName```
- The listed users were bstoll, btun, splunk_access, and web_admin
- I also noted difference between likely service accounts and named user accounts
- Screenshots saved under ss/01

### Q2 - identifying IAM users without MFA

- Still on the CloudTrail sourceType, I excluded console logins with: ```eventName!=ConsoleLogin``` and then added a keyword search for ```*mfa*```
- Altimately, since I am looking for a field similar to `userIdentity.sessionContext.attributes.mfaAuthenticated` according to the CloudTrail userIdentity element (AWS documentation), I tailored the search to directly find it by querying: ```sourcetype=aws:cloudtrail userIdentity.sessionContext.attributes.mfaAuthenticated="false" | stats count by userIdentity.userName | sort -count```
- I found that all four users accessed AWS services without MFA
- I flagged web_admin and bstoll as higher risk due to volume and likely privileges (seen from the count)
- Important note: lack of MFA is a major contributing weakness
- Screenshots saved under ss/01

## fourth stage - infrastructure awareness (Q3)
- I've switched focus away from AWS to underlying infrastructure
- First I verified that hardware telemetry existed with: ```sourcetype=hardware | head 5```
- I then identified web servers by host count with: ```sourcetype=hardware | stats count by host```
- The web servers listed are gacrux.i-06fea586f3d3c8ce8, gacrux.i-09cbc261e84259b54, gacrux.i-0cc93bade2b3cba63
- Taking the first in the list, I pulled CPU info with the query: ```sourcetype=hardware host=gacrux.i-06fea586f3d3c8ce8 | table host cpu```
- I confirmed the cpu type is Intel Xeon E5-2676 v3 CPUs
- This stage is mostly about asset visibility and baseline knowledge
- Screenshots saved under ss/02