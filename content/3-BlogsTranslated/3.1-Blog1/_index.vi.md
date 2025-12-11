---
title: "Blog 1"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Tăng cường khả năng quản lý với các quy tắc thực thi siêu dữ liệu (metadata enforcement rules) trên Amazon SageMaker

**_Tác giả_**: Pradeep Misra, Ramesh H Singh, and Sandhya Edupuganti | ngày 28/3/2025 |

**_Danh mục_**: **Amazon SageMaker Lakehouse**, **Analytics**, **Announcements**, **Technical How-to**

**SageMaker** thế hệ tiếp theo kết hợp học máy (machine learning) cùng với khả năng phân tích (analytics) được áp dụng rộng rãi của AWS, mang lại trải nghiệm tích hợp cùng với khả năng truy cập thống nhất (unified access) vào toàn bộ dữ liệu. **Amazon SageMaker Lakehouse** hỗ trợ truy cập dữ liệu hợp nhất (unified data), trong khi **Amazon SageMaker Catalog**, được xây dựng trên Amazon DataZone, cung cấp các tính năng quản lý và danh mục để đáp ứng nhu cầu bảo mật doanh nghiệp. Amazon SageMaker Catalog ngày nay đã hỗ trợ quy tắc siêu dữ liệu (metadata rules) cho phép các tổ chức thực thi các tiêu chuẩn metadata trên toàn bộ quy trình xuất bản và đăng ký dữ liệu.

Một quy tắc (rule) là một thoả thuận chính thức nhằm thực thi các yêu cầu metadata cụ thể trên các workflow của người dùng (ví dụ: xuất bản assets lên catalog, yêu cầu quyền truy cập dữ liệu) qua cổng thông tin **Amazon SageMaker Unified Studio**. Ví dụ, một quy tắc thực thi metadata có thể chỉ định các thông tin bắt buộc khi tạo yêu cầu đăng ký (**subscription request**) hoặc xuất bản một tài sản dữ liệu (**publishing a data asset**) hay sản phẩm dữ liệu vào catalog, đảm bảo sự tuân thủ với các tiêu chuẩn của tổ chức. Các quy tắc metadata cũng cho phép tạo các quy trình phê duyệt tùy chỉnh (custom approval workflows) cho việc đăng ký tài sản, sử dụng metadata đã thu thập để hỗ trợ quyết định truy cập hoặc tự động phê duyệt — bên ngoài SageMaker.

Bằng cách tiêu chuẩn hóa việc quản lý metadata, Amazon SageMaker Catalog giúp khách hàng đáp ứng yêu cầu tuân thủ, nâng cao khả năng sẵn sàng kiểm toán, và tối ưu hóa quy trình truy cập dữ liệu để đạt hiệu quả và kiểm soát tốt hơn. Một trong những khách hàng tiêu biểu là Amazon Shipping Tech, đơn vị sử dụng SageMaker Catalog cho việc lập danh mục, khám phá, chia sẻ và quản trị trong toàn bộ hệ sinh thái dữ liệu của họ:

> “Chúng tôi đang xây dựng một Hệ sinh thái Phân tích (Analytics Ecosystem) để thúc đẩy khả năng khám phá trong toàn tổ chức — nhưng nếu không có metadata nhất quán, ngay cả dữ liệu giá trị nhất cũng có thể bị bỏ quên. Tính năng này giúp nhiều nhóm có thể chủ động đóng góp vào việc quản lý metadata với khuôn khổ quản trị phù hợp. Nó cho phép chúng tôi thiết lập các tiêu chuẩn rõ ràng cho người tạo dữ liệu, đồng thời hợp lý hóa việc thu thập thông tin cần thiết cho các yêu cầu đăng ký — mà không cần thêm biểu mẫu phụ.
> Bằng cách thực thi các thuộc tính metadata tiêu chuẩn, chúng tôi cải thiện khả năng khám phá dữ liệu, bổ sung ngữ cảnh cho từng yêu cầu, và tăng cường hỗ trợ cho các phân tích cũng như các giải pháp GenAI.”
>
> — Saurabh Pandey, Kỹ sư Dữ liệu Cấp cao (Principal Data Engineer) tại Amazon Shipping Tech

