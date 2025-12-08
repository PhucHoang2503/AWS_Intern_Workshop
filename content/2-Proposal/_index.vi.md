---
title: "Đề xuất Dự án"
date: 2025-09-09
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Nền tảng DocuVerse

### 1. Tóm tắt Dự án

Nền tảng **DocuVerse** là một thị trường số và trình đọc nâng cao được thiết kế để cung cấp trải nghiệm học tập tập trung và có tính tương tác cao cho người dùng thông qua các tài liệu giáo dục. Nền tảng cho phép quản trị viên và người tạo nội dung tải lên và bán các nội dung như sách điện tử (e-books), bài nghiên cứu, tài liệu hướng dẫn học tập, và các bài kiểm tra tương tác (CRUD). Đối với học viên, nền tảng cung cấp một giao diện người dùng (front-end) lấy người học làm trung tâm để mua, sắp xếp và nghiên cứu tài liệu.

Điểm khác biệt chính của nền tảng là chế độ **"Focus Mode"** (Chế độ Tập trung) tích hợp, được điều chỉnh cho việc đọc, giúp người dùng tập trung với đồng hồ Pomodoro, tích hợp Lịch Google (Google Calendar) để lên lịch học, và một giao diện không gây xao nhãng. Hơn nữa, một hệ thống ghi chú đột phá cho phép người dùng tạo các vùng tô sáng (highlights), ghi chú và các công việc cần làm được liên kết trực tiếp đến các trang hoặc đoạn văn cụ thể trong tài liệu.

### 2. Bài toán Cần giải quyết

#### Vấn đề là gì?

- **Đọc thụ động và Khả năng ghi nhớ thấp:** Học viên thường đọc tài liệu số một cách thụ động mà không có sự tương tác tích cực, dẫn đến khả năng lưu giữ thông tin kém và khó nhớ lại các khái niệm chính.
- **Thiếu sự kết nối giữa Nội dung và Hành động:** Có một sự thiếu kết nối đáng kể giữa tài liệu đọc và việc tạo ra các ghi chú học tập có thể hành động. Học viên gặp khó khăn trong việc tổ chức các vùng tô sáng và danh sách công việc liên kết trực tiếp trở lại văn bản gốc, làm cho việc ôn tập không hiệu quả.
- **Xao nhãng và Quản lý thời gian kém hiệu quả:** Môi trường đọc kỹ thuật số đầy rẫy những yếu tố gây xao nhãng. Người học thiếu các công cụ tích hợp để quản lý thời gian học một cách hiệu quả, chặn các gián đoạn và cấu trúc các buổi đọc của mình để đạt được sự tập trung và năng suất tối ưu.

#### Giải pháp

DocuVerse được xây dựng trên kiến trúc serverless của AWS để mang lại sự linh hoạt, khả năng mở rộng và hiệu quả về chi phí. Hệ thống tận dụng **AWS Amplify, Lambda, API Gateway, DynamoDB, S3, và Cognito** để tạo ra một thị trường số an toàn, chi phí thấp và hiệu suất cao. Người dùng có thể mua tài liệu, đọc chúng trong một trình xem nâng cao, quản lý các công việc học tập liên quan đến nội dung, và sử dụng các công cụ tương tác—tất cả trong một hạ tầng gốc trên đám mây (cloud-native) và khả dụng trên toàn cầu.

#### Lợi ích và Lợi tức Đầu tư:

- Phương pháp serverless giúp giảm thiểu chi phí vận hành và bảo trì thủ công.
- Tự động mở rộng quy mô dựa trên hoạt động của người dùng.
- Sử dụng Gói Miễn phí (Free Tier) của AWS và mô hình định giá theo mức sử dụng, giữ chi phí trung bình dưới 100 đô la/tháng.
- Tăng cường sự tương tác và khả năng ghi nhớ thông qua các tính năng năng suất tích hợp.

### 3. Kiến trúc Giải pháp

Kiến trúc phần lớn vẫn giữ nguyên, vì nó hoàn toàn phù hợp với trường hợp sử dụng này. Dữ liệu và nội dung được xử lý sẽ chỉ đơn giản là thay đổi.

