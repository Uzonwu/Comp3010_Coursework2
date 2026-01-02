# Comp3010_Coursework2

## Introduction

### Security Operations Centre (SOC)

A Security Operations Centre (SOC) is a centralised function responsible for monitoring, detecting, and responding to security threats across an organisation's information systems. In the modern cybersecurity landscape, SOCs have evolved from physical locations where analysts manually monitored alerts into highly integrated operations that leverage log aggregation, analytics, and automation to counter increasingly sophisticated cyber threats. [6], [7].

### Why SOCs Exist

SOCs exist to protect an organisation's critical assets from a wide range of threats, including unauthorised access, malware, and data breaches. They provide several essential capabilities that justify their role within modern enterprises:

- Centralised Defence: SOCs provide a unified approach to identifying, investigating, and responding to security incidents across diverse environments. [1].

- Early Threat Detection and Response: By detecting suspicious activity at an early stage, SOCs reduce dwell time and limit potential damage to systems and data. [7].

- Regulatory Compliance: Many organisations operate under regulatory frameworks such as GDPR and PCI DSS; SOCs support compliance through continuous monitoring and incident documentation. [11].

- Managing Complexity: The adoption of cloud computing and hybrid environments increases attack surface and operational complexity, requiring dedicated teams and specialised tooling to maintain visibility and control. [6].

### What SOCs Do

At a high level, SOC operations revolve around several interrelated activities:

1. Continuous Monitoring: SOCs operate on a 24/7 basis, collecting and reviewing logs, network traffic, and system events to identify indicators of compromise. [7].

2. Detection and Analysis: Collected data is analysed to determine whether suspicious activity represents a genuine security incident. Analysts correlate events across multiple data sources and apply threat intelligence to identify known attack techniques. [8].

3. Incident Response: Once an incident is confirmed, the SOC coordinates containment, eradication, and recovery activities, such as isolating affected systems, removing malicious artefacts, and restoring normal operations. [12].

4. Analysis and Continuous Improvement: Lessons learned are used to refine detection rules, improve alerting, and strengthen overall security posture. Tools such as SIEM and EDR platforms play a key role in supporting these activities. [9].

To perform these functions effectively, SOCs typically operate using a tiered structure that includes SOC analysts, incident responders, security engineers, and SOC managers, each with clearly defined responsibilities. [6].

### BOTS v3 Overview

Boss of the SOC version 3 (BOTS v3) is a publicly available security dataset and training exercise developed by Splunk to simulate a realistic enterprise security incident. The dataset represents a fictional organisation, Frothly, a brewing company that operates a hybrid IT environment consisting of on-premises systems and cloud-based infrastructure hosted on Amazon Web Services (AWS). [13].

BOTS v3 contains a large collection of pre-indexed logs covering multiple domains, including AWS CloudTrail logs, S3 access logs, Windows endpoint telemetry, and hardware/system monitoring data. These logs are designed to replicate the type and volume of telemetry typically analysed by SOC analysts during real-world investigations. [13].

The purpose of the BOTS v3 exercise is to develop practical skills in log analysis, incident investigation, and threat detection using Splunk's Search Processing Language (SPL). By working through guided investigative questions, analysts identify suspicious activity, misconfigurations, and security weaknesses while applying SOC-aligned investigative methodologies. [13].

### Investigation Objectives

The primary objectives of this investigation are to:

- Analyse AWS CloudTrail logs to identify IAM user activity and potential authentication weaknesses. [3].

- Investigate misconfigurations within AWS services, including publicly accessible S3 buckets. [5].

- Correlate cloud, endpoint, and infrastructure logs to reconstruct security-relevant events.

- Demonstrate the application of SOC investigative workflows using Splunk SPL.

- Reflect on SOC roles, incident handling processes, and opportunities for detection and response improvement, including coverage mapping approaches such as MITRE ATT&CK. [8].

### Scope and Assumptions

The scope of this assignment is limited to the data provided within the BOTS v3 dataset. No live systems, external threat intelligence feeds, or real-time alerting mechanisms were used as part of the analysis. The investigation was conducted using a standalone Splunk Enterprise deployment to simulate a simplified SOC environment.

It is assumed that the logs within the dataset are complete, accurately timestamped, and representative of activity within the fictional Frothly environment. Any identified security incidents or misconfigurations are assumed to have occurred solely within the context of the dataset and do not reflect real-world organisational systems.

