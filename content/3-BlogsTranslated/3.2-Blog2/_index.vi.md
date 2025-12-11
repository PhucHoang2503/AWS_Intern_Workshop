---
title: "Blog 2"
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

# Tăng cường khả năng phục hồi và bảo vệ dữ liệu cho các triển khai SAP HANA High Availability (HA) với AWS Backup for SAP HANA

**Tác giả**: Vijay Sitaram, Amit Mohanty, Archana Kuppuswamy, and Khaliluddin Siddiqui
**Ngày đăng**: 1/4/2025
**Danh mục**: AWS Backup, AWS Support, Best Practices, SAP on AWS, Technical How-to, Thought Leadership

## Giới thiệu

Các cơ sở dữ liệu SAP HANA thường được triển khai trong các cấu hình Tính khả dụng cao (High Availability - HA) để duy trì hoạt động liên tục trên AWS. Các cơ sở dữ liệu SAP HANA cần cả cấu hình HA lẫn chiến lược sao lưu toàn diện để bảo vệ dữ liệu và hỗ trợ phục hồi hệ thống đầy đủ. Mặc dù các triển khai SAP HA mang lại nhiều lợi ích, các cơ sở dữ liệu SAP HANA vẫn dễ bị ảnh hưởng bởi nhiều rủi ro, bao gồm các lỗi trên toàn cụm (cluster-wide failures), lỗi logic, hỏng dữ liệu, tấn công ransomware và các vấn đề tuân thủ. Chỉ HA thôi không đủ để giải quyết các tình huống như nhu cầu phục hồi theo thời điểm (point-in-time recovery), làm mới hệ thống cho môi trường kiểm thử/phát triển (test/development), hoặc yêu cầu lưu trữ dữ liệu dài hạn. Các tổ chức cần một chiến lược tổng thể để bảo vệ dữ liệu, tuân thủ quy định và khả năng phục hồi vận hành (operational resilience).

Đối với các triển khai RISE with SAP on AWS, việc sao lưu cơ sở dữ liệu được quản lý thông qua AWS Backint Agent for SAP HANA. Đây là giải pháp sao lưu tiêu chuẩn tích hợp với các cơ sở dữ liệu SAP HANA. Quy trình sao lưu được SAP quản lý hoàn toàn như một phần của dịch vụ RISE, nhưng khách hàng có thể giám sát trạng thái sao lưu thông qua các công cụ do SAP cung cấp và bảng điều khiển AWS Backup (AWS Backup console). Đối với các khách hàng chạy SAP nguyên bản (natively) trên AWS, như SAP ERP hoặc S/4HANA, AWS Backup for SAP HANA là một lựa chọn tuyệt vời.

Trong bài blog này, bạn sẽ tìm hiểu cách các tổ chức đang triển khai các chiến lược sao lưu mạnh mẽ cho cơ sở dữ liệu SAP HANA của họ với AWS Backup trong triển khai HA. Bài viết cũng đề cập đến việc bảo vệ dữ liệu, trụ cột Độ tin cậy (Reliability pillar) trong SAP Lens của Well-Architected Framework, cùng các thực hành vận hành tốt nhất (Operational best practices).

## Bảo vệ dữ liệu và Well-Architected Framework cho SAP Lens

SAP Lens của **Well-Architected Framework** nhấn mạnh việc sao lưu cơ sở dữ liệu, chủ yếu trong trụ cột Độ tin cậy (Reliability). Trụ cột này cung cấp hướng dẫn chuyên môn cho các tải công việc SAP (SAP workloads), giúp chúng thực hiện các chức năng dự định một cách nhất quán và chính xác, ngay cả khi đối mặt với sự cố hoặc gián đoạn.

Trong trụ cột Reliability, backup cơ sở dữ liệu được nhấn mạnh là thành phần quan trọng trong nhiều lĩnh vực then chốt:

1. **Bảo vệ dữ liệu**: Framework cung cấp các thực hành tốt nhất (best practices) để triển khai chiến lược sao lưu và phục hồi (recovery) mạnh mẽ cho cơ sở dữ liệu SAP và các dữ liệu quan trọng khác. Điều này cung cấp hướng dẫn để các tổ chức chuẩn bị phục hồi dữ liệu khi xảy ra lỗi hệ thống, hỏng dữ liệu, hoặc các sự cố ngoài dự kiến.
2. **Phục hồi thảm họa (Disaster Recovery)**: Backup đóng vai trò quan trọng trong lập kế hoạch disaster recovery. SAP Lens hướng dẫn cách thiết lập cơ chế disaster recovery tận dụng backup để khôi phục hoạt động khi xảy ra sự cố lớn.
3. **High Availability (HA)**: Mặc dù không trực tiếp liên quan đến sao lưu, các chiến lược tính khả dụng cao (HA) bổ sung cho các quy trình sao lưu bằng cách đảm bảo thời gian hoạt động của hệ thống và giảm thiểu nhu cầu phục hồi từ các bản sao lưu.
4. **Lưu trữ dữ liệu dài hạn**: Framework đưa ra khuyến nghị về lưu trữ và archive dữ liệu dài hạn, thường liên quan đến chiến lược backup để đáp ứng yêu cầu tuân thủ và nhu cầu kinh doanh.

Trụ cột Reliability nhấn mạnh một số khía cạnh chính trong chiến lược backup cơ sở dữ liệu:

- Triển khai lịch backup đều đặn và nhất quán
- Kiểm tra kỹ các quy trình khôi phục để đảm bảo hiệu quả
- Tự động hóa giải pháp backup để giảm lỗi con người (human error) và duy trì sự nhất quán
- Duy trì tính toàn vẹn và bảo mật dữ liệu trong toàn bộ quá trình backup và khôi phục
- Căn chỉnh chiến lược sao lưu với Mục tiêu Thời gian Phục hồi (**Recovery Time Objective - RTO**) và Mục tiêu Điểm Phục hồi (**Recovery Point Objective - RPO**) của tổ chức

Bằng cách tập trung vào các lĩnh vực này, SAP Lens trong Well-Architected Framework (SAP Lens of the Well-Architected Framework) cung cấp hướng dẫn toàn diện giúp workloads SAP trên AWS có khả năng phục hồi và phục hồi được. Nó hỗ trợ các tổ chức triển khai chiến lược backup hiệu quả, đảm bảo continuity cho doanh nghiệp và giảm thiểu rủi ro mất dữ liệu. Cách tiếp cận này cho phép khôi phục nhanh các hệ thống SAP quan trọng khi xảy ra sự cố, duy trì độ tin cậy và tính khả dụng cần thiết cho hoạt động cốt lõi của doanh nghiệp.

## Tổng quan về AWS Backint Agent cho SAP HANA

**AWS Backint Agent for SAP HANA** cung cấp khả năng tích hợp nguyên bản (native integration) với các tính năng sao lưu của SAP HANA, đảm bảo các bản sao lưu nhất quán với ứng dụng (application-consistent backups). Giải pháp sử dụng Amazon S3 để lưu trữ, mang lại độ bền, khả năng mở rộng và chi phí hợp lý. Agent Backint hỗ trợ sao lưu gia tăng (incremental backups), giảm chi phí lưu trữ và cửa sổ sao lưu (backup windows). Nó cũng hỗ trợ mã hóa cho các bản sao lưu khi truyền (in transit) và lưu trữ (at rest), tăng cường bảo mật. Giải pháp có thể lập kịch bản (scriptable), cho phép tự động hóa và tích hợp với các quy trình làm việc sao lưu (backup workflows) hiện có. Nó hỗ trợ Point-In-Time Recovery (PITR), quan trọng để xử lý lỗi dữ liệu hoặc lỗi do con người. Backint có thể mở rộng cho các cơ sở dữ liệu SAP HANA lớn, giảm tối đa ảnh hưởng đến hiệu suất trong quá trình backup. Khả năng tiết kiệm chi phí là một tính năng then chốt, tận dụng lớp lưu trữ S3 (S3 storage class) và chính sách vòng đời (lifecycle policy) để chuyển sang các lớp lưu trữ rẻ hơn cho việc lưu trữ dài hạn. Giải pháp tận dụng độ bền và tính khả dụng cao của Amazon S3 cho việc lưu trữ các bản sao lưu.

Những tính năng này làm cho AWS Backint Agent trở thành lựa chọn tốt cho khách hàng cần giải pháp backup SAP-native đáng tin cậy, tận dụng khả năng lưu trữ AWS, đặc biệt là những khách hàng muốn tích hợp trực tiếp với công cụ SAP và có yêu cầu backup đơn giản. Để biết cách cấu hình AWS Backint Agent với Amazon S3, tham khảo: Backup and restore SAP HANA workloads to Amazon S3.

## AWS Backup cho SAP HANA trên Amazon EC2

**AWS Backup for SAP HANA on Amazon Elastic Cloud Compute** (EC2) mang lại trải nghiệm hợp lý và được quản lý cho các hoạt động sao lưu và phục hồi (backup and restore) cơ sở dữ liệu SAP HANA. Nó nâng cao trải nghiệm khách hàng nhờ bảng điều khiển AWS Backup (AWS Backup console) trực quan, mở rộng khả năng bảo vệ dữ liệu và đơn giản hóa việc quản lý trên nhiều dịch vụ AWS, bao gồm Amazon EC2, Amazon Elastic Block Store (EBS) và Amazon Elastic File System (EFS). Các tính năng sao lưu liên tục (continuous backup) giúp tiết kiệm đáng kể chi phí, đặc biệt đối với người dùng trước đây chỉ giới hạn ở các bản sao lưu đầy đủ (full backups) với AWS Backint Agent. AWS Backup hỗ trợ khả năng kiểm toán (audit) và tuân thủ (compliance) toàn diện cho các bản sao lưu với Backup Vault Lock và Legal Holds. Nó cũng hỗ trợ sao chép backup tự động giữa các region và account, nâng cao khả năng DR và độ dư thừa dữ liệu. Dịch vụ full-managed này tối ưu quản lý dữ liệu SAP HANA, tăng độ tin cậy và hiệu quả vận hành trong hệ sinh thái AWS.