#### Tổng quan Kiến trúc

![Nền tảng DocuVerse](/images/2-Proposal/DocuVerse.jpg)

- **Amazon S3:** Lưu trữ các tài sản số cốt lõi:
  - Sách điện tử (định dạng PDF, EPUB)
  - Dữ liệu bài kiểm tra (tệp JSON)
  - Tài liệu hướng dẫn học tập và các tài liệu khác.
- **Amazon DynamoDB:** Cơ sở dữ liệu NoSQL chính lưu trữ:
  - Hồ sơ người dùng và lịch sử mua hàng.
  - Siêu dữ liệu tài liệu (tiêu đề, tác giả, giá, mô tả, đường dẫn tệp trong S3).
  - Nội dung do người dùng tạo: Vùng tô sáng, ghi chú và danh sách công việc có tham chiếu đến trang/đoạn văn trong tài liệu.
  - Theo dõi tiến độ (ví dụ: trang đọc cuối cùng).
  - Lịch sử các phiên học trong Chế độ Tập trung.
- **AWS Lambda:** Thực thi logic nghiệp vụ của ứng dụng bao gồm:
  - Các hoạt động CRUD tài liệu.
  - Tạo URL có chữ ký trước (pre-signed URL) một cách an toàn cho S3 để cho phép người dùng đã xác thực tải xuống/xem tài liệu đã mua.
  - Quy trình ghi chú và tô sáng của người dùng.
  - Các tương tác trong Chế độ Tập trung.
- **Các dịch vụ khác (Cognito, API Gateway, CloudFront, v.v.):** Vai trò của chúng vẫn giống hệt như kế hoạch ban đầu, cung cấp xác thực, các điểm cuối API và phân phối nội dung toàn cầu.

### 4. Triển khai Kỹ thuật

#### Các Giai đoạn Triển khai

Tiến độ 3 tháng vẫn khả thi cho một Sản phẩm Khả thi Tối thiểu (MVP).

#### Yêu cầu Kỹ thuật

- **Nền tảng Học tập:** Ngăn xếp công nghệ AWS vẫn giữ nguyên. Thay đổi chính nằm ở giao diện người dùng (React/Next.js), sẽ cần một thành phần xem PDF hoặc EPUB mạnh mẽ (như react-pdf) để hiển thị tài liệu và hỗ trợ chức năng tô sáng/ghi chú.
- **Hệ thống Chế độ Tập trung:** Tính năng này được điều chỉnh một cách hoàn hảo. Đồng hồ Pomodoro và tích hợp lịch có thể được gắn với "các phiên đọc" thay vì "các phiên xem video."

### 5. Tiến độ & Các Mốc quan trọng

#### Tiến độ Dự án

Tiến độ 3 tháng ban đầu vẫn thực tế để phát triển một Sản phẩm Khả thi Tối thiểu (MVP).

1.  **Tháng 1:** Nghiên cứu AWS, tìm hiểu các thư viện xem tài liệu (ví dụ: PDF.js), và hoàn thiện các mô hình dữ liệu cho DynamoDB.
2.  **Tháng 2:** Thiết kế và điều chỉnh kiến trúc. Phát triển logic backend cho việc tải lên, mua và truy cập tài liệu an toàn.
3.  **Tháng 3:** Triển khai giao diện người dùng React với trình xem tài liệu và các tính năng ghi chú. Thử nghiệm và ra mắt.

### 6. Ước tính Ngân sách

Phần này cung cấp một ước tính chi phí hàng tháng chi tiết dựa trên các kịch bản lưu lượng truy cập thấp, trung bình và cao. Mô hình serverless đảm bảo bạn chỉ trả tiền cho những gì bạn sử dụng, làm cho nó cực kỳ hiệu quả về chi phí cho một dự án giáo dục nhỏ.

#### Các Giả định Cốt lõi