### Các use-case mẫu

Các quy tắc metadata có thể hữu ích trong các trường hợp sau:

- Nhà sản xuất dữ liệu tại một công ty ô tô đang chuẩn bị xuất bản một tập dữ liệu mới vào danh mục dữ liệu của tổ chức (**data catalog**). Chủ sở hữu miền (domain owner) của miền “Automotive” yêu cầu người xuất bản phải bao gồm các trường metadata như Năm sản xuất (Model Year), Khu vực (Region) và Trạng thái tuân thủ (Compliance Status). Trước khi tập dữ liệu được xuất bản, hệ thống tự động kiểm tra để đảm bảo các trường này được điền đúng theo tiêu chuẩn đã định sẵn.

- Người tiêu dùng đang yêu cầu truy cập tài sản dữ liệu trong SageMaker. Để đáp ứng tiêu chuẩn của tổ chức và phục vụ cho việc kiểm toán, họ phải hoàn tất yêu cầu đăng ký, điền vào một biểu mẫu chi tiết bao gồm mục đích dự án, đồng thời đính kèm liên kết email chứa bằng chứng phê duyệt trước và chứng chỉ đào tạo tuân thủ, khi yêu cầu quyền truy cập vào sản phẩm dữ liệu tài chính. Chủ sở hữu dữ liệu sẽ xem xét yêu cầu và đảm bảo rằng tất cả siêu dữ liệu bắt buộc đã được cung cấp trước khi cấp quyền truy cập.

### Các lợi ích chính

Một số lợi ích chính của metadata enforcement rules gồm có:

- **Tăng cường kiểm soát cho chủ sở hữu miền** – Admins có thể ép buộc người dùng cung cấp thêm các metadata fields trong quy trình đăng ký và xuất bản mà người dùng dữ liệu phải tuân thủ. Việc này giúp đảm bảo review kỹ lưỡng và tuân thủ chuẩn của tổ chức.

- **Hỗ trợ workflow tùy chỉnh** – Bạn có thể tạo các workflows tuỳ chỉnh cho việc xử lý đăng ký đối với các asset chưa được quản lí, bằng cách thu thập metadata từ người tiêu dùng dữ liệu. Thông tin này có thể dùng để cấu hình quyền truy cập hoặc đáp ứng các yêu cầu kinh doanh cụ thể.

- Trong bài đăng này, chúng tôi sẽ hướng dẫn bạn thực hiện hai workflow: thiết lập quy tắc thực thi metadata cho một miền cụ thể và xuất bản asset hoặc sản phẩm dữ liệu trong catalog, và thiết lập quy tắc thực thi metadata cho một miền cụ thể và đăng ký asset hoặc sản phẩm dữ liệu thuộc sở hữu của một dự án trong miền đó.

## Tổng quan giải pháp: Thực thi metadata khi xuất bản

Trong phần giải pháp này, chúng ta sẽ đi qua hai workflow: Thiết lập quy tắc thực thi metadata cho việc xuất bản (publishing), và thiết lập quy tắc thực thi metadata cho việc đăng ký (subscription)

### Điều kiện tiên quyết

Để thực hiện theo bài viết này, bạn cần có một miền SageMaker Unified Studio đã được thiết lập, với quyền chủ sở hữu miền (domain owner) hoặc chủ sở hữu đơn vị miền (domain unit owner). Để biết hướng dẫn chi tiết, tham khảo tài liệu **Getting started**.

## Thiết lập thực thi metadata để xuất bản

Trong phần này, bạn sẽ học cách tạo metadata rules cho một domain cụ thể với tư cách domain admin, chúng tôi cũng giải thích sẽ điều gì xảy ra khi bạn xuất bản asset hoặc sản phẩm dữ liệu vào catalog có áp dụng các quy tắc này.

### Tạo domain unit cho nhóm Marketing

