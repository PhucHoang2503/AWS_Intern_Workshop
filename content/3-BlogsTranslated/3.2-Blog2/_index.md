---
title: "Blog 2"
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

# Enhance resiliency and data protection for SAP HANA high-availability deployments with AWS Backup for SAP HANA

by Vijay Sitaram, Amit Mohanty, Archana Kuppuswamy, and Khaliluddin Siddiqui | on 01 APR 2025 | in **AWS Backup**, **AWS Support**, **Best Practices**, **SAP on AWS**, **Technical How-to**, **Thought Leadership**

## Introduction

SAP HANA databases are often deployed in High Availability (HA) configurations to maintain continuous operations on AWS. SAP HANA databases need both HA configuration and comprehensive backup strategies to protect data and support complete system recovery. Despite the benefits of SAP HA deployments, SAP HANA databases remain vulnerable to various risks, including cluster-wide failures, logical errors, data corruption, ransomware attacks, and compliance issues.  
HA alone cannot address scenarios such as point-in-time recovery needs, system refreshes for test/development environments, or long-term data retention requirements. Organizations need a holistic strategy for complete data protection, regulatory compliance, and operational resilience.

For RISE with SAP on AWS deployments, database backups are managed through AWS Backint Agent for SAP HANA. This is the standard backup solution that integrates with SAP HANA databases. The backup process is fully managed by SAP as part of the RISE offering, but customers can monitor backup status through SAP-provided tools and AWS Backup console. For customers running SAP natively on AWS such as SAP ERP or S/4HANA, AWS Backup for SAP HANA is a great option.

In this blog, you will learn about how organizations are implementing robust backup strategies for their SAP HANA databases with AWS Backup in HA deployment. The blog also covers the data protection and Well-Architected Framework for SAP lens Reliability pillar and Operational best practices.

## Data Protection and Well-Architected Framework for SAP Lens

The **Well-Architected Framework SAP Lens** emphasizes database backups primarily within its Reliability pillar. This pillar is provided expert guidance for SAP workloads to perform their intended functions consistently and correctly, even when faced with failures or disruptions.

Within the Reliability pillar, database backups are highlighted as a key component of several critical areas:

1. **Data Protection**: The framework provides best practices for implementing robust backup and recovery strategies for SAP databases and other critical data. This provides guidance for organizations to prepare for data recovery in case of system failures, data corruption, or other unforeseen events.
2. **Disaster Recovery**: Backups play a vital role in disaster recovery planning. The SAP Lens offers guidance on setting up disaster recovery mechanisms that leverage backups to restore operations in case of major incidents.
3. **High Availability**: While not directly about backups, high availability strategies complement backup procedures by ensuring system uptime and minimizing the need for recovery from backups.
4. **Data Retention**: The framework provides recommendations for long-term data storage and archiving, which often involve backup strategies to meet regulatory requirements and business needs.

The Reliability pillar emphasizes several key aspects of database backup strategies:

- Implementing regular and consistent backup schedules
- Thoroughly testing recovery procedures for effectiveness
- Automating backup solutions to reduce human error and maintain consistency
- Maintaining data integrity and security throughout the backup and recovery processes
- Aligning backup strategies with the organization’s **Recovery Time Objectives (RTO)** and **Recovery Point Objectives (RPO)**

By focusing on these areas, the **SAP Lens of the Well-Architected Framework** provides comprehensive guidance such that SAP workloads on AWS are resilient and recoverable. It helps organizations implement effective backup strategies that support business continuity and minimize data loss risks. This approach delivers rapid restoration of critical SAP systems during failures, preserving the reliability and availability essential for businesses’ core operations.

## Primer on AWS Backint Agent for SAP HANA

**AWS Backint for SAP HANA** offers native integration with SAP HANA’s backup capabilities, ensuring application-consistent backups. The solution utilizes Amazon Simple Storage Service (S3) for storage, offering durability, scalability, and cost-effectiveness. The Backint agent supports incremental backups, reducing storage costs and backup windows. It also offers encryption for backups both in transit and at rest, enhancing security.The solution is scriptable, allowing automation and integration with existing backup workflows. It supports Point-In-Time Recovery (PITR), crucial for addressing data corruption or human errors. Backint scales with large SAP HANA databases, minimizing impact on performance during backup operations. Cost-effectiveness is a key feature, leveraging S3 storage class and life cycle policy to transition to cheaper storage classes for long-term retention.The solution benefits from the high durability and availability of Amazon S3 for backup storage.