The focus of this assignment is on log analysis and incident reconstruction rather than the implementation of remediation actions, which are discussed conceptually in alignment with SOC best practices and incident handling guidance. [12].

## SOC Roles and Incident Handling Reflection

### SOC Roles (Tiered Model)

A SOC typically employs a hierarchical, tiered structure to ensure efficient incident handling and appropriate technical escalation. These roles are commonly divided into three tiers, each with distinct responsibilities that collectively support effective security monitoring and response. [6].

#### Tier 1 (SOC Analyst)

Tier 1 analysts act as the first line of defence and are primarily responsible for continuous monitoring and initial alert triage. Their role involves reviewing logs and alerts generated by security technologies to identify suspicious patterns and determine whether escalation is required. In a Splunk-based environment, Tier 1 analysts rely on dashboards and ad hoc searches across relevant indexes (such as those provided in BOTS v3) to identify unusual activity early. [11], [13].

#### Tier 2 (SOC Analyst / Incident Responder)

Tier 2 analysts conduct deeper investigations into incidents escalated from Tier 1. Their responsibilities include correlating activity across multiple data sources to validate incidents and determine scope and impact. In this investigation, this aligns with analysing AWS CloudTrail logs, S3 access logs, and endpoint/infrastructure telemetry to confirm misconfigurations and evidence of data exposure. [7], [8].

#### Tier 3 (SOC Engineer / Threat Hunter)

Tier 3 analysts focus on advanced security functions, including proactive threat hunting, root cause analysis, and detection engineering. Rather than responding to individual alerts, Tier 3 work improves overall detection capability by refining SIEM rules, validating use cases, and reducing false positives. In the context of this investigation, Tier 3 responsibilities are reflected conceptually through the identification of detection gaps and the consideration of how alerting could be engineered to detect high-risk AWS actions (e.g., S3 permission changes) more effectively. [9], [8].

### Incident Handling Lifecycle

The incident handling lifecycle provides a structured framework for managing security events. Guidance such as NIST SP 800-61 Rev. 2 describes a lifecycle that includes preparation, detection and analysis, containment/eradication/recovery, and post-incident activity ("lessons learned"). [12].

In the BOTS v3 investigation, detection is simulated through manual identification of suspicious activity within aggregated logs rather than real-time alerting. Analysis is supported by Splunk's ability to centralise and correlate diverse data sources, enabling reconstruction of event timelines and identification of security misconfigurations. While containment, eradication, and recovery actions are not executed within the dataset, a real SOC would respond by revoking compromised credentials, restricting public access to affected S3 buckets, and restoring systems to a secure state. [12].

The lessons learned phase is particularly significant, as it informs improvements to detection logic, alert thresholds, and preventative controls. Insights gained from this investigation highlight the importance of monitoring high-risk cloud actions and enforcing security best practices such as multi-factor authentication and least-privilege access. [4].

### Reflection

This investigation demonstrates the value of Splunk as a SIEM platform capable of providing visibility across cloud, endpoint, and system-level data. The correlation of AWS CloudTrail events with S3 access logs and host telemetry enables effective incident reconstruction. However, reliance on manual searching also highlights the importance of automation and alert engineering to reduce detection time and analyst workload. [9].

Additionally, the investigation reinforces that effective SOC operations depend not only on tooling but also on skilled analysts capable of interpreting complex logs within an organisational context. In a real SOC, improvements could include increased automation, tuned correlation rules to reduce false positives, and structured coverage mapping using frameworks such as MITRE ATT&CK to identify detection gaps. [8].

## Investigation and Analysis

### Q1 - IAM Users Accessing AWS Services

To determine which IAM users accessed AWS services within the environment, AWS CloudTrail logs were analysed using Splunk. The following search was used to aggregate API activity by IAM username:

```
index=* sourcetype=aws:cloudtrail | stats count by userIdentity.userName | sort -count
```

The results show that the IAM users `splunk_access`, `web_admin`, `bstoll`, and `btun` performed AWS API actions during the observed period. The presence of both service-style accounts (e.g., splunk_access) and named user accounts suggests a mix of automated access and direct administrative or user-driven interactions.

![Figure 1](ss/01/14_finding_IAM_usernames.png?raw=true)
Figure 1: CloudTrail aggregation showing IAM usernames and associated API activity counts (Q1).