Để biết cách tự động hóa backup SAP HANA với AWS Backup, xem blog [Automate and Simplify SAP HANA Backups with AWS Backup](https://aws.amazon.com/blogs/awsforsap/general-availability-announcement-aws-backup-for-sap-hana-database/). Để cấu hình AWS Backup cho SAP HANA, xem [document](https://docs.aws.amazon.com/sap/latest/sap-hana/aws-backint-agent-backup.html).

## Khuyến nghị cho triển khai SAP HA

AWS Backup for SAP HANA hỗ trợ cả triển khai đơn nút (single-node) và HA. Trong triển khai HA, nó tự động xác định nút (node) đang hoạt động để thực hiện sao lưu, như minh họa trong Hình 1 (Figure 1) bên dưới. Khách hàng sử dụng phần mềm cụm Pacemaker (Pacemaker cluster) để quản lý triển khai SAP HANA HA.
![Blog-2](/images/3-Blog/Blog-2/Architecture-i1.png)
Hình 1: Triển khai AWS Backup for SAP HANA HA

Các bước cấu hình AWS Backup cho SAP HANA trên Amazon EC2:

1. Lưu thông tin xác thực HANA (HANA Credentials) trong Secrets Manager.
2. Kiểm tra quyền IAM (IAM permissions) và thẻ (tags) cho các phiên bản EC2 (EC2 instances) và khóa Secrets (Secrets key).
3. Đăng ký một phiên bản HANA (HANA instance) trong triển khai HA với SSM for SAP (ssm-sap).
4. Cài đặt Backint for AWS Backup bằng tài liệu SSM (SSM document) (AWSSAP-InstallBackintForAWSBackup).
5. Đăng ký tham gia (Opt-in) SAP HANA on Amazon EC2 trong cài đặt của bảng điều khiển AWS Backup (AWS Backup console settings).
6. Tạo backup plan.

Để khởi tạo một bản sao lưu theo yêu cầu (on-demand backup) của cơ sở dữ liệu SAP HANA trong triển khai HA từ bảng điều khiển AWS Backup (AWS Backup console). Các bản sao lưu theo lịch trình được cấu hình trong Kế hoạch sao lưu (Backup plan) bằng cách xác định chính sách sao lưu (backup policy). Sau đây là danh sách các hoạt động high-level:

1. AWS Backup gọi các API sao lưu của SSM for SAP (SSM for SAP backup APIs) để bắt đầu quá trình sao lưu.
2. SSM for SAP gọi Backint qua SSM run command.
3. Backup được lưu trong AWS Vault, trạng thái backup được ghi nhận.

Các bước dưới đây là bắt buộc phải thực hiện trước và sau khi khôi phục cơ sở dữ liệu trong triển khai SAP HA được quản lý bởi trình quản lý cụm Pacemaker:

**Trước restore**: cluster và các node (primary & standby) ở chế độ maintenance, SAP HANA dừng trên cả hai node. Restore system database, sau đó start primary trước khi restore tenant database. Trong quá trình restore, cluster và các node vẫn ở chế độ maintenance.

**Restore**: login vào console AWS Backup, restore bằng full backup hoặc continuous backup recovery point.

**Sau restore**: stop SAP HANA trên secondary node, đăng ký HSR trên primary và secondary, start SAP HANA trên secondary, đưa cluster trở lại trạng thái bình thường.

Các bước dưới đây là bắt buộc phải thực hiện trước và sau khi khôi phục cơ sở dữ liệu trong triển khai SAP HA được quản lý bởi trình quản lý cụm Pacemaker. Để tìm hiểu thêm về các bước bổ sung cần bao gồm khi khôi phục hệ thống SAP HANA dạng HA, vui lòng đọc tài liệu [SAP HANA HA restore](https://docs.aws.amazon.com/aws-backup/latest/devguide/saphana-restore.html#saphanarestoreha). Ngoài ra, bạn có thể tham khảo thêm hướng dẫn thiết lập cụm Pacemaker trong SAP HANA trên AWS tại tài liệu [SAP HANA on AWS: High Availability Configuration Guide for SLES and RHEL](https://docs.aws.amazon.com/sap/latest/sap-hana/sap-hana-on-aws-ha-configuration.html).

**Trước khi khôi phục**: Cụm cơ sở dữ liệu SAP và các nút (nút cơ sở dữ liệu chính và nút cơ sở dữ liệu dự phòng) phải được đặt ở chế độ bảo trì (maintenance mode), và SAP HANA phải được dừng trên cả hai nút. Sau khi khôi phục cơ sở dữ liệu hệ thống (system database), hãy khởi động (start) SAP HANA trên nút cơ sở dữ liệu chính trước khi tiến hành khôi phục cơ sở dữ liệu tenant (tenant database). Trong suốt quá trình khôi phục, cụm cơ sở dữ liệu SAP và các nút cụm vẫn ở chế độ bảo trì.

Để bắt đầu khôi phục cơ sở dữ liệu SAP HANA, hãy đăng nhập vào AWS Backup console và bắt đầu khôi phục bằng cách sử dụng bản backup đầy đủ hoặc mã định danh điểm khôi phục từ sao lưu liên tục. Dưới đây là danh sách các hoạt động chính:

1. AWS Backup gọi SSM for SAP backup APIs để bắt đầu quá trình khôi phục.
2. AWS SSM for SAP gọi Backint thông qua lệnh SSM run command.
3. Cơ sở dữ liệu được khôi phục từ AWS Vault đến nút cơ sở dữ liệu chính.

**Sau khi khôi phục**: Sau khi khôi phục thành công cơ sở dữ liệu hệ thống và cơ sở dữ liệu tenant, hãy dừng (stop) SAP HANA trên nút cơ sở dữ liệu thứ cấp (secondary database node). Đăng ký Sao chép Hệ thống SAP HANA (SAP HANA System Replication - HSR) trên nút cơ sở dữ liệu chính, sau đó trên nút cơ sở dữ liệu thứ cấp. Tiếp tục khởi động (start) SAP HANA trên nút cơ sở dữ liệu thứ cấp, và đưa các nút cụm cơ sở dữ liệu SAP cùng cụm toàn cục (global cluster) ra khỏi chế độ bảo trì.

## Đơn giản hóa quản lý và vận hành bảo vệ dữ liệu

AWS Backup for SAP HANA là một dịch vụ AWS được quản lý hoàn toàn (fully managed), cung cấp một bộ tính năng mạnh mẽ giúp hợp lý hóa quy trình sao lưu, tăng cường khả năng phục hồi và cải thiện năng lực phục hồi sau thảm họa (disaster recovery) cho cả triển khai SAP HANA đơn nút và HA.

| **Tính năng**                                                             | **Lợi ích**                                                                                                                                                                                                                                                                                                                                             |
| ------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Quản lý Tập trung (Centralized Management)                                | Một bảng điều khiển duy nhất để quản lý sao lưu và khôi phục trên nhiều phiên bản SAP, giúp đơn giản hóa các hoạt động phục hồi trong kiến trúc phân tán.                                                                                                                                                                                               |
| Lưu trữ Tối ưu Chi phí (Cost optimized storage)                           | Các tính năng quản lý chính sách vòng đời cho phép lưu trữ dài hạn bản sao lưu một cách tiết kiệm chi phí mà không ảnh hưởng đến khả năng phục hồi.                                                                                                                                                                                                     |
| Sao chép Liên Vùng & Liên Tài khoản (Cross-Region and Cross-Account copy) | Tích hợp nguyên bản với SAP HANA tạo ra các bản sao lưu nhất quán với ứng dụng, rất quan trọng để duy trì tính toàn vẹn dữ liệu trong môi trường SAP phức tạp. Khả năng sao lưu liên vùng và liên tài khoản cho phép lựa chọn phục hồi phân tán theo địa lý, tăng cường chiến lược khôi phục sau thảm họa và bảo vệ trước ransomware.                   |
| Tuân thủ & Kiểm toán (Compliance and Audit)                               | Giúp đáp ứng yêu cầu quy định với audit logs và retention policies, rất quan trọng đối với nhiều triển khai SAP. Vault Lock cho phép thực thi retention policies và ngăn việc xóa/sửa đổi các bản sao lưu trong AWS Backup vault. Hỗ trợ đặt legal holds để ngăn xóa dữ liệu bất kể cài đặt retention.                                                  |
| Giám sát & Cảnh báo (Monitoring and alerting)                             | Bảng điều khiển công việc mặc định trên AWS Backup để giám sát trạng thái sao lưu/khôi phục và trạng thái recovery point id. CloudWatch cung cấp metrics và alarms để giám sát lỗi sao lưu, khôi phục và recovery points.                                                                                                                               |
| Phục hồi tại một Thời điểm (Point-In-Time Recovery - PITR)                | Các bản sao lưu liên tục PITR được tự động hóa và lập lịch để duy trì recovery point IDs. PITR cho phép phục hồi nhanh cơ sở dữ liệu SAP HANA tới một timestamp cụ thể với độ chi tiết 1 giây, giảm thiểu downtime.                                                                                                                                     |
| Tự động hóa Dựa trên Chính sách (Policy based automation)                 | Giảm lỗi con người trong quá trình sao lưu/khôi phục nhờ tự động hóa và các chính sách được định nghĩa trước.                                                                                                                                                                                                                                           |
| Gắn thẻ Tài nguyên (Resource tagging)                                     | Hỗ trợ tối đa 50 tags cho sao lưu SAP HANA, và các dịch vụ AWS như Amazon EC2 AMI backups, Amazon EBS snapshots và Amazon EFS filesystem snapshots.                                                                                                                                                                                                     |
| Quản lý Phục hồi Đơn giản hóa (Simplified Recovery management)            | **Recovery Time Objective (RTO):** Kết hợp sao lưu liên tục (đầy đủ & gia tăng) và database logs cùng quy trình tự động hóa giúp giảm đáng kể thời gian phục hồi hệ thống SAP. <br> **Recovery Point Objective (RPO):** Sao lưu liên tục theo lịch trình giúp có recovery points và logs gần nhất, giảm thiểu mất dữ liệu xuống mức độ chi tiết 1 giây. |
| Khả năng Mở rộng (Scalability)                                            | Dễ dàng mở rộng để xử lý các cơ sở dữ liệu SAP HANA lớn mà không ảnh hưởng đến tốc độ hoặc độ tin cậy phục hồi.                                                                                                                                                                                                                                         |
| Lập lịch (Scheduling)                                                     | Lập lịch trực tiếp từ AWS Backup console theo giờ, ngày, tuần hoặc cron expressions tùy chỉnh.                                                                                                                                                                                                                                                          |
| Bảo mật (Security)                                                        | Mã hóa tích hợp sẵn với KMS và IAM giúp bảo vệ dữ liệu sao lưu/khôi phục, đảm bảo tính toàn vẹn và kiểm soát truy cập.                                                                                                                                                                                                                                  |
| Hỗ trợ thêm các thành phần SAP (Support for additional SAP components)    | Tích hợp liền mạch với các dịch vụ AWS khác, hỗ trợ các kịch bản phục hồi nâng cao và tự động hóa như Amazon EC2, Amazon EBS và Amazon EFS.                                                                                                                                                                                                             |

AWS Backup for SAP HANA và AWS Backint Agent đưa ra các cách tiếp cận khác biệt để sao lưu cơ sở dữ liệu SAP HANA. Backint là dịch vụ đặc thù cho SAP HANA, tích hợp liền mạch với hệ sinh thái SAP và sử dụng các công cụ SAP để quản lý và phục hồi. Ngược lại, AWS Backup for SAP HANA là một dịch vụ linh hoạt hơn, cung cấp khả năng sao lưu trên nhiều dịch vụ AWS, bao gồm cả SAP HANA chạy nguyên bản (native). Nó cung cấp tính linh hoạt cao hơn trong các tùy chọn lưu trữ và vòng đời, được quản lý thông qua AWS console.

Dưới đây là bảng so sánh song song AWS Backint Agent và AWS Backup for SAP HANA:

| **Tính năng**                              | **AWS Backint Agent for SAP HANA**                                                                    | **AWS Backup for SAP HANA**                                                                                                                                  |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Tự động hóa (Automation)                   | Cơ chế lập lịch của SAP HANA                                                                          | Backup plan được cấu hình với Backup rule                                                                                                                    |
| Loại sao lưu (Backup Types)                | Sao lưu đầy đủ (full), gia tăng (incremental), vi sai (differential) và logs                          | Sao lưu đầy đủ (full), gia tăng (incremental) và logs                                                                                                        |
| Quản lý Catalog (Catalog management)       | Lưu trữ trong SAP HANA backup catalog                                                                 | Lưu trữ trong AWS Backup catalog                                                                                                                             |
| Tuân thủ (Compliance)                      | Cả hai đều hỗ trợ audit logs và retention policies như legal hold                                     |                                                                                                                                                              |
| Khôi phục sau thảm họa (Disaster Recovery) | Amazon S3 Cross-Region Replication (CRR)                                                              | AWS Backup sao chép bản sao lưu giữa các vùng (cross-region) và giữa các tài khoản (cross-account) sang cùng hoặc khác AWS Vault với cùng hoặc khác KMS key. |
| Công cụ người dùng cuối (End-user tool)    | Sao lưu và phục hồi được quản lý cho cơ sở dữ liệu SAP HANA từ SAP HANA Studio                        | Quản lý sao lưu và phục hồi từ AWS Backup console cho SAP HANA và các thành phần SAP khác như Amazon EC2, Amazon EBS và Amazon EFS.                          |
| Mã hóa (Encryption)                        | Mã hóa phía server-side của Amazon S3 bucket                                                          | Các bản sao lưu SAP HANA luôn được mã hóa. AWS KMS encryption được cấu hình trong AWS Backup Vault                                                           |
| Mức độ chi tiết (Granularity)              | Sao lưu cấp độ file và cấp độ database                                                                | Sao lưu cấp độ database và sao lưu các thành phần SAP như Amazon EC2, Amazon EBS và Amazon EFS                                                               |
| Quản lý vòng đời (Lifecycle management)    | Amazon S3 lifecycle policy                                                                            | AWS Backup Vault lifecycle policy                                                                                                                            |
| Hiệu năng (Performance)                    | Tối ưu cho các hoạt động sao lưu đặc thù của SAP HANA                                                 | Tối ưu cho các hoạt động sao lưu SAP HANA và các dịch vụ AWS (Amazon EC2, Amazon EBS, Amazon EFS)                                                            |
| Phục hồi (Recovery)                        | Phục hồi toàn bộ cơ sở dữ liệu và PITR từ SAP HANA Studio                                             | Phục hồi toàn bộ cơ sở dữ liệu và PITR từ AWS Backup console                                                                                                 |
| Khả năng mở rộng (Scalability)             | Thiết kế dành riêng cho cơ sở dữ liệu SAP HANA                                                        | Thiết kế cho SAP HANA và các dịch vụ AWS như Amazon EC2, Amazon EBS và Amazon EFS                                                                            |
| Lưu trữ (Storage)                          | Bản sao lưu được lưu trực tiếp trong Amazon S3 bucket do khách hàng quản lý                           | Bản sao lưu được lưu trực tiếp trong AWS Backup Vault do dịch vụ quản lý                                                                                     |
| Bảo mật (Security)                         | Amazon S3 Object Lock với mô hình Write-Once-Read-Many (WORM). Hỗ trợ IAM file-grained access control | AWS Backup Vault Lock để tăng cường bảo mật và kiểm soát các ảnh sao lưu. Hỗ trợ IAM file-grained access control                                             |

## Các biện pháp thực hành vận hành tốt nhất (Operational best practices)

Trong khi AWS Backint Agent được tích hợp sâu với SAP, AWS Backup mở rộng khả năng này và tích hợp rộng hơn với các dịch vụ AWS. Việc AWS Backup cung cấp chức năng liên dịch vụ (cross-service) rộng hơn và Backint cung cấp các tính năng chuyên biệt tập trung vào SAP khiến nó trở thành một lựa chọn phù hợp cho chiến lược sao lưu SAP HANA. Dưới đây là các thực hành vận hành tốt nhất (operational best practices) cho AWS Backup for SAP HANA trên Amazon EC2.

Khám phá và đăng ký (**Discovery and registration**): AWS Backup for SAP HANA yêu cầu quyền truy cập điểm cuối công cộng (public endpoint) cho một số dịch vụ AWS, bao gồm SSM for SAP (SSM4SAP), Systems Manager (SSM), Secrets Manager, và AWS Backup. Để cho phép hoạt động trơn tru, hãy cấu hình tường lửa (firewall) để cho phép cả hai nút SAP HANA truy cập điểm cuối dịch vụ (service endpoint) và điểm cuối lưu trữ (storage endpoint). AWS SSM Agent phải được cài đặt và đang chạy trên các phiên bản EC2 (EC2 instances) lưu trữ cơ sở dữ liệu SAP HANA, với các vai trò IAM (IAM roles) chính xác được đính kèm. Cần phải đáp ứng các điều kiện tiên quyết (prerequisites) này trên tất cả các nút HANA để tạo điều kiện thuận lợi cho việc **đăng ký cơ sở dữ liệu SAP HANA với AWS SSM4SAP** và thực hiện thành công các hoạt động sao lưu và phục hồi (backup and restore) thông qua AWS Backup.

Lưu trữ tối ưu chi phí (**Cost-optimized storage**): Tối ưu hóa chi phí và hợp lý hóa quản trị với các tầng lưu trữ và vòng đời (**lifecycle and storage tiers**) của AWS Backup Vault cho các môi trường SAP HANA. Triển khai một Kế hoạch sao lưu phân tầng (tiered Backup Plan) toàn diện, kết hợp cả các bản sao lưu cơ sở dữ liệu đầy đủ (full database backups) và các bản sao lưu PITR (PITR backups) được căn chỉnh với chính sách lưu giữ (retention policy) của tổ chức bạn. Chiến lược này cho phép bảo vệ dữ liệu mạnh mẽ trong khi quản lý chi phí hiệu quả. Đối với các bản sao lưu đầy đủ (full backups), tận dụng các chính sách vòng đời sao lưu (backup lifecycle policies) để tự động chuyển các bản sao lưu cũ hơn sang các giải pháp lưu trữ lạnh (cold storage) tiết kiệm chi phí như Amazon S3 Glacier. Cách tiếp cận này giảm đáng kể chi phí lưu trữ dài hạn mà không ảnh hưởng đến khả năng truy cập dữ liệu. Mặc dù phân tầng lưu trữ (storage tiering) không được hỗ trợ cho PITR (sao lưu liên tục), PITR sẽ xác định loại sao lưu cơ sở dữ liệu (đầy đủ hoặc gia tăng) một cách thông minh dựa trên khối lượng thay đổi được ghi vào cơ sở dữ liệu, và kết hợp với chính sách vòng đời (lifecycle policy) sẽ tối ưu hóa chi phí lưu trữ.

Bảo mật, Quản trị và Tuân thủ (**Security, Governance and Compliance**): AWS Backup **Vault Lock** thiết lập các khung quản trị (governance frameworks) mạnh mẽ, căn chỉnh bảo vệ dữ liệu với các yêu cầu tuân thủ nghiêm ngặt. Các chế độ tuân thủ và quản trị (compliance and governance modes) của Vault Lock cho phép các tổ chức đáp ứng hiệu quả các quy định cụ thể của ngành. Chức năng giữ hợp pháp (Legal hold) ngăn chặn việc xóa các bản sao lưu đang được xem xét, đảm bảo tính toàn vẹn dữ liệu trong quá trình kiểm toán (audits) hoặc thủ tục pháp lý. Triển khai các kho lưu trữ được mã hóa (**encrypted vaults**) an toàn của AWS Backup Vault và các bản sao lưu bất biến (immutable backups). Cách tiếp cận nhiều lớp (multi-layered) này bảo vệ dữ liệu SAP khỏi truy cập trái phép, các cuộc tấn công ransomware, và các mối đe dọa nội bộ và bên ngoài. Theo mặc định, các bản sao lưu SAP HANA được mã hóa, và AWS Backup Vault yêu cầu sử dụng AWS Key Management Service (KMS) để mã hóa lưu trữ sao lưu, đảm bảo tính bảo mật dữ liệu ở trạng thái nghỉ (data confidentiality at rest).

Phục hồi Thảm họa (DR) với sao chép liên tài khoản và liên vùng (**cross-account and cross-region copy**): Chiến lược sao lưu đa vùng (multi-region backup strategy) này tăng cường đáng kể tính liên tục trong kinh doanh (business continuity) và khả năng phục hồi (resilience) chống lại các sự cố ngừng hoạt động theo vùng (regional outages). Chức năng liên vùng và liên tài khoản (**cross-region cross-account**) cho phép các bản sao lưu được tự động sao chép sang các Vùng AWS (AWS regions) và Tài khoản (account) khác nhau, đảm bảo tính sẵn sàng của dữ liệu (data availability) ngay cả khi toàn bộ một Vùng không khả dụng. Cross-account copying (Sao chép liên tài khoản) bổ sung một lớp bảo vệ khác bằng cách lưu trữ các bản sao lưu trong các tài khoản AWS riêng biệt, cách ly chúng khỏi các vi phạm bảo mật tiềm tàng trong tài khoản chính. Để tìm hiểu cách thiết lập cross-region and cross-account copy, vui lòng tham khảo bài [blog](https://aws.amazon.com/blogs/awsforsap/perform-cross-region-cross-account-backup-and-restore-of-sap-hana-database-using-aws-backup-for-sap/).

Tinh chỉnh Hiệu suất (**Performance tuning**): Khuyến nghị nên điều chỉnh kích thước phù hợp (right-size) cho phiên bản EC2 cơ sở dữ liệu HANA (HANA database EC2 instance) và các ổ đĩa EBS (EBS volumes) để đạt được hiệu suất tối ưu cho các hoạt động hàng ngày và sao lưu. Đối với các phiên bản EC2 được tối ưu hóa bộ nhớ và chứng nhận SAP (SAP-certified memory optimized EC2 instances), hãy xem xét các thông số kỹ thuật EBS (**EBS specifications**) về băng thông tối đa, thông lượng tối đa và IOPS tối đa cho loại phiên bản (instance). Điều này hữu ích trong việc hiểu các giới hạn cho EC2 instance khi tinh chỉnh từng EBS volumes cho dữ liệu và logs của SAP HANA. Điều này có tác động trực tiếp đến hiệu suất của bất kỳ workload nào, bao gồm cả các bản sao lưu. Để đo lường EBS metrics cho từng volumes và metrics tổng hợp ở cấp độ instance, vui lòng tham khảo **AWSSupport-CalculateEBSPerformanceMetrics** runbook giúp chẩn đoán các sự cố hiệu suất Amazon Elastic Block Store (Amazon EBS) bằng cách tính toán và xuất bản các performance metrics đến một Amazon CloudWatch dashboard.

![Blog-2](/images/3-Blog/Blog-2/SAW-Metrics-i2.png)
Hình 2: Bảng điều khiển Amazon CloudWatch từ runbook AWSSupport-CalculateEBSPerformanceMetrics

Cần xem xét và điều chỉnh cẩn thận các tham số này liên quan đến SAP HANA và AWS Backint Agent để chúng đáp ứng các mục tiêu về hiệu suất và thời gian phục hồi. Các tham số cấu hình để tinh chỉnh SAP HANA được lưu trữ trong /hana/shared/$SID/global/hdb/custom/config/global.ini, và đối với AWS Backint Agent trong /hana/shared/aws-backint-agent/aws-backint-agent-config.yaml. Các tham số này nên được điều chỉnh dựa trên tài nguyên hệ thống (CPU, bộ nhớ, dung lượng mạng), kích thước cơ sở dữ liệu và đặc điểm tải công việc (workload characteristics), yêu cầu cửa sổ sao lưu (Backup window), và băng thông mạng khả dụng. Để tìm hiểu thêm về tinh chỉnh hiệu suất sao lưu và phục hồi SAP HANA, vui lòng tham khảo [Install and configure AWS Backint Agent for SAP HANA – Performance tuning](https://docs.aws.amazon.com/sap/latest/sap-hana/aws-backint-agent-s3-installing-configuring.html#aws-backint-agent-performance-tuning) và [Install and configure AWS Backint Agent for SAP HANA – Backint-related SAP HANA parameters](https://docs.aws.amazon.com/sap/latest/sap-hana/aws-backint-agent-s3-installing-configuring.html#aws-backint-agent-sap-hana-parameters).

Cấu hình tham số để tối ưu hóa Sao lưu và Phục hồi SAP HANA

# Bảng Cấu Hình AWS Backint & SAP HANA

| Tệp cấu hình                    | Tham số                                 | Mục đích                                         | Giá trị khuyến nghị                    |
| ------------------------------- | --------------------------------------- | ------------------------------------------------ | -------------------------------------- |
| `global.ini`                    | `parallel_data_backup_backint_channels` | Điều khiển số lượng kênh backup song song        | 8 (tùy chỉnh theo tài nguyên hệ thống) |
| `global.ini`                    | `data_backup_buffer_size`               | Thiết lập kích thước bộ đệm cho quá trình backup | 1024 (tùy chỉnh theo dung lượng RAM)   |
| `aws-backint-agent-config.yaml` | `UploadChannelSize`                     | Điều khiển kích thước mỗi khối upload            | 256MB (có thể điều chỉnh)              |
| `aws-backint-agent-config.yaml` | `UploadConcurrency`                     | Số luồng upload đồng thời                        | 8 (có thể điều chỉnh)                  |
| `aws-backint-agent-config.yaml` | `MaximumConcurrentFilesForRestore`      | Điều khiển số lượng file được restore song song  | 5 (tùy theo tài nguyên hệ thống)       |
| `aws-backint-agent-config.yaml` | `DownloadConcurrency`                   | Số luồng download đồng thời                      | 10 (tùy theo tài nguyên hệ thống)      |

Giám sát (**Monitoring**): Bảng điều khiển AWS Backup (AWS Backup Dashboards) tập trung hóa hoạt động sao lưu trên các dịch vụ AWS. Chúng theo dõi trạng thái công việc, giám sát sự tuân thủ, và hiển thị các tài nguyên được bảo vệ. Các bảng điều khiển này xác định các vấn đề, giám sát việc tuân thủ chính sách và quản lý các hoạt động. AWS Backup tích hợp với Amazon CloudWatch với các chỉ số (metrics) và các sự kiện (events), cho phép bạn theo dõi trạng thái công việc sao lưu và phục hồi, và đặt các cảnh báo (alarms) cho các lỗi.

![Blog-2](/images/3-Blog/Blog-2/CW-Metrics-i3.png)
Hình 3: Bảng điều khiển Amazon CloudWatch để giám sát các công việc Sao lưu, Phục hồi và Sao chép của AWS Backup for SAP HANA

Khắc phục sự cố (**Troubleshooting errors**): Khi cấu hình AWS Backup for SAP HANA trên Amazon EC2, hãy lưu ý các tệp log sau và mục đích của các tệp này.

# Nhật ký hoạt động và vị trí log

| Hoạt động                                                 | Nhật ký liên quan                                                                                                                                                                                                                                                                                                                                                             |
| --------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Tự động phát hiện và Đăng ký (Discovery and Registration) | `/usr/bin/ssm-sap/logs/discovery.log`                                                                                                                                                                                                                                                                                                                                         |
| Cài đặt AWS Backint Agent cho SAP HANA                    | `/var/lib/amazon/ssm/packages/AWSSAP-Backint/*/aws-backint-agent-install-*.log`                                                                                                                                                                                                                                                                                               |
| Nhật ký sao lưu và phục hồi của AWS Backint Agent         | `/hana/shared/aws-backint-agent/aws-backint-agent.log`                                                                                                                                                                                                                                                                                                                        |
| Nhật ký SAP HANA cho System DB và Tenant DB               | Nhật ký sao lưu và phục hồi của System DB: <br><br> `/usr/sap/<SID>/<SID><instance>/<hostname>/trace/backup.log` <br> `/usr/sap/<SID>/<SID><instance>/<hostname>/trace/backint.log` <br><br> Nhật ký sao lưu và phục hồi của Tenant DB: <br><br> `/usr/sap/<SID>/<SID><instance>/<hostname>/trace/DB*<SID>/backup.log` <br> `/usr/sap/<SID>/<SID>/trace/DB*<SID>/backint.log` |

Các hoạt động dọn dẹp (**House-keeping activities**) cho các bản sao lưu SAP HANA: SAP note tóm tắt các bước để duy trì các hoạt động dọn dẹp (house-keeping activities) danh mục sao lưu, và giải pháp cho kích thước danh mục sao lưu lớn để tránh các lỗi Hết Bộ nhớ (Out Of Memory - OOM) của SAP HANA. Để tìm hiểu thêm về các hoạt động dọn dẹp SAP BASIS (SAP BASIS housekeeping activities) cho các bản sao lưu SAP HANA, vui lòng đọc SAP Note: [3411878](https://me.sap.com/notes/3411878/E).

**Luôn cập nhật phiên bản AWS Backint Agent**: Amazon Simple Notification Service (Amazon SNS) có thể thông báo cho bạn khi các phiên bản mới của AWS Backint agent hoặc AWS Backint installer được phát hành. Để tìm hiểu thêm, vui lòng truy cập [Install and configure AWS Backint Agent for SAP HANA – Update to the newest version or install a previous version of AWS Backint agent](https://docs.aws.amazon.com/sap/latest/sap-hana/aws-backint-agent-s3-installing-configuring.html#aws-backint-agent-update-version).

Luôn cập nhật phiên bản cơ sở dữ liệu SAP HANA và Hệ điều hành (**Operating system version**): SAP note tóm tắt danh sách các phiên bản cơ sở dữ liệu SAP HANA và các hệ điều hành được hỗ trợ. Để tìm hiểu thêm, vui lòng đọc SAP note: [2235581](https://me.sap.com/notes/2235581).

## Kết luận

Bài blog này đã phác thảo các chiến lược và các thực hành tốt nhất (best practices) để triển khai AWS Backup trong các triển khai SAP HANA HA. Nó bao gồm các khả năng được quản lý hoàn toàn (fully managed capabilities) như sao lưu theo lịch trình, phục hồi đơn giản hóa cho các triển khai HA, sao chép liên vùng và liên tài khoản (cross-region cross-account copies) cho phục hồi sau thảm họa (DR) và bảo vệ khỏi ransomware, tuân thủ với Audit Manager, và các chỉ số CloudWatch (CloudWatch metrics) để giám sát các công việc và các điểm phục hồi (recovery points). Tối ưu hóa chi phí (Cost optimization) thông qua các chính sách vòng đời (lifecycle policies) và việc đánh giá thời gian lưu giữ (retention period) cũng đã được đề cập. Tầm quan trọng của việc xem xét chiến lược, kiểm toán và tinh chỉnh thường xuyên dựa trên nhu cầu kinh doanh đang phát triển đã được nhấn mạnh. Những biện pháp thực hành này thiết lập một chiến lược AWS Backup mạnh mẽ, hiệu quả và tuân thủ cho các triển khai SAP HANA HA, tăng cường bảo vệ dữ liệu và khả năng phục hồi vận hành (operational resilience).

Để bắt đầu với dịch vụ AWS Backup, chúng tôi khuyên bạn nên xem xét tài liệu, blog và workshop dưới đây.

- SAP HANA databases on Amazon EC2 instances backup
- Automate and Simplify SAP HANA Backups with AWS Backup
- Simplify SAP Backups with AWS Services
- Perform Cross-Region, Cross-Account Backup and Restore of SAP HANA database using AWS Backup for SAP
- AWS Backup for SAP HANA High-Availability

Để tìm hiểu lý do vì sao hàng nghìn khách hàng tin tưởng AWS để di chuyển (migrate), hiện đại hóa (modernize) và đổi mới (innovate) các khối lượng công việc SAP (SAP workloads) của họ, hãy truy cập trang [SAP on AWS](https://aws.amazon.com/sap/).

## Lời cảm ơn

Tôi muốn cảm ơn các thành viên nhóm sau đây vì những đóng góp của họ: Nerys Olver, Dhrumin Shah, Adam Hill, Spencer Martenson, David Rocha, Adam Kaminski và Balaji Krishna

TAGS: #saponaws, #awsforsap, AWS Backup, Backup, SAP, SAP on AWS.

TAGS: #saponaws, AWS Backint, AWS Backup, SAP, SAP HANA, SAP High Availability, SAP on AWS
