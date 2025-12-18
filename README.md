# Comp3010_Coursework2
## Notes for now
### first stage - installing and prepping dataset
- I have Splunk installed and set up on unbuntu
- The Botsv3 dataset has been downloaded and ingested into splunk (placed in /apps and enabled via splunk web interface)
- I ran checks to verify that the dataset was ingested properly, searching for some of the data sourcetypes listed on the repo. Event counts across indexes to confirm successful ingestion. AWS CloudTrail logs validated using the aws:cloudtrail source type. S3 access logs, endpoint logs, and hardware data included.
- Screenshots for this stage is under ss/00