From a SOC perspective, accounts such as web_admin warrant closer scrutiny due to their likely elevated privileges, while named user accounts should be reviewed for adherence to authentication and access control policies. This establishes a baseline for subsequent analysis, including MFA usage and identification of high-risk API actions. [2].

### Q2 - IAM Users Accessing AWS Services Without MFA

To identify IAM users accessing AWS services without multi-factor authentication (MFA), CloudTrail logs were filtered using the field `userIdentity.sessionContext.attributes.mfaAuthenticated`, which indicates whether the IAM/root user authenticated with MFA for the request. [3].

```
index=* sourcetype=aws:cloudtrail userIdentity.sessionContext.attributes.mfaAuthenticated="false" | stats count by userIdentity.userName | sort -count
```

The results show that the IAM users web_admin, bstoll, btun, and splunk_access accessed AWS services without MFA enabled. The high volume of activity by web_admin and bstoll without MFA represents a significant risk, as compromised credentials without MFA materially increase the likelihood of unauthorised access. While splunk_access may represent an integration/service account, named user accounts operating without MFA would typically be treated as high-severity findings in production environments. [4].

![Figure 2](ss/01/17_userIdentity_element.png?raw=true)
Figure 2: CloudTrail results showing AWS API activity performed without MFA (Q2).

From a SOC perspective, API activity performed without MFA increases the attack surface and should trigger immediate investigation, particularly for privileged accounts. In a real SOC, automated alerts would monitor userIdentity.sessionContext.attributes.mfaAuthenticated and escalate high-risk activity for Tier 2 validation and containment. [11].

### Q3 - Web Servers Processor Number

To identify the processor used on the web servers, hardware-related logs were analysed using the hardware sourcetype in Splunk. 

First, data presence was verified:

```
index=* sourcetype=hardware | head 5
```
Next, hardware data was summarised to identify relevant hosts:

```
index=* sourcetype=hardware | stats count by host | sort -count
```

This returned the following web server hosts:

- gacrux.i-06fea586f3d3c8ce8

- gacrux.i-09cbc261e84259b54

- gacrux.i-0cc93bade2b3cba63

![Figure 3](ss/02/19_web_servers.png?raw=true)
Figure 3: The web server hosts (Q3 context).

Processor information was then extracted (example shown for one host):

```
index=* sourcetype=hardware host=gacrux.i-06fea586f3d3c8ce8 | table host cpu
```

The results indicate that the web servers are running processors identified as Intel(R) Xeon(R) CPU `E5-2676` v3 @ 2.40GHz. From a SOC perspective, visibility into hardware characteristics supports asset inventory and can assist in incident response (e.g., identifying performance bottlenecks, validating expected infrastructure baselines, and scoping affected systems). [6].

![Figure 4](ss/02/20_cpu_type.png?raw=true)
Figure 4: Hardware telemetry showing web server host(s) and CPU information (Q3).

### Q4, Q5, Q6 - S3 Public Access Misconfiguration (API, User, Bucket)

To identify how public access was enabled for an S3 bucket, AWS CloudTrail logs were analysed for S3-related API activity. The following search was used to identify high-risk S3 actions:

```
index=* sourcetype=aws:cloudtrail eventSource=s3.amazonaws.com | stats count by eventName | sort -count
```

The API call PutBucketAcl was identified as a high-risk operation capable of modifying bucket permissions and enabling public access. [5]. 

![Figure 5](ss/03/22_noting_PutBucketAcl_highrisk.png?raw=true)
Figure 5: CloudTrail event frequency results showing S3 API calls including PutBucketAcl (Q4 context).

Further analysis was performed to determine the user and bucket associated with this action:

```
index=* sourcetype=aws:cloudtrail eventName="PutBucketAcl" | table _time eventID requestID userIdentity.userName eventSource requestParameters.bucketName requestParameters.x-amz-acl userAgent sourceIPAddress | sort _time
```

The results show that the IAM user `bstoll` executed the `PutBucketAcl` API call against the S3 bucket `frothlywebcode`. This action represents the point at which bucket access controls were modified, potentially enabling public access to bucket contents.

Note (for forensic traceability): The change was identified in CloudTrail with eventID: `ab45689d-69cd-41e7-8705-5350402cf7ac`.