Với vai trò domain admin, hoàn thành các bước sau:

1. Tại giao diện SageMaker Unified Studio, mở menu **Govern** trên thanh điều hướng và chọn **Domain units**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image001.png)
2. Chọn **CREATE DOMAIN UNIT**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image003.png)
3. Cung cấp thông tin như hình dưới đây và chọn CREATE DOMAIN UNIT.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image005.png)
   Bạn có thể thấy domain unit đã được tạo như hình dưới đây.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image007.png)

### Kích hoạt chính sách tạo metadata form trong domain unit Marketing

Thực hiện các bước sau:

1. Đi tới tab **AUTHORIZATION POLICIES** trong domain unit **Marketing** và chọn **Metadata form creation policy**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image009.png)
2. Chọn **ADD POLICY GRANT**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image011.png)
3. Chọn tất cả project trong domain unit, sau đó chọn một chính sách trợ cấp.
4. Bạn cũng có thể chọn các project cụ thể được phép tạo metadata forms.
5. Chọn **ADD POLICY GRANT**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image013.png)
   Bây giờ, bạn có thể thấy chính sách đã được tạo cho domain unit **Marketing**.

### Tạo metadata form để áp dụng (enforce) cho các asset trước khi publish

Để tạo metadata form, thực hiện các bước sau:

1. Tại project `publish-1` , chọn **Metadata entities** trong phần **Project catalog** ở thanh điều hướng.
2. Trên tab **Metadata forms**, chọn **CREATE METADATA** FORM.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image017.png)
3. Nhập thông tin gồm display name, technical name, và mô tả.
4. Chọn **CREATE METADATA FORM**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image019.png)
5. Sau khi tạo form, bạn có thể chọn **CREATE FIELD** để xác định (enforce) các trường bắt buộc phải có trong tất cả các asset được publish.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image021.png)
6. Cung cấp thông tin như hình dưới đây.
7. Chọn **Searchable**, **Required**, và **Publishing** vì những trường này là bắt buộc trước khi xuất bản.
8. Chọn **CREATE FIELD**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image023.png)
9. Thêm một trường khác như hình dưới đây.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image025.png)

Cả hai trường được tạo với hành động **Publishing** sẽ yêu cầu nhập giá trị trước khi xuất bản lên catalog.
![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image027.png)

### Tạo rules cho việc publish asset

Thực hiện các bước sau:

1. Trong dự án `publish-1`, ở phần Domain Management trên thanh điều hướng, chọn Domain units.
2. Chọn **Marketing domain unit**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image029.png)
3. Ở tab **Rules**, chọn **ADD**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image031.png)
4. Tạo cấu hình rule với các chi tiết như trong ảnh dưới đây và thêm metadata form đã được tạo ở bước trước.
5. Bạn có thể chọn phạm vi áp dụng theo loại asset và dự án.
6. Chọn **ADD RULE** để tạo rule.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image033.png)
   Quy tắc thực thi xuất bản (publishing enforcement rule) có tên `publish_rules` đã được tạo.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image035.png)

### Tạo một project trong Marketing domain unit

Tạo một project có tên `publish-1` trong Marketing domain unit. Để biết cách tạo project, hãy tham khảo phần **Create a project**.

### Tạo một asset trong project

Các rule sẽ hoạt động trên những asset được quản lý bởi SageMaker Catalog hoặc trên custom assets. Để tạo một asset, hãy thực hiện các bước sau::

1. Trong project `publish-1`, ở thanh điều hướng, chọn **Assets** trong phần **Project catalog**.
2. Trong menu **Create** (thả xuống), chọn **Create asset**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image037.png)
3. Nhập tên asset và mô tả, sau đó chọn Next.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image039.png)
   Đối với giải pháp này, bạn sẽ tạo một **Amazon Simple Storage Service** (Amazon S3) object collection.
