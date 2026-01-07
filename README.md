# Comp3010 Coursework 2: BOTSv3 Incident Analysis and Presentation

## Video Walkthrough
[![My walkthrough](https://img.youtube.com/vi/T3Yy7aGKDHI/0.jpg)](https://www.youtube.com/watch?v=T3Yy7aGKDHI)

## Introduction

### Security Operations Centre (SOC)

A Security Operations Centre (SOC) is a centralised function responsible for monitoring, detecting, and responding to security threats across an organisation's information systems. In modern cybersecurity environments, SOCs have evolved from physical locations where analysts manually reviewed alerts into integrated operations that leverage log aggregation, analytics, and automation to counter increasingly sophisticated threats [6], [7].

### Why SOCs Exist

SOCs exist to protect organisational assets from threats such as unauthorised access, malware, and data breaches. They provide a centralised capability for incident detection and response, reduce attacker dwell time through early identification, support regulatory compliance through monitoring and documentation, and address the growing complexity introduced by cloud and hybrid infrastructures [1], [6], [11].

### What SOCs Do

At a high level, SOC operations consist of continuous monitoring, detection and analysis of security events, coordinated incident response, and continuous improvement of detection capabilities. These activities are supported by technologies such as Security Information and Event Management (SIEM) and Endpoint Detection and Response (EDR) platforms, which enable analysts to correlate and analyse data across diverse systems [7], [9], [12].

SOCs typically operate using a tiered structure that includes analysts, incident responders, engineers, and managers, each with clearly defined responsibilities [6].

### BOTS v3 Overview

Boss of the SOC version 3 (BOTS v3) is a publicly available security dataset developed by Splunk to simulate a realistic enterprise security incident. The dataset represents a fictional brewing company, Frothly, operating a hybrid environment with on-premises systems and cloud infrastructure hosted on Amazon Web Services (AWS) [13].

BOTS v3 contains pre-indexed logs across multiple domains, including AWS CloudTrail logs, S3 access logs, Windows endpoint telemetry, and hardware monitoring data. These logs reflect the scale and diversity of telemetry typically analysed by SOC analysts. The exercise is designed to develop practical skills in log analysis, incident investigation, and threat detection using Splunk's Search Processing Language (SPL) [13].

### Investigation Objectives

The objectives of this investigation are to analyse AWS CloudTrail logs to identify IAM activity and authentication weaknesses, investigate AWS misconfigurations such as publicly accessible S3 buckets, correlate cloud and endpoint logs to reconstruct security events, demonstrate SOC investigative workflows using SPL, and reflect on SOC roles and incident handling practices [3], [5], [8].

### Scope and Assumptions

This investigation is limited to the data provided within the BOTS v3 dataset. No live systems, external threat intelligence feeds, or real-time alerting mechanisms were used. It is assumed that the dataset is complete, accurately timestamped, and representative of activity within the fictional Frothly environment. The focus is on log analysis and incident reconstruction rather than remediation implementation, which is discussed conceptually in alignment with SOC best practices [12].

## SOC Roles and Incident Handling Reflection

### SOC Roles (Tiered Model)

SOCs commonly employ a tiered structure to ensure efficient incident handling and technical escalation [6].

Tier 1 analysts perform continuous monitoring and initial alert triage, reviewing logs and alerts to identify suspicious activity requiring escalation. In a Splunk environment, this involves dashboard review and ad hoc searching across relevant indexes such as those used in BOTS v3 [11], [13].

Tier 2 analysts conduct deeper investigations into escalated incidents, correlating activity across multiple data sources to confirm incidents and determine scope and impact. In this investigation, Tier 2 responsibilities align with analysing CloudTrail, S3 access logs, and endpoint telemetry to validate misconfigurations and data exposure [7], [8].

Tier 3 analysts focus on advanced activities such as threat hunting and detection engineering. Their role involves improving detection logic, reducing false positives, and identifying coverage gaps. In the context of this investigation, Tier 3 responsibilities are reflected conceptually through identifying how high-risk AWS actions could be more effectively detected [9], [8].

### Incident Handling Lifecycle

Incident response follows a structured lifecycle that includes preparation, detection and analysis, containment and eradication, recovery, and lessons learned [12]. In the BOTS v3 dataset, detection is simulated through manual log analysis rather than real-time alerting. Analysis is supported by Splunk's ability to centralise and correlate diverse data sources. While containment and recovery actions are not executed, a real SOC would revoke compromised credentials, restrict public access to misconfigured resources, and restore systems to a secure state.

The lessons learned phase is particularly important, as it informs improvements to detection logic and preventative controls, such as enforcing MFA and monitoring high-risk cloud actions [4].

## Installation and Data Preparation

Splunk Enterprise was installed locally and configured as a standalone SIEM instance to simulate a simplified SOC environment. The Boss of the SOC version 3 (BOTS v3) dataset was downloaded from the official Splunk repository and prepared for ingestion.

![Figure 1](ss/00/03_dataset_download.png?raw=true)
Figure 1: BOTS v3 dataset successfully downloaded prior to ingestion.

Prior to ingestion, dataset integrity was verified using MD5 hash comparison to ensure the files had not been corrupted or altered during download. This step is critical in SOC contexts to maintain evidential integrity and confidence in subsequent analysis.

![Figure 2](ss/00/04_md5_hash_check.png?raw=true)
Figure 2: MD5 hash verification confirming dataset integrity before ingestion.

The dataset was ingested into Splunk using the Apps framework, after which successful loading was verified by confirming event counts and indexed data availability. Multiple sourcetypes were validated to ensure coverage across cloud, endpoint, and infrastructure telemetry, including:
aws:cloudtrail
aws:s3:accesslogs
winhostmon
hardware

![Figure 3](ss/00/06_dataset_ingestion.png?raw=true)
Figure 3: Dataset ingestion process initiated within Splunk.

![Figure 4](ss/00/07_dataset_loading_in_splunk.png?raw=true)
Figure 4: Dataset loading confirmation within the Splunk interface.

Event presence and sourcetype correctness were confirmed through targeted searches, ensuring that the dataset was fully operational prior to investigation. From a SOC perspective, validating data ingestion and sourcetype accuracy is essential, as incomplete or misclassified data can lead to missed detections, false conclusions, or ineffective incident response.

![Figure 5](ss/00/08_confirming_events.png?raw=true)
Figure 5: Verification of indexed events following ingestion.

![Figure 6](ss/00/09_confirming_sourceTypes(cloudtrail).png?raw=true)
Figure 6: Confirmation of aws:cloudtrail sourcetype availability.

![Figure 7](ss/00/10_confirming_sourceTypes(s3accesslogs).png?raw=true)
Figure 7: Confirmation of aws:s3:accesslogs sourcetype availability.

![Figure 8](ss/00/11_confirming_sourceTypes(winhostmon).png?raw=true)
Figure 8: Confirmation of Windows (winhostmon) and hardware sourcetypes.


## Investigation and Analysis

### Q1 - IAM Users Accessing AWS Services

To identify IAM users accessing AWS services, CloudTrail logs were analysed using the following SPL query:

```
index=* sourcetype=aws:cloudtrail | stats count by userIdentity.userName | sort -count
```

The results show that the users `splunk_access`, `web_admin`, `bstoll`, and `btun` performed AWS API actions. The presence of both service-style and named user accounts indicates a mix of automated and user-driven activity, establishing a baseline for further authentication and risk analysis [2].

![Figure 9](ss/01/14_finding_IAM_usernames.png?raw=true)
Figure 9: CloudTrail aggregation showing IAM usernames and associated API activity counts (Q1).

### Q2 - IAM Users Accessing AWS Services Without MFA

To identify AWS API activity performed without multi-factor authentication (MFA), CloudTrail logs were filtered using the field `userIdentity.sessionContext.attributes.mfaAuthenticated` [3]:

```
index=* sourcetype=aws:cloudtrail userIdentity.sessionContext.attributes.mfaAuthenticated="false" | stats count by userIdentity.userName | sort -count
```

The results indicate that web_admin, bstoll, btun, and splunk_access accessed AWS services without MFA enabled. High volumes of unauthenticated activity by web_admin and bstoll represent significant risk, as compromised credentials without MFA increase the likelihood of unauthorised access [4].

![Figure 10](ss/01/17_userIdentity_element.png?raw=true)
Figure 10: CloudTrail results showing AWS API activity performed without MFA (Q2).

### Q3 - Web Servers Processor Number

Hardware logs were analysed to identify processor information for web servers. Data presence was confirmed:

```
index=* sourcetype=hardware | head 5
```
Hosts were then identified:

```
index=* sourcetype=hardware | stats count by host | sort -count
```

This returned the following web servers:

- gacrux.i-06fea586f3d3c8ce8

- gacrux.i-09cbc261e84259b54

- gacrux.i-0cc93bade2b3cba63

![Figure 11](ss/02/19_web_servers.png?raw=true)
Figure 11: The web server hosts (Q3 context).

Processor information was extracted via viewing event.

This shows that the web servers run Intel(R) Xeon(R) CPU `E5-2676` v3 @ 2.40GHz processors. Hardware visibility supports asset inventory and incident scoping during investigations [6].

![Figure 12](ss/02/20_cpu_type.png?raw=true)
Figure 12: Hardware telemetry showing web server host(s) and CPU information (Q3).

### Q4, Q5, Q6 - S3 Public Access Misconfiguration (API, User, Bucket)

CloudTrail logs were analysed to identify high-risk S3 actions:

```
index=* sourcetype=aws:cloudtrail eventSource=s3.amazonaws.com | stats count by eventName | sort -count
```

The PutBucketAcl API call was identified as a high-risk operation capable of enabling public access [5]. 

![Figure 13](ss/03/22_noting_PutBucketAcl_highrisk.png?raw=true)
Figure 13: CloudTrail event frequency results showing S3 API calls including PutBucketAcl (Q4 context).

Further analysis showed:

```
index=* sourcetype=aws:cloudtrail eventName="PutBucketAcl" | table _time eventID requestID userIdentity.userName eventSource requestParameters.bucketName requestParameters.x-amz-acl userAgent sourceIPAddress | sort _time
```

The IAM user `bstoll` executed `PutBucketAcl` against the `frothlywebcode` bucket (eventID: `ab45689d-69cd-41e7-8705-5350402cf7ac`). This represents the point at which bucket permissions were modified.

![Figure 14](ss/03/24_username_bucket_eventID.png?raw=true)
Figure 14: CloudTrail details showing PutBucketAcl executed by bstoll on frothlywebcode with event metadata (Q4-Q6 evidence).

### Q7 - Evidence of Public Object Access

S3 access logs were analysed to determine whether objects were accessed publicly:

```
index=* sourcetype=aws:s3:accesslogs "frothlywebcode" | table _time host source sourcetype _raw | head 20
```

The logs show successful unauthenticated access to `OPEN_BUCKET_PLEASE_FIX.txt` via the REST.GET.OBJECT operation with HTTP status 200, confirming real data exposure [12].

![Figure 15](ss/04/26_GET_file.png?raw=true)
Figure 15: S3 access log entry showing REST.GET.OBJECT access to OPEN_BUCKET_PLEASE_FIX.txt with HTTP 200 (Q7).

### Q8 - Endpoint Running a Different Windows Version

Endpoint operating systems were analysed using winhostmon:

```
index=* sourcetype=winhostmon | fieldsummary
```

![Figure 16](ss/05/28_finding_OS.png?raw=true)
Figure 16: winhostmon field summary showing distinct OS values (Q8 context).

Two OS versions were identified: Windows 10 Pro and Windows 10 Enterprise. Comparison by host:

```
index=* sourcetype=winhostmon | stats count by host OS | sort OS
```

The endpoint `BSTOLL-L` was identified as running Windows 10 Enterprise, while others ran Windows 10 Pro. Inconsistent baselines can introduce security and management risks [9]. Based on hostname normalisation and organisational domain usage, the FQDN is derived as `bstoll-l.froth.ly`.

![Figure 17](ss/05/30_BSTOLL-L.png?raw=true)
Figure 17: Endpoint OS comparison showing BSTOLL-L as the Windows 10 Enterprise outlier (Q8 evidence).

## Conclusion

This investigation applied a SOC-aligned analytical approach to the BOTS v3 dataset to identify security risks, misconfigurations, and confirmed data exposure within a simulated enterprise environment. Using Splunk as a SIEM platform, cloud, endpoint, and infrastructure logs were correlated to reconstruct a realistic security incident involving AWS IAM weaknesses and an S3 bucket misconfiguration.

The analysis identified multiple IAM users accessing AWS services without MFA and confirmed that a PutBucketAcl action resulted in a publicly accessible S3 bucket. Subsequent access logs verified unauthenticated object access, demonstrating real exposure rather than a theoretical risk. Endpoint analysis further highlighted the importance of maintaining consistent baselines across systems.

Overall, the investigation reinforces the importance of continuous monitoring, strong authentication controls, and proactive detection engineering in modern SOC operations. While BOTS v3 represents a simulated environment, the investigative techniques and lessons learned are directly applicable to real-world SOC workflows where early detection and effective response reduce organisational risk [12].

## References

[1] M. Abd Majid and K. A. Zainol Ariffin, "Model for successful development and implementation of Cyber Security Operations Centre (SOC)," PLOS ONE, vol. 16, no. 11, 2021, Art. no. e0260157, doi: 10.1371/journal.pone.0260157.

[2] "Understanding CloudTrail events," Amazon Web Services (AWS) Documentation. [Online]. Available: https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-events.html

[3] "CloudTrail userIdentity element," Amazon Web Services (AWS) Documentation. [Online]. Available: https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html

[4] "Logging IAM and AWS STS API calls with AWS CloudTrail," Amazon Web Services (AWS) Documentation. [Online]. Available: https://docs.aws.amazon.com/IAM/latest/UserGuide/cloudtrail-integration.html

[5] "PutBucketAcl - Amazon Simple Storage Service," Amazon Web Services (AWS) Documentation. [Online]. Available: https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketAcl.html

[6] A. Basta et al., Open-Source Security Operations Center (SOC): A Complete Guide to Establishing, Managing, and Maintaining a Modern SOC. Hoboken, NJ, USA: Wiley, 2024.

[7] R. Bejtlich, The Practice of Network Security Monitoring: Understanding Incident Detection and Response. San Francisco, CA, USA: No Starch Press, 2013.

[8] S. A. Chamkar, Y. Maleh, and N. Gherabi, "Security Operations Centers: Use case best practices, coverage, and gap analysis based on MITRE adversarial tactics, techniques, and common knowledge," Journal of Cybersecurity and Privacy, vol. 4, no. 4, pp. 777-793, 2024, doi: 10.3390/jcp4040036.

[9] J. Forsberg and T. Frantti, "Technical performance metrics of a security operations center," Computers & Security, vol. 135, 2023, Art. no. 103529, doi: 10.1016/j.cose.2023.103529.

[10] N. Miloslavskaya, "Security Operations Centers for Information Security Incident Management," in Proc. 2016 IEEE 4th Int. Conf. on Future Internet of Things and Cloud (FiCloud), 2016, pp. 131-136, doi: 10.1109/FiCloud.2016.26.

[11] D. Nathans, Designing and Building a Security Operations Center. Waltham, MA, USA: Syngress, 2015.

[12] K. Scarfone, P. Mell, T. Grance, and K. Masone, Computer Security Incident Handling Guide, NIST Special Publication 800-61 Revision 2, 2012. [Online]. Available: https://nvlpubs.nist.gov/nistpubs/specialpublications/nist.sp.800-61r2.pdf

[13] "splunk/botsv3," GitHub repository. [Online]. Available: https://github.com/splunk/botsv3