![Figure 6](ss/03/24_username_bucket_eventID.png?raw=true)
Figure 6: CloudTrail details showing PutBucketAcl executed by bstoll on frothlywebcode with event metadata (Q4-Q6 evidence).

From a SOC perspective, unauthorised or inappropriate use of PutBucketAcl is a high-severity finding and would typically trigger immediate alerting and rapid investigation to assess exposure and potential misuse. [2].

### Q7 - Evidence of Public Object Access

Following the identification of the S3 bucket access misconfiguration, S3 access logs were analysed to determine whether objects within the bucket were accessed publicly. The following search identified access log entries referencing the bucket:

```
index=* sourcetype=aws:s3:accesslogs "frothlywebcode" | table _time host source sourcetype _raw | head 20
```

Analysis of the S3 access logs revealed successful unauthenticated access to an object named `OPEN_BUCKET_PLEASE_FIX.txt`. The access used the REST.GET.OBJECT operation and returned HTTP status 200, indicating the object was retrieved successfully.

![Figure 7](ss/04/26_GET_file.png?raw=true)
Figure 7: S3 access log entry showing REST.GET.OBJECT access to OPEN_BUCKET_PLEASE_FIX.txt with HTTP 200 (Q7).

From a SOC perspective, this confirms the misconfiguration resulted in real data exposure. In a production environment, unauthenticated object access would be treated as a high-severity incident, requiring immediate containment (restrict bucket permissions), impact assessment (identify exposed objects and access scope), and follow-up detection engineering. [12].

### Q8 - Endpoint Running a Different Windows Version

To identify endpoints running different Windows versions, host monitoring logs were analysed using the winhostmon sourcetype in Splunk. A field summary confirmed that the dataset contained two OS values:

```
index=* sourcetype=winhostmon | fieldsummary
```

![Figure 8](ss/05/28_finding_OS.png?raw=true)
Figure 8: winhostmon field summary showing distinct OS values (Q8 context).

The OS field contained two distinct values:

- Microsoft Windows 10 Pro

- Microsoft Windows 10 Enterprise

Next, OS values were compared by endpoint:

```
index=* sourcetype=winhostmon | stats count by host OS | sort OS
```

The analysis revealed that the endpoint `BSTOLL-L` was running Microsoft Windows 10 Enterprise, while other endpoints were running Microsoft Windows 10 Pro. From a SOC perspective, inconsistent endpoint baselines can introduce security and management challenges (e.g., policy drift, patching variance, and inconsistent hardening controls). [9].

![Figure 9](ss/05/30_BSTOLL-L.png?raw=true)
Figure 9: Endpoint OS comparison showing BSTOLL-L as the Windows 10 Enterprise outlier (Q8 evidence).

Hostname normalisation and FQDN derivation: Windows hostnames are case-insensitive and commonly normalised to lowercase in log analysis workflows. Within the dataset, organisational systems consistently use the froth.ly domain (e.g., splunk.froth.ly), supporting the derivation of the endpoint FQDN as `bstoll-l.froth.ly`.

## Conclusion

This investigation applied a SOC-aligned analytical approach to the BOTS v3 dataset to identify security risks, misconfigurations, and evidence of data exposure within a simulated enterprise environment. Using Splunk as a SIEM platform, cloud, endpoint, and infrastructure logs were correlated to reconstruct a realistic security incident involving AWS IAM weaknesses and an S3 bucket misconfiguration.

The analysis demonstrated that multiple IAM users accessed AWS services without multi-factor authentication, increasing the risk of credential compromise. Further investigation identified a high-risk PutBucketAcl API call that resulted in a publicly accessible S3 bucket. Subsequent S3 access logs confirmed unauthenticated access to an object within the bucket, validating that the misconfiguration led to real data exposure rather than a theoretical weakness.

Endpoint and infrastructure analysis further highlighted the importance of asset visibility and baseline consistency, with one endpoint running a different Windows edition compared to the rest of the environment. From a SOC perspective, such inconsistencies can introduce operational and security challenges if not monitored and managed.

Overall, this investigation reinforces the importance of continuous monitoring, strong authentication controls, and proactive detection engineering in modern SOC operations. While the BOTS v3 dataset simulates a controlled environment, the investigative techniques and lessons learned are transferable to real SOC workflows where early detection and effective incident response reduce organisational risk. [12].

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