4. Ở mục **Asset type**, chọn S3 object collection.
5. Ở mục **S3 location ARN**, nhập Amazon Resource Name (ARN) của S3 object.
6. Chọn **Next**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image041.png)
7. Chọn **Create**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image043.png)
   Asset có tên `marketing_campaign_asset` hiện đã được tạo. Tuy nhiên, đây vẫn chỉ là một inventory asset và chưa được xuất bản lên catalog.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image045.png)

### Thực thi quy tắc xuất bản (Publish rules enforcement)

Chi tiết của asset hiện cho thấy rằng các giá trị bắt buộc đang bị thiếu trong biểu mẫu bắt buộc (mandatory form) có tên `Publish_form`.
![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image047.png)
Bạn có thể thử xuất bản mà không điền các trường bắt buộc, và hệ thống sẽ hiển thị lỗi để thực thi các quy tắc metadata khi xuất bản, như minh họa trong ảnh dưới đây.
![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image049.png)
Để khắc phục vấn đề, hãy chỉnh sửa giá trị trong metadata form và cung cấp các thông tin bắt buộc.
![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image051.png)
Nhập thông tin cho các trường được yêu cầu, sau đó chọn **SAVE**.
![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image053.png)
Chọn **PUBLISH ASSET** , và asset sẽ được xuất bản lên catalog.
![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image055.png)
Bạn có thể thấy rằng asset đã được xuất bản với các trường bắt buộc đã được áp dụng theo quy tắc (rules).
![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image057.png)

## Thiết lập quy tắc bắt buộc metadata cho yêu cầu đăng ký (subscription requests)

Trong phần này, chúng tôi sẽ hướng dẫn cho bạn cách thiết lập metadata rules cho một domain cụ thể với vai trò là domain admin. Đồng thời, bài viết cũng giải thích điều gì sẽ xảy ra khi bạn đăng ký (subscribe) một asset hoặc data product khi các quy tắc này được áp dụng.

### Tạo quy tắc cho việc đăng ký asset

Thực hiện các bước sau:

1. Điều hướng đến project đã được sử dụng ở phần trước và chọn **Metadata entities** dưới mục **Project catalog** trong thanh điều hướng.
2. Trong tab **Metadata forms**, chọn **CREATE METADATA FORM** để tạo một form mới.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image059.png)
3. Nhập tên form và mô tả, sau đó chọn **CREATE METADATA FORM**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image061.png)
4. Thêm các trường vào form bằng cách chọn CREATE FIELD và bật tùy chọn Enabled.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image063.png)
5. Thêm một trường để người đăng ký (subscribers) để giải thích mục đích sử dụng (use case) khi gửi yêu cầu truy cập.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image065.png)

### Tạo quy tắc (rules) cho việc đăng ký asset

Thực hiện các bước sau:

1. Trên trang project, chọn Domain units trong phần Domain Management ở thanh điều hướng.
2. Chọn **Marketing** domain unit.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image067.png)
   Chúng ta đã có một quy tắc xuất bản (publishing rule).
3. Trong tab **Rules**, chọn **ADD** để thêm một quy tắc mới.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image069.png)
4. Cung cấp thông tin chi tiết cho quy tắc mới.
5. Chỉ định Action là **Subscription request**.
6. Thêm metadata form đã tạo ở các bước trước (`Subscribe_form`).
7. Chọn phạm vi áp dụng (scope) và các project cần thi hành (enforcement) như trong ảnh minh họa.
8. Chọn **ADD RULE**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image071.png)
   Bạn sẽ thấy rằng quy tắc áp dụng cho việc đăng ký (subscription enforcement rule) đã được tạo thành công.

### Đăng ký asset

Thực hiện các bước sau để đăng ký tài sản:

1. Trên trang project, điều hướng đến marketing asset.
2. Chọn **SUBSCRIBE**.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image073.png)
   Form Subscribe bây giờ đã được đính kèm vào yêu cầu (request) để người dùng cung cấp thông tin cần thiết.
   ![Blog-1](/images/3-Blog/Blog-1/BDB-5126-image075.png)
   Sau khi người dùng dữ liệu (data consumer) gửi yêu cầu đăng ký (subscription request), nhà sản xuất dữ liệu (data producer) sẽ nhận được yêu cầu kèm theo metadata được cung cấp — chẳng hạn như **Use Case**. Điều này cho phép nhà sản xuất review yêu cầu trước khi cấp quyền truy cập.

