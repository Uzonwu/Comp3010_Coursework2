# Comp3010_Coursework2

## Introduction

### Security Operations Centre (SOC)

A Security Operations Centre (SOC) is a centralised function responsible for monitoring, detecting, and responding to security threats across an organisation's information systems. In the modern cybersecurity landscape, SOCs have evolved from physical locations where analysts manually monitored alerts into highly integrated operations that leverage log aggregation, advanced analytics, and automation to counter increasingly sophisticated cyber threats.

### Why SOCs Exist

SOCs exist to protect an organisation's critical assets from a wide range of threats, including unauthorised access, malware, and data breaches. They provide several essential capabilities that justify their role within modern enterprises:

- Centralised Defence: SOCs provide a unified approach to identifying, investigating, and responding to security incidents across diverse environments.
- Early Threat Detection and Response: By detecting suspicious activity at an early stage, SOCs reduce dwell time and limit potential damage to systems and data.
- Regulatory Compliance: Many organisations operate under strict regulatory frameworks such as GDPR and PCI DSS; SOCs support compliance through continuous monitoring and incident documentation.
- Managing Complexity: The increasing adoption of cloud computing, Internet of Things (IoT), and machine learning technologies has expanded the attack surface, requiring dedicated teams and specialised tooling to maintain visibility and control.

### What SOCs Do

At a high level, SOC operations revolve around several interrelated activities:

1. Continuous Monitoring: SOCs operate on a 24/7 basis, collecting and reviewing logs, network traffic, and system events to identify indicators of compromise.
2. Detection and Analysis: Collected data is analysed to determine whether suspicious activity represents a genuine security incident. Analysts correlate events across multiple data sources and apply threat intelligence to identify known attack techniques.
3. Incident Response: Once an incident is confirmed, the SOC coordinates containment, eradication, and recovery activities, such as isolating affected systems, removing malicious artefacts, and restoring normal operations.
4. Analysis and Continuous Improvement: Lessons learned from incidents are used to refine detection rules, improve alerting, and strengthen the organisation's overall security posture. Tools such as Security Information and Event Management (SIEM) and Endpoint Detection and Response (EDR) platforms play a critical role in supporting these activities.

To perform these functions effectively, SOCs typically operate using a tiered structure that includes SOC analysts, incident responders, security engineers, and SOC managers, each with clearly defined responsibilities.

### BOTS v3 Overview

Boss of the SOC version 3 (BOTS v3) is a publicly available security dataset and training exercise developed by Splunk to simulate a realistic enterprise security incident. The dataset represents a fictional organisation, Frothly, a brewing company that operates a hybrid IT environment consisting of on-premises systems and cloud-based infrastructure hosted on Amazon Web Services (AWS).

BOTS v3 contains a large collection of pre-indexed logs covering multiple domains, including AWS CloudTrail logs, S3 access logs, Windows endpoint logs, and hardware and system monitoring data. These logs are designed to replicate the type and volume of telemetry typically analysed by SOC analysts during real-world investigations.

The purpose of the BOTS v3 exercise is to develop practical skills in log analysis, incident investigation, and threat detection using Splunk's Search Processing Language (SPL). By working through guided investigative questions, analysts are required to identify malicious activity, misconfigurations, and security weaknesses while applying SOC-aligned investigative methodologies.

### Investigation Objectives

The primary objectives of this investigation are to:

- Analyse AWS CloudTrail logs to identify IAM user activity and potential authentication weaknesses.
- Investigate misconfigurations within AWS services, including publicly accessible S3 buckets.
- Correlate cloud, endpoint, and system-level logs to reconstruct security-relevant events.
- Demonstrate the application of SOC investigative workflows using Splunk SPL.
- Reflect on SOC roles, incident handling processes, and opportunities for detection and response improvement.

### Scope and Assumptions

The scope of this assignment is limited to the data provided within the BOTS v3 dataset. No live systems, external threat intelligence feeds, or real-time alerting mechanisms were used as part of the analysis. The investigation was conducted using a standalone Splunk Enterprise deployment to simulate a simplified SOC environment.

It is assumed that the logs within the dataset are complete, accurately timestamped, and representative of activity within the fictional Frothly environment. Any identified security incidents or misconfigurations are assumed to have occurred solely within the context of the dataset and do not reflect real-world organisational systems.

The focus of this assignment is on log analysis and incident reconstruction rather than the implementation of remediation actions, which are discussed conceptually in alignment with SOC best practices.

## SOC Roles and Incident Handling Reflection