These features make AWS Backint Agent a great choice for customers seeking a reliable, SAP-native backup solution that leverages AWS storage capabilities, particularly those preferring direct integration with SAP tools and having simpler backup requirements. To learn more about how to configure AWS Backint agent for SAP with Amazon S3, please visit Backup and restore SAP HANA workloads to Amazon S3.

## AWS Backup for SAP HANA on Amazon Elastic Cloud Compute (EC2)

**AWS Backup for SAP HANA on Amazon EC2** delivers a managed, streamlined experience for SAP HANA database backup and restore operations. It enhances the customer experience through its intuitive AWS Backup console, extends data protection and simplifies management across multiple AWS services, including Amazon EC2, Amazon Elastic Block Store (EBS), and Amazon Elastic File System (EFS). The continuous backup features offer substantial cost savings, particularly for users previously limited to full backups with AWS Backint Agent. AWS Backup supports comprehensive audit and compliance capabilities for backups with Backup Vault Lock and Legal Holds. It supports automated cross-region cross-account backup copying, enhancing disaster recovery and data redundancy. This full-managed service optimizes SAP HANA data management, improving reliability and operational efficiency within the AWS ecosystem.

To learn more about how to automate and simplify SAP HANA backups with AWS Backup, please read the blog [Automate and Simplify SAP HANA Backups with AWS Backup](https://aws.amazon.com/blogs/awsforsap/general-availability-announcement-aws-backup-for-sap-hana-database/). For documentation on how to configure AWS Backup for SAP HANA, please read the [document](https://docs.aws.amazon.com/sap/latest/sap-hana/aws-backint-agent-backup.html).

## Recommendations for SAP HA deployment

AWS Backup for SAP HANA supports both single-node and HA deployments. In HA deployment, it automatically identifies the active node for running backups as shown in Figure-3 below. Customers use Pacemaker cluster software to manage the SAP HANA HA deployment.
![Blog-2](/images/3-Blog/Blog-2/Architecture-i1.png)
Figure-1: AWS Backup for SAP HANA HA deployment

Below are the steps to configure AWS Backup for SAP HANA on Amazon EC2:

1. Save HANA Credentials in Secrets Manager
2. Check IAM permissions and tags for EC2 instances, and Secrets key
3. Register one of the HANA instances in HA deployment with SSM for SAP (ssm-sap)
4. Install Backint for AWS Backup using SSM document (AWSSAP-InstallBackintForAWSBackup)
5. OPT in SAP HANA on Amazon EC2 in AWS Backup console settings
6. Create a backup plan.

To initiate an on-demand backup of SAP HANA database in HA deployment from AWS Backup console. Scheduled backups are configured in Backup plan by defining a backup policy. Following is a high-level list of activities:

1. AWS Backup calls SSM for SAP backup APIs to initiate backup.
2. AWS SSM for SAP calls Backint via a SSM run command.
3. Backup is stored in AWS Vault and backup status is recorded.

Below steps are required before and after database restore in SAP HA deployment when managed by Pacemaker cluster manager. To learn more about additional steps to include when restoring a HA system of SAP HANA, please read the documentation [SAP HANA HA restore](https://docs.aws.amazon.com/aws-backup/latest/devguide/saphana-restore.html#saphanarestoreha). In addition, you will find more guidance on setting up Pacemaker cluster in [SAP HANA on AWS: High Availability Configuration Guide for SLES and RHEL](https://docs.aws.amazon.com/sap/latest/sap-hana/sap-hana-on-aws-ha-configuration.html).

**Before restore**: SAP database cluster and the nodes (primary database node and standby database node) must be placed in maintenance mode, and SAP HANA is stopped on both nodes. After restoring system database, start SAP HANA on primary database before proceeding to restore tenant database. During the database restore operation, the SAP database cluster and the cluster nodes remain in maintenance mode.

To initiate SAP HANA database restore, login to AWS Backup console and initiate a restore by using a full backup or a continuous backup recovery point ID. Following is a high-level list of activities:

1. AWS Backup calls SSM for SAP backup APIs to initiate restore.
2. AWS SSM for SAP calls Backint via a SSM run command.
3. Database is restored from AWS Vault to the primary database node.

**After restore**: Following a successful restore of system and tenant database, stop SAP HANA in secondary database node. Register SAP HANA System Replication (HSR) in primary database node followed by secondary database node. Proceed to start SAP HANA in secondary database node, and take SAP database cluster nodes and global cluster out of maintenance mode.

## Simplify data protection management and operations

AWS Backup for SAP HANA is a fully managed AWS service that provides robust set of features to streamlines backup processes, enhance recovery, and improve disaster recovery capabilities for SAP HANA single-node and HA deployments.

| **Feature**                           | **Benefits**                                                                                                                                                                                                                                                                                                                                                                           |
| ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Centralized Management                | Single dashboard for managing backup and restores across multiple SAP instances simplifies recovery operations in distributed architectures.                                                                                                                                                                                                                                           |
| Cost optimized storage                | Lifecycle policy management features allow for cost-effective long-term retention of backups without compromising recovery capabilities.                                                                                                                                                                                                                                               |
| Cross-Region and Cross-Account copy   | Native integration with SAP HANA creates application-consistent backups, critical for maintaining data integrity in complex SAP environments. Cross-region and cross-account backup capabilities enable geographically dispersed recovery options, enhancing disaster recovery strategies and ransomware protection.                                                                   |
| Compliance and Audit                  | Helps meet regulatory requirements with audit logs and retention policies, crucial for many SAP deployments. Vault Lock is a mechanism that allows you to enforce retention policies and prevent deletion or modification of backups stored in an AWS Backup vault. Supports placing legal holds on backups to prevent deletion regardless of retention settings.                      |
| Monitoring and alerting               | Default AWS Backup console Job dashboard to monitor backup and restore job status, and recovery point id status. CloudWatch metrics and alarms to monitor failed backup, restore and recovery points status.                                                                                                                                                                           |
| Point-In-Time Recovery (PITR)         | Automated and scheduled PITR continuous backups to maintain recovery point IDs. PITR allows for quick SAP HANA database restore to a specific timestamp within one second granularity, minimizing downtime.                                                                                                                                                                            |
| Policy based automation               | Reduces human error in backup and recovery processes through automation and predefined policies.                                                                                                                                                                                                                                                                                       |
| Resource tagging                      | Supports up to 50 tags for SAP HANA backups, and AWS service such as Amazon EC2 Amazon Machine Image (AMI) backups, Amazon EBS volume snapshots and Amazon EFS filesystem snapshots.                                                                                                                                                                                                   |
| Simplified Recovery management        | Recovery Time Objective (RTO): The combination of continuous backup (full and incremental) and database logs with an automated processes significantly reduces the time to recover SAP systems. <br> Recovery Point Objective (RPO): Automated scheduled continuous backups allow for more recent recovery points and logs, minimizing potential data loss up to 1 second granularity. |
| Scalability                           | Easily scales to handle large SAP HANA databases without compromising recovery speed or reliability.                                                                                                                                                                                                                                                                                   |
| Scheduling                            | Scheduled from AWS Backup console for hourly, daily, weekly and custom cron expressions.                                                                                                                                                                                                                                                                                               |
| Security                              | Built-in encryption with KMS and IAM integration provides secure storage and recovery processes, maintaining data integrity and access control.                                                                                                                                                                                                                                        |
| Support for additional SAP components | Seamless integration with other AWS services enables advanced recovery scenarios and automation such as Amazon EC2, Amazon EBS, and Amazon EFS.                                                                                                                                                                                                                                        |

AWS Backup for SAP HANA and AWS Backint Agent offer distinct approaches to backing up SAP HANA databases. Backint is SAP HANA-specific, integrating seamlessly with the SAP ecosystem and using SAP tools for management and recovery. In contrast, AWS Backup for SAP HANA is a more versatile service, providing backup capabilities across multiple AWS services, including native SAP HANA. It offers greater flexibility in storage and lifecycle options, managed through the AWS console.

Below is a side-by-side comparison of AWS Backint Agent and AWS Backup for SAP HANA:

| **Feature**          |                                                                      **AWS Backint Agent for SAP HANA** |                                                                                                                      **AWS Backup for SAP HANA** |
| -------------------- | ------------------------------------------------------------------------------------------------------: | -----------------------------------------------------------------------------------------------------------------------------------------------: |
| Automation           |                                                                        SAP HANA’s scheduling mechanisms |                                                                                                          Backup plan configured with Backup rule |
| Backup Types         |                                                                Full, incremental, differential and logs |                                                                                                                       Full, incremental and logs |
| Catalog management   |                                                                       Stored in SAP HANA backup catalog |                                                                                                                     Stored in AWS Backup catalog |
| Compliance           |                                       Both support audit logs and retention policies such as legal hold |
| Disaster Recovery    |                                                                Amazon S3 Cross-Region Replication (CRR) |                     AWS Backup copying backups across regions and across accounts to same or different AWS Vault with same or different KMS key. |
| End-user tool        |                                 Backup and recovery managed for SAP HANA database from SAP HANA Studio. | Backup and recovery management from AWS Backup console for SAP HANA and additional SAP components such as Amazon EC2, Amazon EBS and Amazon EFS. |
| Encryption           |                                                                 Amazon S3 bucket server-side encryption |                    SAP HANA database backups are always encrypted. AWS Key Management Service (KMS) encryption is configured in AWS Backup Vault |
| Granularity          |                                                                   File-level and database-level backups |                                            Database-level backups, and backups for SAP components such as Amazon EC2, Amazon EBS, and Amazon EFS |
| Lifecycle management |                                                                              Amazon S3 lifecycle policy |                                                                                                                AWS Backup Vault lifecycle policy |
| Performance          |                                                       Optimized for SAP HANA-specific backup operations |                                                  Optimized for SAP HANA backup operations, and AWS services (Amazon EC2, Amazon EBS, Amazon EFS) |
| Recovery             |                                                    Full database and PITR recovery from SAP HANA Studio |                                                                                          Full database and PITR recovery from AWS Backup console |
| Scalability          |                                                             Designed specifically for SAP HANA database |                                                    Designed for SAP HANA database and AWS services such as Amazon EC2, Amazon EBS and Amazon EFS |
| Storage              |                                         Backups stored directly in Amazon S3 bucket managed by customer |                                                                                    Backup stored directly in AWS Backup Vault managed by service |
| Security             | Amazon S3 Object Lock using Write-Once-Read-Many (WORM) model. Supports IAM file-grained access control |                           AWS Backup Vault Lock for additional security and control over backup images. Supports IAM file-grained access control |

## Operational best practices

While AWS Backint Agent is deeply integrated with SAP, AWS Backup extends this capability and integrates more broadly with AWS services. AWS Backup offering wider cross-service functionality and Backint providing specialized SAP-centric features makes it suitable option for SAP HANA backup strategy. Below are operational best practices for AWS Backup for SAP HANA on Amazon EC2.

**Discovery and registration**: AWS Backup for SAP HANA necessitates public endpoint access for several AWS services, including SSM for SAP (SSM4SAP), Systems Manager (SSM), Secrets Manager, and AWS Backup. To enable smooth operation, configure firewall to allow access for both SAP HANA nodes to permit service and storage endpoint access. The AWS SSM Agent must be installed and running on EC2 instances hosting SAP HANA database, with the correct IAM roles attached. It’s required to meet these prerequisites on all HANA nodes to facilitate **registration of SAP HANA database with AWS SSM4SAP** and run successful backup and restore operations through AWS Backup.

**Cost-optimized storage**: Optimize costs and streamline administration with AWS Backup Vault’s **lifecycle and storage tiers** for SAP HANA environments. Implement a comprehensive tiered Backup Plan that incorporates both full database backups and PITR backups aligned with your organization’s retention policy. This strategy enables robust data protection while effectively managing expenses. For full backups, leverage backup lifecycle policies to automatically transition older backups to cost-effective cold storage solutions like Amazon S3 Glacier. This approach significantly reduces long-term storage costs without compromising data accessibility. While storage tiering isn’t supported for PITR (continuous backups), PITR will determine type of database backup (full or incremental) intelligently based on volume of changes written to database, and combined with lifecycle policy optimizes cost of storage.

**Security, Governance and Compliance**: AWS Backup **Vault Lock** establishes strong governance frameworks, aligning data protection with stringent compliance requirements. The Vault Lock’s compliance and governance modes enable organizations to meet industry-specific regulations effectively. Legal hold functionality prevents deletion of backups under review, ensuring data integrity during audits or legal proceedings. Implement AWS Backup Vault secure **encrypted vaults** and immutable backups. This multi-layered approach safeguards SAP data against unauthorized access, ransomware attacks, and both internal and external threats. By default, SAP HANA backups are encrypted, and AWS Backup Vault mandates the use of AWS Key Management Service (KMS) for encrypting backup storage, ensuring data confidentiality at rest.

**Disaster Recovery (DR) with cross-account and cross-region copy**: This multi-region backup strategy significantly enhances business continuity and resilience against regional outages. The **cross-region cross-account** functionality allows backups to be automatically copied to different AWS regions and account, ensuring data availability even if an entire region becomes unavailable. Cross-account copying adds another layer of protection by storing backups in separate AWS accounts, isolating them from potential security breaches in the primary account. To learn how to set-up cross-region and cross-account copy, please refer to this [blog](https://aws.amazon.com/blogs/awsforsap/perform-cross-region-cross-account-backup-and-restore-of-sap-hana-database-using-aws-backup-for-sap/).

**Performance tuning**: It is recommended to right-size HANA database EC2 instance and EBS volumes to achieve optimal performance for day-to-day operations and backups. For SAP-certified memory optimized EC2 instances, take into account **EBS specifications** for maximum bandwidth, maximum throughput and maximum IOPS for instance type. This is helpful in understanding the limits for EC2 instance when tuning individual EBS volumes for SAP HANA data, and logs. This has direct impact on performance of any workload, including backups. To measure EBS metrics for individual volumes and aggregated metrics at instance level, please refer to **AWSSupport-CalculateEBSPerformanceMetrics** runbook which helps diagnose Amazon Elastic Block Store (Amazon EBS) performance issues by calculating and publishing performance metrics to an Amazon CloudWatch dashboard.

![Blog-2](/images/3-Blog/Blog-2/SAW-Metrics-i2.png)
Figure-2: AWSSupport-CalculateEBSPerformanceMetrics runbook Amazon CloudWatch Dashboard

Carefully review and adjust these parameters associated with SAP HANA and AWS Backint Agent so that they meet performance and recovery time objectives. The configuration parameters to fine-tune SAP HANA are stored in /hana/shared/$SID/global/hdb/custom/config/global.ini, and for AWS Backint Agent in /hana/shared/aws-backint-agent/aws-backint-agent-config.yaml. These parameters should be adjusted based on system resources (CPU, memory, network capacity), database size and workload characteristics, Backup window requirements, and available network bandwidth. To learn more about tuning performance of SAP HANA backup and restore, please refer to [Install and configure AWS Backint Agent for SAP HANA – Performance tuning](https://docs.aws.amazon.com/sap/latest/sap-hana/aws-backint-agent-s3-installing-configuring.html#aws-backint-agent-performance-tuning) and [Install and configure AWS Backint Agent for SAP HANA – Backint-related SAP HANA parameters](https://docs.aws.amazon.com/sap/latest/sap-hana/aws-backint-agent-s3-installing-configuring.html#aws-backint-agent-sap-hana-parameters).

Parameter configuration for SAP HANA Backup and Restore optimization

| Configuration File              |                               Parameter | Purpose                                     |                                 Setting |
| ------------------------------- | --------------------------------------: | ------------------------------------------- | --------------------------------------: |
| `global.ini`                    | `parallel_data_backup_backint_channels` | Controls number of parallel backup channels |    8 (adjust based on system resources) |
| `global.ini`                    |               `data_backup_buffer_size` | Sets buffer size for backup operations      | 1024 (adjust based on available memory) |
| `aws-backint-agent-config.yaml` |                     `UploadChannelSize` | Controls size of upload chunks              |                256MB (Adjust as needed) |
| `aws-backint-agent-config.yaml` |                     `UploadConcurrency` | Number of concurrent upload streams         |                    8 (Adjust as needed) |
| `aws-backint-agent-config.yaml` |      `MaximumConcurrentFilesForRestore` | Controls parallel restore operations        |    5 (Adjust based on system resources) |
| `aws-backint-agent-config.yaml` |                   `DownloadConcurrency` | Number of concurrent download streams       |   10 (Adjust based on system resources) |

**Monitoring**: AWS Backup Dashboards centralize backup activity across AWS services. They track job statuses, monitor compliance, and showcase protected resources. The dashboards identify issues, monitor policy adherence, and manage operations. AWS Backup integrates with Amazon CloudWatch with metrics and events, allowing you to track backup and restore job statuses, and set alarms for failures.

![Blog-2](/images/3-Blog/Blog-2/CW-Metrics-i3.png)
Figure-3: Amazon CloudWatch dashboard to monitor AWS Backup for SAP HANA Backup, Restore and Copy jobs

**Troubleshooting errors**: When configuring AWS Backup for SAP HANA on Amazon EC2, take note of the following log files and the purpose of these files.

| Activity                                     | Related logs                                                                                                                                                                                                                                                                                                                                                                            |
| -------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Discovery and Registration                   | `/usr/bin/ssm-sap/logs/discovery.log`                                                                                                                                                                                                                                                                                                                                                   |
| Install AWS Backint Agent for SAP HANA       | `/var/lib/amazon/ssm/packages/AWSSAP-Backint/*/aws-backint-agent-install-*.log`                                                                                                                                                                                                                                                                                                         |
| Backup and Restore log for AWS Backint Agent | `/hana/shared/aws-backint-agent/aws-backint-agent.log`                                                                                                                                                                                                                                                                                                                                  |
| SAP HANA logs for system and tenant database | System DB backup and recovery logs: <br> <br> `/usr/sap/<SID>/<SID><instance>/<hostname>/trace/backup.log` <br> `/usr/sap/<SID>/<SID><instance>/<hostname>/trace/backint.log` <br> <br> Tenant DB backup and recovery logs: <br> <br> `/usr/sap/<SID>/<SID><instance>/<hostname>/trace/DB*<SID>/backup.log` <br> `/usr/sap/<SID>/<SID><instance>/<hostname>/trace/DB*<SID>/backint.log` |

|

**House-keeping activities for SAP HANA backups**: SAP note summarizes steps to maintain backup catalog house-keeping activities, and resolution for large backup catalog size to avoid SAP HANA Out Of Memory (OOM) errors. To learn more about SAP BASIS housekeeping activities for SAP HANA backups, please read the SAP Note: [3411878](https://me.sap.com/notes/3411878/E).

**Stay updated with AWS Backint Agent version**: Amazon Simple Notification Service (Amazon SNS) can notify you when new versions of AWS Backint agent or AWS Backint installer are released. To learn more, please visit [Install and configure AWS Backint Agent for SAP HANA – Update to the newest version or install a previous version of AWS Backint agent](https://docs.aws.amazon.com/sap/latest/sap-hana/aws-backint-agent-s3-installing-configuring.html#aws-backint-agent-update-version).

**Stay updated with SAP HANA database and Operating system version**: SAP note summarizes the list of SAP HANA database versions and supported operating systems. To learn more, please read the SAP note: [2235581](https://me.sap.com/notes/2235581).

## Conclusion

This blog outlined strategies and best practices for implementing AWS Backup in SAP HANA HA deployments. It covered fully-managed capabilities such as scheduled backups, simplified recovery for HA deployments, cross-region cross-account copies for DR and ransomware protection, compliance adherence with Audit Manager, and CloudWatch metrics for monitoring jobs and recovery points. Cost optimization through lifecycle policies and retention period reviews was also addressed. The importance of regular strategy reviews, audits, and refinements based on evolving business needs was emphasized. These practices establish a robust, efficient, and compliant AWS Backup strategy for SAP HANA HA deployments, enhancing data protection and operational resilience.

To get started with the AWS Backup service, we recommend that you review the documentation, blog and workshop below.

- SAP HANA databases on Amazon EC2 instances backup
- Automate and Simplify SAP HANA Backups with AWS Backup
- Simplify SAP Backups with AWS Services
- Perform Cross-Region, Cross-Account Backup and Restore of SAP HANA database using AWS Backup for SAP
- AWS Backup for SAP HANA High-Availability

To learn why thousands of customers trust AWS to migrate, modernize, and innovate with their SAP workloads, visit the [SAP on AWS](https://aws.amazon.com/sap/) page.

## Credits

I would like to thank the following team members for their contributions: Nerys Olver, Dhrumin Shah, Adam Hill, Spencer Martenson, David Rocha, Adam Kaminski and Balaji Krishna.

TAGS: #saponaws, #awsforsap, AWS Backup, Backup, SAP, SAP on AWS, AWS Backint, SAP HANA, SAP High Availability, SAP on AWS