- **Kích thước Tài liệu Trung bình:** 5 MB
- **Bộ nhớ Lambda:** 256 MB
- **Kích thước Mục trong DynamoDB:** 1 KB
- **Khu vực:** us-east-1 (Bắc Virginia)

| Dịch vụ                              | Mô hình Tính giá                      | Lưu lượng Thấp (Giai đoạn Ra mắt)                               | Lưu lượng Trung bình (Tăng trưởng)                                   | Lưu lượng Cao (Đã ổn định)                                               |
| :----------------------------------- | :------------------------------------ | :-------------------------------------------------------------- | :------------------------------------------------------------------- | :----------------------------------------------------------------------- |
| **Giả định**                         |                                       | ~200 MAU <br> 500 lượt xem/tháng <br> 10.000 lệnh gọi API/tháng | ~2.000 MAU <br> 5.000 lượt xem/tháng <br> 100.000 lệnh gọi API/tháng | ~10.000 MAU <br> 50.000 lượt xem/tháng <br> 1.000.000 lệnh gọi API/tháng |
| **Amazon S3**                        | Lưu trữ + Truyền dữ liệu              | $0.10 <br> (5 GB lưu trữ, 2.5 GB truyền)                        | $1.00 <br> (20 GB lưu trữ, 25 GB truyền)                             | $10.00 <br> (100 GB lưu trữ, 250 GB truyền)                              |
| **CloudFront**                       | Truyền dữ liệu + Yêu cầu              | $0.25 <br> (Dựa trên ước tính của S3)                           | $2.20 <br> (Rẻ hơn S3 direct)                                        | $21.50 <br> (Phần lớn chi phí)                                           |
| **AWS Lambda**                       | Yêu cầu + Thời gian (GB-s)            | $0.00 <br> (Trong Gói Miễn phí)                                 | $0.00 <br> (Trong Gói Miễn phí)                                      | $1.50 <br> (Vượt Gói Miễn phí một chút)                                  |
| **API Gateway**                      | Yêu cầu                               | $0.00 <br> (Trong Gói Miễn phí)                                 | $0.00 <br> (Trong Gói Miễn phí)                                      | $1.00 <br> (1 triệu yêu cầu với $1/triệu)                                |
| **DynamoDB**                         | Đơn vị Đọc/Ghi (Theo yêu cầu)         | $0.00 <br> (Trong Gói Miễn phí)                                 | $0.50 <br> (Sử dụng ít)                                              | $5.00 <br> (Nhiều ghi chú, đọc, ghi hơn)                                 |
| **Amazon Cognito**                   | Người dùng Hoạt động Hàng tháng (MAU) | $0.00 <br> (Miễn phí tới 50k MAU)                               | $0.00 <br> (Miễn phí tới 50k MAU)                                    | $0.00 <br> (Miễn phí tới 50k MAU)                                        |
| **Route 53**                         | Hosted Zone                           | $0.50 (Cố định)                                                 | $0.50 (Cố định)                                                      | $0.50 (Cố định)                                                          |
| **AWS WAF**                          | Cơ bản + Quy tắc + Yêu cầu            | $6.00                                                           | $6.10                                                                | $7.00                                                                    |
| **CloudWatch**                       | Logs, Metrics, Alarms                 | $0.00 <br> (Trong Gói Miễn phí)                                 | $0.50 <br> (Tăng ghi log)                                            | $3.00 <br> (Giám sát lưu lượng cao)                                      |
| **Tổng Chi phí Ước tính Hàng tháng** |                                       | **~ $7**                                                        | **~ $11**                                                            | **~ $50**                                                                |

### 7. Đánh giá Rủi ro

#### Ma trận Rủi ro

