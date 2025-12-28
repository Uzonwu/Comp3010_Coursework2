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

## installing and prepping dataset
- I have splunk installed and set up on unbuntu
- The Botsv3 dataset has been downloaded and ingested into splunk (placed in /apps and enabled via splunk web interface)
- I ran checks to verify that the dataset was ingested properly, searching for some of the data sourcetypes listed on the repo. Event counts across indexes to confirm successful ingestion. AWS CloudTrail logs validated using the aws:cloudtrail source type. S3 access logs, endpoint logs, and hardware data included
- Screenshots for this stage is under ss/00

## understanding SOC context and roles
- I have written background sections (mostly a draft for now with references) on what a SOC is, why they exist and what they do in practice
- I reviewed the SOC tiered model, of which there are 3, that is monitoring and triage, investigation and correlation and threat hunting and detection engineering
- I've drafted a reflection on incident handling lifecycle (which is detection, analysis, containment, eradication, recovery and lessons learned)
- At this point I haven't done much that is technical in Splunk, just setting up a frame for guided answers and stuff I'll find during the SOC investigation

## IAM activity analysis (Q1, Q2)

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

## infrastructure awareness (Q3)
- I've switched focus away from AWS to underlying infrastructure
- First I verified that hardware telemetry existed with: ```sourcetype=hardware | head 5```
- I then identified web servers by host count with: ```sourcetype=hardware | stats count by host```
- The web servers listed are gacrux.i-06fea586f3d3c8ce8, gacrux.i-09cbc261e84259b54, gacrux.i-0cc93bade2b3cba63
- Taking the first in the list, I pulled CPU info with the query: ```sourcetype=hardware host=gacrux.i-06fea586f3d3c8ce8 | table host cpu```
- I confirmed the cpu type is Intel Xeon E5-2676 v3 CPUs
- This stage is mostly about asset visibility and baseline knowledge
- Screenshots saved under ss/02

## S3 misconfiguration investigation (Q4, Q5, Q6)
- I noticed that the analysis is now becoming more incident focused from here
- I started by looking at S3-related API calls with: ```sourcetype=aws:cloudtrail eventSource=s3.amazonaws.com | stats count by eventName | sort -count```
- I identified PutBucketAcl as the key high-risk action with count 2
- I then narrowed down to the exact events, insecting IAM username, finding out what bucket were made public with requestParameters, alltogether in the query of: ```sourcetype=aws:cloudtrail eventName="PutBucketAcl" | table _time eventID userIdentity.userName requestParameters.bucketName | sort _time```
- And found username: bstoll, bucket: frothlywebcode and event ID: ab45689d-69cd-41e7-8705-5350402cf7ac
- I noted that the quiz expected the eventID, but the report needed the API action, user and resource
- This part has shown me the root cause of the incident
- Screenshots saved under ss/03

## confirming data exposure (Q7)
- This part is basically confirming whether the misconfiguration actually led to exposure or not
- From what I saw, S3 access logs initially didn't return results when filtering by field name
- So I pivoted to raw search with: ```sourcetype=aws:s3:accesslogs "frothlywebcode"```
- Scrolled looking for an event with the same bucket name as Q6, Get file and found evidence of this under REST.GET.OBJECT, the object: OPEN_BUCKET_PLEASE_FIX.txt, HTTP 200 response
- I was able to confirm unauthenticated access, meaning real data exposure occurred
- This completed the incident timeline being IAM without MFA, S3 ACL change and then public object access
- Screenshots saved under ss/04

## endpoint baseline analysis (Q8)
- This part focused on endpoint hygiene rather than AWS
- following the hint, I searched the sourcetype winhostmon, using field summary to look through the fields: ```sourcetype=winhostmon | fieldsummary```
- I then tailored my search for operating systems after finding OS in the list and compared endpoints using: ```sourcetype=winhostmon | stats count by host OS | sort OS```
- This gave me 8 OS values, seven of which are Microsoft Windows 10 Pro and one which is Microsoft Windows 10 Enterprise
- I identified that BSTOLL-L as the only endpoint running Windows 10 Enterprise
- The quiz required FQDN format, which is apparently derived from the hostname and the domain which is froth.ly, hence the answer bstoll-l.froth.ly. (i didnt know this and i'm not sure why the quiz is asking for this format yet*find out)
- Screenshots saved under ss/05