### SOC Roles (Tiered Model)

A SOC typically employs a hierarchical, tiered structure to ensure efficient incident handling and appropriate technical escalation. These roles are commonly divided into three tiers, each with distinct responsibilities that collectively support effective security monitoring and response.

#### Tier 1 (SOC Analyst)
Tier 1 analysts act as the first line of defence and are primarily responsible for continuous monitoring and initial alert triage. Their role involves reviewing logs and alerts generated by security technologies to identify known attack patterns and determine whether further investigation is required. In a Splunk-based environment, Tier 1 analysts rely heavily on dashboards and ad hoc searches across relevant indexes, such as those provided in the BOTS v3 dataset, to identify unusual activity at an early stage. Within the context of this investigation, Tier 1 responsibilities are reflected in the identification of suspicious AWS API activity through preliminary CloudTrail log analysis.

#### Tier 2 (SOC Analyst / Incident Responder)
Tier 2 analysts conduct deeper investigations into incidents escalated from Tier 1. Their responsibilities include correlating events across multiple data sources to validate incidents and determine scope and impact. In the BOTS v3 investigation, this aligns with analysing AWS CloudTrail logs, S3 access logs, and endpoint telemetry to confirm misconfigurations and unauthorised activity. Tasks such as reviewing IAM actions, identifying publicly accessible S3 buckets, and correlating cloud and endpoint data are representative of Tier 2 investigative responsibilities.

#### Tier 3 (SOC Engineer / Threat Hunter) 
Tier 3 analysts focus on advanced security functions, including proactive threat hunting, root cause analysis, and detection engineering. Rather than responding to individual alerts, Tier 3 activities aim to improve overall detection capability by refining SIEM rules, validating use cases, and reducing false positives. In the context of BOTS v3, Tier 3 responsibilities are reflected conceptually through the identification of detection gaps and the consideration of how alerts could be engineered to identify high-risk AWS actions more effectively in a production SOC environment.

### Incident Handling Lifecycle

The incident handling lifecycle provides a structured framework for managing security events, commonly encompassing detection, analysis, containment, eradication, recovery, and lessons learned.

In the BOTS v3 investigation, detection is simulated through manual identification of suspicious activity within aggregated logs rather than real-time alerting. Analysis is supported by Splunk's ability to centralise and correlate diverse data sources, enabling reconstruction of event timelines and identification of security misconfigurations. While containment, eradication, and recovery actions are not directly executed within the dataset, a real SOC would respond by revoking compromised credentials, restricting public access to affected S3 buckets, and restoring systems to a secure state. 

The lessons learned phase is particularly significant, as it informs improvements to detection logic, alert thresholds, and preventative controls. Insights gained from this investigation highlight the importance of monitoring high-risk cloud actions and enforcing security best practices such as multi-factor authentication and least-privilege access.

### Reflection

This investigation demonstrates the value of Splunk as a centralised SIEM platform capable of providing comprehensive visibility across cloud, endpoint, and system-level data. The ability to correlate AWS CloudTrail events with S3 access logs and endpoint telemetry enables effective incident reconstruction. However, the reliance on manual analysis within the BOTS v3 dataset highlights the limitations of reactive investigations and underscores the importance of automated alerting to reduce detection time and analyst workload.

Additionally, the investigation reinforces that effective SOC operations depend not only on tooling but also on skilled analysts capable of interpreting complex data within an organisational context. In a real-world SOC, further improvements could be achieved through increased automation, the application of machine learning to reduce false positives, and the integration of frameworks such as MITRE ATT&CK to systematically assess and enhance detection coverage.

## Investigation and Analysis

### Q1
To find out which IAM users accessed AWS services within the environment, I analysed AWS CloudTrail logs using Splunk. The following search was used to aggregate API activity by IAM username:

```
index=* sourcetype=aws:cloudtrail | stats count by userIdentity.userName
```

The results show that the IAM users `bstoll`, `btun`, `splunk_access`, and  `web_admin` performed AWS API actions during the observed period. The presence of both service-style accounts (e.g. splunk_access) and named user accounts suggests a mix of automated access and direct administrative or user-driven interactions.

From a SOC perspective, accounts such as web_admin warrant a closer look due to their likely elevated privileges, while named user accounts should be reviewed for adherence to authentication and access control policies. This initial identification step establishes a baseline for further analysis, including the assessment of multi-factor authentication usage and potentially risky actions performed by these users.