| Loại Rủi ro                                  | Mô tả                                                                                                                                                              | Mức độ Ảnh hưởng | Xác suất   |
| :------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------- | :--------- |
| **Vi phạm Bảo mật**                          | Truy cập trái phép vào dữ liệu người dùng (PII, lịch sử mua hàng), các chức năng quản trị, hoặc nội dung tài liệu độc quyền.                                       | Cao              | Thấp       |
| **Vi phạm Bản quyền & Phân phối Trái phép**  | Người dùng tải xuống tài liệu đã mua và chia sẻ chúng bất hợp pháp trên các nền tảng khác, làm suy yếu mô hình kinh doanh cốt lõi.                                 | Cao              | Trung bình |
| **Vượt Ngân sách**                           | Hóa đơn AWS hàng tháng vượt quá đáng kể ngân sách dự kiến do một cuộc tấn công DDoS, một lỗi (ví dụ: vòng lặp Lambda vô hạn), hoặc cấu hình sai.                   | Trung bình       | Trung bình |
| **Dịch vụ Ngưng hoạt động / Không khả dụng** | Nền tảng trở nên không thể truy cập do một lần triển khai thất bại, lỗi hạ tầng, hoặc một lỗi nghiêm trọng, ngăn cản người dùng truy cập hoặc mua nội dung.        | Trung bình       | Thấp       |
| **Mất Dữ liệu**                              | Xóa dữ liệu quan trọng một cách vô tình hoặc cố ý, chẳng hạn như tài khoản người dùng, hồ sơ mua hàng trong DynamoDB, hoặc các tài liệu gốc được lưu trữ trong S3. | Cao              | Rất thấp   |
| **Suy giảm Hiệu suất**                       | Ứng dụng trở nên chậm và không phản hồi (ví dụ: tải tài liệu chậm, thời gian phản hồi API dài), dẫn đến trải nghiệm người dùng kém.                                | Trung bình       | Thấp       |

#### Chiến lược Giảm thiểu Rủi ro (Làm thế nào để ngăn chặn vấn đề)

Đây là các biện pháp chủ động được thực hiện trong giai đoạn phát triển và vận hành để giảm xác suất và/hoặc tác động của các rủi ro đã xác định.

##### Vi phạm Bảo mật

1.  **Nguyên tắc Đặc quyền Tối thiểu (IAM Least Privilege):** Cấp quyền trên cơ sở "cần biết". Các hàm Lambda chỉ nên có quyền truy cập vào các tài nguyên AWS cụ thể mà chúng yêu cầu.
2.  **WAF & Xác thực Dữ liệu Đầu vào:** Sử dụng AWS WAF để chặn các cuộc tấn công web phổ biến (SQLi, XSS). Xác thực và làm sạch tất cả dữ liệu đầu vào của người dùng ở cả frontend và trong các hàm Lambda.
3.  **Xác thực An toàn:** Tận dụng Amazon Cognito để quản lý người dùng, bao gồm việc thực thi các chính sách mật khẩu mạnh và kích hoạt Xác thực Đa yếu tố (MFA).
4.  **Lưu trữ An toàn:** Giữ các bucket S3 ở chế độ riêng tư theo mặc định. Quyền truy cập vào tài liệu chỉ nên được cấp thông qua các URL có chữ ký trước (pre-signed URL) tạm thời, an toàn do Lambda tạo ra.

##### Vi phạm Bản quyền

1.  **Sử dụng URL có chữ ký trước của S3:** Không bao giờ để lộ các liên kết đối tượng S3 trực tiếp. Backend sẽ tạo ra các URL chỉ sử dụng một lần, có thời hạn ngắn cho người dùng đã xác thực để xem/tải xuống nội dung đã mua, làm cho các liên kết không thể chia sẻ được.
2.  **Ưu tiên Xem trong Ứng dụng:** Thiết kế trải nghiệm người dùng để khuyến khích việc đọc trong trình xem tài liệu an toàn của nền tảng thay vì tải tệp xuống.
3.  **Đóng dấu bản quyền Động (Dynamic Watermarking - Phạm vi Tương lai):** Như một biện pháp nâng cao hơn, đóng dấu động email của người mua hoặc một ID giao dịch duy nhất lên các trang tài liệu khi tải xuống để ngăn cản việc chia sẻ.

##### Vượt Ngân sách

