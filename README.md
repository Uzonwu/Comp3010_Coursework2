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

 

## References

The data set: https://github.com/splunk/botsv3?tab=readme-ov-file

Basta, A. et al. (2024) Open-Source Security Operations Center (SOC): A Complete Guide to Establishing, Managing, and Maintaining a Modern SOC,  IEEE Xplore. Newark, New Jersey: Wiley. Available at: https://ieeexplore-ieee-org.plymouth.idm.oclc.org/ (Accessed: 2025). (from primo, will split up by chapters used: introduction to soc analysis, soc metrics and performance measurement)

Nathans, D. (2015) Designing and building security operations center, EBook Central. Syngress. Available at: https://ebookcentral.proquest.com/ (Accessed: 2025). (from primo)

Abd Majid, M. and Zainol Ariffin, K.A. (2021) 'Model for successful development and implementation of Cyber Security Operations Centre (SOC)', PLOS ONE, 16(11). doi:10.1371/journal.pone.0260157. (from primo)

Forsberg, J. and Frantti, T. (2023) 'Technical performance metrics of a security operations center', Computers &amp; Security, 135, p. 103529. doi:10.1016/j.cose.2023.103529. (from primo)

Chamkar, S.A., Maleh, Y. and Gherabi, N. (2024) 'Security Operations Centers: Use case best practices, coverage, and gap analysis based on mitre adversarial tactics, techniques, and common knowledge', Journal of Cybersecurity and Privacy, 4(4), pp. 777-793. doi:10.3390/jcp4040036. (from primo)

Miloslavskaya, N. (2016) 'Security Operations Centers for Information Security Incident Management', 2016 IEEE 4th International Conference on Future Internet of Things and Cloud (FiCloud), pp. 131-136. doi:10.1109/ficloud.2016.26. (from primo)

Bejtlich, R. (2013) The practice of network security monitoring: Understanding incident detection and response, EBook Central. No Starch Press, Incorporated. Available at: https://ebookcentral.proquest.com/ (Accessed: 2025). (from primo)
