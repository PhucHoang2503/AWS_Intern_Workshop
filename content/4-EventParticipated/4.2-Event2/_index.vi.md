---
title: "Event 2"
weight: 1
chapter: false
pre: " <b> 4.2. </b> "
---

# Bài thu hoạch “AWS Cloud Mastery Series #2 : DevOps on AWS Workshop”

### Mục Đích Của Sự Kiện

- Buổi workshop hướng đến việc giúp người tham dự hiểu DevOps như một văn hóa và triết lý làm việc — không chỉ là tập hợp công cụ—dựa trên tinh thần hợp tác, tốc độ và cải tiến liên tục, được củng cố bởi các chỉ số quan trọng như DORA metrics.
- Người tham gia được giới thiệu toàn cảnh về hệ sinh thái DevOps trên AWS, bao gồm mọi giai đoạn của vòng đời phần mềm từ quản lý mã nguồn đến vận hành sản phẩm.
- Thay vì chỉ học lý thuyết, sự kiện tập trung vào các phần demo thực tế như xây dựng pipeline CI/CD, triển khai hạ tầng bằng mã (IaC), và vận hành ứng dụng container hóa.
- Các phiên chia sẻ làm nổi bật cách theo dõi, truy vết và xử lý sự cố trong các hệ thống phân tán theo kiến trúc cloud-native.

### Danh Sách Diễn Giả

- **Truong Quang Tinh**
- **Van Hoang Kha**
- **Quoc Bao**
- **Phuc Thinh**
- **Dai Vi**
- **Long Huynh**
- **Quy Pham**
- **Nghiem Le**

### Nội Dung Nổi Bật

#### Từ Mã nguồn đến Đám mây: Đường ống CI/CD Tự động

- **Nội dung**: Tập trung vào việc xây dựng quy trình CI/CD hoàn chỉnh với các dịch vụ DevOps của AWS:
  - **CodeCommit** cho quản lý mã nguồn và chiến lược nhánh hợp lý.
  - **CodeBuild** để tự động hóa bước build và kiểm thử.
  - **CodeDeploy** với các kỹ thuật triển khai hiện đại như Blue/Green, Rolling, và Canary.
  - **CodePipeline** để điều phối toàn bộ quy trình thành một luồng tự động thống nhất.
- **Khoảnh khắc nổi bật**: Phần demo trực tiếp pipeline đầy đủ — từ khi commit mã đến lúc triển khai lên môi trường production—giúp người xem hình dung cách các bước liên kết thành một quy trình liền mạch.

#### Xây dựng Nền tảng: Hạ tầng bằng Mã lệnh (IaC)

- **Nội dung**: Tập trung vào quản lý tài nguyên đám mây bằng cách tiếp cận dựa trên mã:
  - **AWS CloudFormation** và cấu trúc template khai báo.
  - **AWS CDK**, cho phép xây dựng hạ tầng bằng các ngôn ngữ lập trình quen thuộc.
- **Khoảnh khắc nổi bật**: Việc xem CloudFormation và CDK được trình diễn song song giúp người tham dự phân biệt rõ ràng và biết nên chọn công cụ nào trong từng tình huống.

#### Triển khai Ứng dụng Hiện đại: Hệ sinh thái Container

- **Nội dung**: Đi sâu vào vai trò của container trong kiến trúc ứng dụng hiện đại:
  - Quản lý image với **Amazon ECR**.
  - So sánh giữa **Amazon ECS** (vận hành đơn giản) và **Amazon EKS** (tính linh hoạt cao).
  - Giới thiệu **AWS App Runner** giúp triển khai ứng dụng mà không cần quản lý hạ tầng.
- **Khoảnh khắc nổi bật**: Case study ứng dụng microservices mang đến góc nhìn rõ ràng về ưu – nhược điểm của App Runner, ECS, và EKS trong các tình huống thực tế.

#### Đóng Vòng Lặp: Khả năng Quan sát Toàn diện (Full-Stack Observability)

- **Nội dung**: Phiên cuối tập trung vào giám sát và truy vết hệ thống trong môi trường production:
  - **Amazon CloudWatch** cho metrics và logs.
  - **AWS X-Ray** cho distributed tracing đầu cuối.
  - Best practices về cảnh báo, dashboard, và quy trình trực hệ thống.
- **Khoảnh khắc nổi bật**: Phần demo tracing—theo dõi một request đi qua nhiều microservices—cho thấy cách observability phát hiện các nút thắt mà các phương pháp giám sát truyền thống khó phát hiện.

### Những Điều Học Được

#### Tự động hóa & Quy trình Pipeline

- **Xây dựng quy trình CI/CD hoàn chỉnh**: Học được cách thiết kế và vận hành một pipeline triển khai tự động hóa toàn diện, giúp tăng tốc độ release và giảm thiểu lỗi thủ công.
- **Nền tảng Infrastructure as Code**: Hiểu cách quản lý môi trường đám mây bằng các template và mã được kiểm soát phiên bản, đồng thời biết khi nào nên sử dụng CloudFormation hoặc AWS CDK.

#### Triển khai Ứng dụng Hiện đại

- **Container hóa trên AWS**: Nắm được cách lựa chọn dịch vụ container phù hợp—ECS, EKS hoặc App Runner—dựa trên kỹ năng đội ngũ, yêu cầu kiến trúc và độ phức tạp vận hành.
- **Kỹ thuật triển khai nâng cao**: Khám phá các chiến lược release an toàn như feature flags, A/B testing và progressive rollout để giảm rủi ro khi cập nhật hệ thống.

#### Độ tin cậy & Vận hành hệ thống

- **Xây dựng hệ thống có khả năng quan sát (Observability)**: Hiểu cách metrics, logs và traces phối hợp để làm rõ hành vi hệ thống và tăng tốc quá trình xử lý sự cố trong môi trường phân tán.
- **Áp dụng thực hành DevOps**: Hiểu sâu hơn về quy trình phản ứng sự cố, postmortem và xây dựng văn hóa học hỏi, cải tiến liên tục.

### Trải Nghiệm Sự Kiện

Workshop “DevOps on AWS” mang đến một ngày trải nghiệm chuyên sâu về việc xây dựng, triển khai và vận hành các ứng dụng hiện đại trên môi trường cloud.

> Tổng thể, sự kiện không chỉ cung cấp kiến thức kỹ thuật mà còn giúp tôi thay đổi cách tư duy về thiết kế ứng dụng, hiện đại hóa hệ thống và phối hợp hiệu quả hơn giữa các team.