1.  **AWS Budgets & Cảnh báo:** Cấu hình cảnh báo thanh toán trong AWS Budgets để gửi thông báo khi chi tiêu vượt quá các ngưỡng được xác định trước (ví dụ: ở mức 50% và 80% của ngân sách 100 đô la).
2.  **Biện pháp Bảo vệ Lambda:** Đặt thời gian chờ và giới hạn bộ nhớ hợp lý cho tất cả các hàm Lambda để ngăn mã chạy không kiểm soát gây ra chi phí cao.
3.  **Lưu vào Bộ nhớ đệm (Caching) với CloudFront:** Sử dụng CloudFront để lưu vào bộ nhớ đệm các tài sản tĩnh và các tài liệu phổ biến tại các điểm biên, giảm chi phí truyền dữ liệu ra khỏi S3.

##### Dịch vụ Ngưng hoạt động

1.  **Tận dụng các Dịch vụ được Quản lý:** Dựa vào tính sẵn sàng cao của các dịch vụ cốt lõi của AWS (S3, DynamoDB, Lambda), vốn có khả năng phục hồi và chạy trên nhiều Vùng sẵn sàng (Multi-AZ).
2.  **Quy trình CI/CD Tự động:** Triển khai một quy trình tích hợp/phân phối liên tục (CI/CD) với các bài kiểm tra tự động. Điều này giúp phát hiện lỗi sớm và ngăn mã lỗi đến môi trường sản xuất.
3.  **Triển khai Canary hoặc Blue/Green:** Sử dụng các chiến lược triển khai định tuyến một phần nhỏ lưu lượng truy cập đến phiên bản mới trước khi triển khai toàn bộ, cho phép cập nhật an toàn, không có thời gian chết.

##### Mất Dữ liệu

1.  **Bật Versioning trên S3:** Kích hoạt tính năng quản lý phiên bản trên bucket S3 nơi lưu trữ tài liệu. Điều này giữ lại một lịch sử đầy đủ của tất cả các đối tượng, bảo vệ chống lại việc ghi đè và xóa vô tình.
2.  **Khôi phục tại một Thời điểm (PITR) cho DynamoDB:** Kích hoạt PITR trên tất cả các bảng DynamoDB quan trọng. Điều này cho phép bạn khôi phục một bảng về bất kỳ giây nào trong 35 ngày trước đó.
3.  **Chính sách Xóa Hạn chế:** Sử dụng các chính sách IAM và chính sách bucket S3 để từ chối rõ ràng các hành động xóa đối với dữ liệu sản xuất, ngoại trừ bởi một vai trò quản trị có đặc quyền cao.

##### Suy giảm Hiệu suất

1.  **Thiết kế Cơ sở dữ liệu Hiệu quả:** Thiết kế các bảng DynamoDB với các khóa phân vùng và chỉ mục (GSI) phù hợp để đảm bảo hiệu suất truy vấn nhanh, có thể mở rộng và tránh các lần quét bảng chậm, tốn kém.
2.  **Giám sát Hiệu suất:** Sử dụng Amazon CloudWatch để giám sát các chỉ số chính như độ trễ của API Gateway, thời gian thực thi của Lambda, và dung lượng đọc/ghi của DynamoDB. Đặt cảnh báo cho các bất thường về hiệu suất.
3.  **Kiểm tra Tải (Load Testing):** Trước khi ra mắt và sau các bản cập nhật tính năng lớn, thực hiện kiểm tra tải để xác định các điểm nghẽn hiệu suất dưới lưu lượng người dùng mô phỏng.

#### Kế hoạch Dự phòng (Làm gì khi sự cố xảy ra)

Đây là các kế hoạch phản ứng chi tiết các bước cần thực hiện ngay sau khi một rủi ro xảy ra để giảm thiểu thiệt hại và khôi phục dịch vụ.

**Nếu phát hiện Vi phạm Bảo mật...**