## Dọn dẹp

Để tránh phát sinh thêm chi phí, hãy xóa Amazon SageMaker domain. Tham khảo quy trình trong phần Delete domains để thực hiện.

## Kết luận

Trong bài viết này, chúng ta đã tìm hiểu về metadata rules và cách triển khai chúng cho cả quy trình xuất bản (publishing) và đăng ký (subscribing) asset trên các domain khác nhau, qua đó minh họa các thực tiễn quản trị metadata (metadata governance) hiệu quả.

Metadata enforcement rule mới trong Amazon SageMaker giúp tăng cường khả năng quản trị dữ liệu (data governance) bằng cách cho phép chủ sở hữu domain unit thiết lập các yêu cầu metadata rõ ràng cho người dùng dữ liệu, tối ưu hóa tính nhất quán của catalog và nâng cao quy trình quản trị dữ liệu khi xử lý yêu cầu truy cập (access request). Tính năng này giúp các tổ chức tuân thủ tiêu chuẩn metadata nội bộ, tùy chỉnh quy trình làm việc (custom workflows), và mang lại trải nghiệm quản trị dữ liệu thống nhất, có kiểm soát.

Tính năng hiện được hỗ trợ tại các AWS Commercial Regions nơi Amazon SageMaker đã khả dụng. Để bắt đầu với metadata rules:
Đọc [user guide](https://docs.aws.amazon.com/sagemaker-unified-studio/latest/userguide/metadata-rules-publishing.html) về cách tạo rules trong quy trình publishing

Đọc [user guide](https://docs.aws.amazon.com/sagemaker-unified-studio/latest/userguide/metadata-rules-subscription.html) về cách tạo rules trong subscription requests

---

### Về các tác giả

![Blog-1](/images/3-Blog/Blog-1/pkmisra-1.png)
**Pradeep Misra** là Kiến trúc sư Giải pháp Phân tích cấp cao (Principal Analytics Solutions Architect) tại AWS. Anh làm việc cùng nhiều nhóm trong Amazon để thiết kế và xây dựng các nền tảng phân tích dữ liệu và AI/ML hiện đại, phân tán. Anh đam mê giải quyết các thách thức của khách hàng thông qua dữ liệu, phân tích và trí tuệ nhân tạo/máy học (AI/ML). Ngoài công việc, Pradeep thích khám phá những địa điểm mới, thưởng thức ẩm thực đa dạng và chơi board game cùng gia đình. Anh cũng yêu thích làm các thí nghiệm khoa học, lắp ráp LEGO và xem anime cùng các con gái của mình.

![Blog-1](/images/3-Blog/Blog-1/RAMESH-H-SINGH-PHONE-TOOL.png)
**Ramesh H Singh** là Quản lý sản phẩm kỹ thuật cấp cao (Senior Product Manager Technical – External Services) tại AWS, trụ sở Seattle, Washington, hiện đang làm việc trong nhóm Amazon SageMaker. Anh đam mê xây dựng các sản phẩm ML/AI và phân tích hiệu năng cao, giúp khách hàng doanh nghiệp đạt được các mục tiêu chiến lược thông qua công nghệ tiên tiến. Bạn có thể kết nối với anh trên LinkedIn.

![Blog-1](/images/3-Blog/Blog-1/sandhyae.png)
**Sandhya Edupuganti** là Trưởng nhóm Kỹ thuật cấp cao (Senior Engineering Leader), hiện đang dẫn dắt dự án Amazon DataZone (hay còn gọi là SageMaker Catalog). Cô làm việc tại khu vực Seattle Metro và đã gắn bó với Amazon hơn 17 năm, nơi cô từng lãnh đạo nhiều sáng kiến chiến lược trong các mảng như Amazon Advertising, Amazon Retail, mở rộng thị trường khu vực Mỹ Latinh, và AWS Analytics.