### Q2
To find which IAM users accessed AWS services without multi-factor authentication (MFA), I analysed AWS CloudTrail logs using Splunk. MFA usage is recorded within the CloudTrail field `userIdentity.sessionContext.attributes.mfaAuthenticated`. The following search was used to identify API activity where MFA was not enabled:

```
index=* sourcetype=aws:cloudtrail userIdentity.sessionContext.attributes mfaAuthenticated="false" | stats count by userIdentity.userName | sort -count
```

The results show that the IAM users web_admin, bstoll, btun, and splunk_access accessed AWS services without MFA enabled. I noted that web_admin and bstoll generated a high volume of API activity without MFA, representing a significant security risk due to the increased likelihood of credential compromise. While splunk_access may represent a service or integration account, named user accounts operating without MFA would be considered high-severity findings in a production environment.

From a SOC perspective, API activity performed without MFA significantly increases the attack surface and should trigger immediate investigation, particularly for accounts with administrative privileges. In a real-world SOC, automated alerts would be configured to monitor the userIdentity.sessionContext.attributes.mfaAuthenticated field and escalate any high-risk activity to Tier 2 analysts for further analysis and containment.

### Q3
To identify the processor used on the web servers, hardware-related logs were analysed using the hardware sourcetype in Splunk. The following searchs were used to extract CPU information for hosts identified as web servers:

First I verified that data exists:

```
index=* sourcetype=hardware | head 5
```
Which returned values confirming it. Then I ran the next search to identify which hosts were web servers:

```
index=* sourcetype=hardware | stats count by host
```

Which returned:

gacrux.i-06fea586f3d3c8ce8

gacrux.i-09cbc261e84259b54

gacrux.i-0cc93bade2b3cba63

So taking the first in the list, I extracted its processor information, via viewing event

```
index=* sourcetype=hardware host=gacrux.i-06fea586f3d3c8ce8 | table host cpu
```

Viewing the events of the returned host, I was able to see the CPU_TYPE.The results indicate that the web servers are running on processors identified as Intel(R) Xeon(R) CPU `E5-2676` v3 @ 2.40GHz. From a SOC perspective, visibility into hardware characteristics supports asset inventory management and can assist in identifying performance bottlenecks or anomalous behaviour during incident response.

### Q4, Q5, Q6
To identify how public access was enabled for an S3 bucket, AWS CloudTrail logs were analysed for S3-related API activity. The following search was used to identify high-risk S3 actions:

```
index=* sourcetype=aws:cloudtrail eventSource=s3.amazonaws.com | stats count by eventName | sort -count
```

Among the returned API calls, the PutBucketAcl action was identified as a high-risk operation capable of modifying bucket permissions and enabling public access. Further analysis was conducted to determine the context of this action:

```
index=* sourcetype=aws:cloudtrail eventName="PutBucketAcl" | table _time eventID userIdentity.userName requestParameters.bucketName | sort _time
```

The results show that the IAM user bstoll executed the PutBucketAcl API call against the S3 bucket named frothlywebcode. This action represents the point at which the bucket's access controls were modified, potentially enabling public access to its contents.

Note: The S3 bucket misconfiguration was caused by a `PutBucketAcl` API call executed by the IAM user `bstoll` against the `frothlywebcode` bucket. This action was identified in AWS CloudTrail logs (eventID: `ab45689d-69cd-41e7-8705-5350402cf7ac`) and represents the point at which bucket permissions were modified.

From a SOC perspective, unauthorised or inappropriate use of the PutBucketAcl API call is considered a high-severity security finding. In a production environment, such activity would typically trigger immediate alerts and prompt further investigation to assess data exposure and potential misuse.

### Q7
Following the identification of the S3 bucket access misconfiguration, S3 access logs were analysed to determine whether any objects within the bucket were accessed publicly. The following search was used to identify object-level access to the affected bucket:

```
index=* sourcetype=aws:s3:accesslogs "frothlywebcode" | table _time host source sourcetype _raw | head 20
```

Analysing S3 access logs revealed successful public access to an object named `OPEN_BUCKET_PLEASE_FIX.txt`. The access was performed using the REST.GET.OBJECT operation and returned an HTTP 200 status code, indicating that the object was retrieved successfully.

From a SOC perspective, this confirms that the bucket misconfiguration resulted in real data exposure. In a production environment, unauthenticated access to objects within an S3 bucket would be treated as a high-severity incident, triggering immediate containment actions such as restricting bucket permissions and conducting an impact assessment to determine the extent of data exposure.

### Q8
To identify endpoints running different versions of Windows, host monitoring logs were analysed using the winhostmon sourcetype in Splunk. The following search was used to compare operating system versions across endpoints:

```
index=* sourcetype=winhostmon | fieldsummary
```

field	count	distinct_count	is_exact	max	mean	min	numeric_count	stdev	values
OS	204	2	1	 	 	 	0	 	[{"value":"Microsoft Windows 10 Pro","count":174},{"value":"Microsoft Windows 10 Enterprise","count":30}]

```
index=* sourcetype=winhostmon | stats count by host OS | sort OS
```

The analysis revealed that the endpoint `BSTOLL-L` was running Microsoft Windows 10 Enterprise, while all other endpoints were running Microsoft Windows 10 Pro. From a SOC perspective, inconsistent operating system versions can introduce security and management challenges, as differences in licensing, configuration, and patching may affect an organisation's security posture.

Windows hostnames are case-insensitive and are commonly normalised to lowercase in log analysis workflows. Within the BOTS v3 dataset, organisational systems consistently use the froth.ly domain, as observed across multiple infrastructure and service logs.

Based on standard enterprise naming conventions, the fully qualified domain name (FQDN) for this endpoint can therefore be derived as `bstoll-l.froth.ly`, which aligns with the format expected by the guided question.

## Conclusion

This investigation applied a SOC-aligned analytical approach to the BOTS v3 dataset in order to identify security risks, misconfigurations, and evidence of data exposure within a simulated enterprise environment. Using Splunk as a centralised SIEM platform, cloud, endpoint, and infrastructure logs were correlated to reconstruct a realistic security incident involving AWS Identity and Access Management weaknesses and S3 bucket misconfiguration.

The analysis demonstrated that multiple IAM users accessed AWS services without multi-factor authentication, increasing the risk of credential compromise. Further investigation identified a high-risk PutBucketAcl API call that resulted in a publicly accessible S3 bucket. Subsequent S3 access logs confirmed unauthenticated access to an object within the bucket, validating that the misconfiguration led to real data exposure rather than a theoretical weakness.

Endpoint and infrastructure analysis further highlighted the importance of asset visibility and baseline consistency, with one endpoint running a different Windows edition compared to the rest of the environment. From a SOC perspective, such inconsistencies can introduce operational and security challenges if not properly monitored.

Overall, this investigation reinforces the importance of continuous monitoring, strong authentication controls, and proactive detection engineering in modern SOC operations. While the BOTS v3 dataset simulates a controlled environment, the investigative techniques and lessons learned are directly transferable to real-world SOC workflows, where early detection and effective incident response are critical to minimising organisational risk.

## References

The data set: https://github.com/splunk/botsv3?tab=readme-ov-file

Basta, A. et al. (2024) Open-Source Security Operations Center (SOC): A Complete Guide to Establishing, Managing, and Maintaining a Modern SOC,  IEEE Xplore. Newark, New Jersey: Wiley. Available at: https://ieeexplore-ieee-org.plymouth.idm.oclc.org/ (Accessed: 2025). (from primo, will split up by chapters used: introduction to soc analysis, soc metrics and performance measurement)

Nathans, D. (2015) Designing and building security operations center, EBook Central. Syngress. Available at: https://ebookcentral.proquest.com/ (Accessed: 2025). (from primo)

Abd Majid, M. and Zainol Ariffin, K.A. (2021) 'Model for successful development and implementation of Cyber Security Operations Centre (SOC)', PLOS ONE, 16(11). doi:10.1371/journal.pone.0260157. (from primo)

Forsberg, J. and Frantti, T. (2023) 'Technical performance metrics of a security operations center', Computers &amp; Security, 135, p. 103529. doi:10.1016/j.cose.2023.103529. (from primo)

Chamkar, S.A., Maleh, Y. and Gherabi, N. (2024) 'Security Operations Centers: Use case best practices, coverage, and gap analysis based on mitre adversarial tactics, techniques, and common knowledge', Journal of Cybersecurity and Privacy, 4(4), pp. 777-793. doi:10.3390/jcp4040036. (from primo)

Miloslavskaya, N. (2016) 'Security Operations Centers for Information Security Incident Management', 2016 IEEE 4th International Conference on Future Internet of Things and Cloud (FiCloud), pp. 131-136. doi:10.1109/ficloud.2016.26. (from primo)

Bejtlich, R. (2013) The practice of network security monitoring: Understanding incident detection and response, EBook Central. No Starch Press, Incorporated. Available at: https://ebookcentral.proquest.com/ (Accessed: 2025). (from primo) 