1.  **Cô lập:** Ngay lập tức xoay vòng tất cả các thông tin xác thực bị lộ (khóa IAM, mật khẩu cơ sở dữ liệu) và tạm thời vô hiệu hóa các tài khoản người dùng hoặc thành phần hệ thống bị ảnh hưởng.
2.  **Điều tra:** Phân tích các bản ghi của CloudTrail, CloudWatch và API Gateway để xác định phạm vi và nguyên nhân gốc rễ của vụ vi phạm.
3.  **Khắc phục:** Vá lỗ hổng đã cho phép vụ vi phạm xảy ra.
4.  **Thông báo:** Thông báo cho người dùng bị ảnh hưởng và các cơ quan quản lý theo yêu cầu của pháp luật, minh bạch về sự cố và các bước đã thực hiện.

**Nếu Chi phí Tăng đột biến...**

1.  **Phân tích:** Sử dụng AWS Cost Explorer để ngay lập tức xác định dịch vụ nào đang gây ra việc vượt chi phí.
2.  **Giảm thiểu:** Nếu đó là một cuộc tấn công DDoS, hãy siết chặt các quy tắc WAF. Nếu đó là một lỗi trong một hàm Lambda, hãy vô hiệu hóa hàm đó hoặc quay lui bản triển khai.
3.  **Quay lui (Rollback):** Sử dụng quy trình CI/CD để triển khai lại phiên bản ổn định cuối cùng của hạ tầng và mã ứng dụng.

**Nếu Dịch vụ bị Ngưng hoạt động...**

1.  **Kiểm tra Trạng thái AWS:** Đầu tiên, xác minh Bảng điều khiển Tình trạng Dịch vụ AWS (AWS Service Health Dashboard) để loại trừ sự cố khu vực của AWS.
2.  **Phân loại sự cố:** Kiểm tra các cảnh báo và bản ghi của CloudWatch để xác định thành phần bị lỗi (ví dụ: một hàm Lambda cụ thể, điểm cuối API Gateway).
3.  **Quay lui:** Nếu sự cố ngừng hoạt động do một lần triển khai gần đây, hãy kích hoạt quay lui ngay lập tức về phiên bản ổn định trước đó.
4.  **Thông báo:** Cập nhật cho người dùng thông qua một trang trạng thái hoặc mạng xã hội về sự cố ngừng hoạt động và cung cấp thời gian dự kiến để giải quyết.

**Nếu Dữ liệu bị Mất/Hỏng...**

1.  **Ngừng Ghi:** Ngay lập tức dừng mọi quy trình ghi vào kho dữ liệu bị ảnh hưởng để ngăn chặn thêm hư hỏng.
2.  **Khôi phục:** Đối với tài liệu, sử dụng S3 Versioning để khôi phục đối tượng đã xóa. Đối với dữ liệu người dùng/mua hàng, sử dụng DynamoDB Point-In-Time Recovery để khôi phục bảng về thời điểm ngay trước khi sự cố xảy ra.
3.  **Phân tích sau sự cố (Post-Mortem):** Tiến hành một cuộc điều tra kỹ lưỡng để hiểu nguyên nhân gốc rễ và thực hiện các biện pháp phòng ngừa mới.

### 8. Kết quả Dự kiến

#### Cải tiến về Kỹ thuật:

- Một nền tảng serverless, có khả năng mở rộng hoàn toàn để bán và đọc tài liệu số với chi phí vận hành tối thiểu.
- Xác thực an toàn và một hệ thống mạnh mẽ để bảo vệ các tài sản số.
- Cải thiện sự tương tác của người dùng thông qua các công cụ ghi chú và tập trung tương tác trong tài liệu.

#### Giá trị Lâu dài:

- Hoạt động bền vững với chi phí dưới 50 đô la/tháng, ngay cả với hàng nghìn người dùng hoạt động.
- Hỗ trợ một lượng lớn lượt xem tài liệu và yêu cầu API hàng ngày.
- Sẵn sàng cho các tính năng dựa trên AI trong tương lai như tóm tắt tài liệu hoặc đề xuất nội dung cá nhân hóa.
- Một hệ thống mạnh mẽ để liên kết các ghi chú và công việc với các trang hoặc phần cụ thể trong tài liệu, tạo ra một công cụ học tập mạnh mẽ.
