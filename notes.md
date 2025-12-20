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
