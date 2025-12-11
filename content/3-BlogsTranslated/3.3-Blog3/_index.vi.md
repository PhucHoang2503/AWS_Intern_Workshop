---
title: "Blog 3"
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---

# Mở rộng Amazon Q Developer CLI với Model Context Protocol (MCP) để có ngữ cảnh phong phú hơn

**Tác giả**: Brian Beach | Vào ngày 29 tháng 04 năm 2025 |
**Danh mục**: Amazon Aurora, Amazon Q Developer, Announcements, PostgreSQL compatible, RDS for PostgreSQL

Đầu ngày hôm nay, Amazon Q Developer đã thông báo hỗ trợ Model Context Protocol (MCP) trong giao diện dòng lệnh (CLI). Các nhà phát triển có thể kết nối các nguồn dữ liệu bên ngoài với Amazon Q Developer CLI bằng cách hỗ trợ MCP để có các phản hồi nhận biết ngữ cảnh (context-aware responses) tốt hơn. Bằng cách tích hợp các công cụ và lời nhắc MCP vào Q Developer CLI, bạn có thể truy cập vào danh sách mở rộng các tích hợp được xây dựng sẵn hoặc bất kỳ MCP Servers nào hỗ trợ stdio. Ngữ cảnh bổ sung này giúp Q Developer viết mã chính xác hơn, hiểu các cấu trúc dữ liệu của bạn, tạo ra các kiểm thử đơn vị (unit tests) thích hợp, tạo tài liệu cơ sở dữ liệu, và thực thi các truy vấn chính xác, tất cả đều không cần phát triển mã tích hợp tùy chỉnh. Bằng cách mở rộng Q Developer với các công cụ và lời nhắc MCP, các nhà phát triển có thể thực hiện các tác vụ phát triển nhanh hơn, tinh giản trải nghiệm nhà phát triển (developer experience). Tại AWS, chúng tôi cam kết hỗ trợ các giao thức mã nguồn mở phổ biến cho các tác nhân (agents) như Model Context Protocol (MCP) được đề xuất bởi Anthropic. Chúng tôi sẽ tiếp tục hỗ trợ nỗ lực này bằng cách mở rộng chức năng này trong các IDE plugins của Amazon Q Developer trong những tuần tới.

## Giới thiệu

Tôi luôn tìm kiếm các công cụ và công nghệ có thể tinh giản quy trình làm việc (workflow) của tôi và mở khóa các khả năng mới. Đó là lý do tại sao tôi rất hào hứng với việc bổ sung gần đây tính năng hỗ trợ Model Context Protocol (MCP) trong giao diện dòng lệnh (CLI) của Amazon Q Developer. MCP là một giao thức mở chuẩn hóa cách các ứng dụng có thể tích hợp liền mạch với LLMs (Mô hình ngôn ngữ lớn), cung cấp một cách chung để chia sẻ ngữ cảnh, truy cập các nguồn dữ liệu, và kích hoạt chức năng mạnh mẽ do AI điều khiển. Bạn có thể đọc thêm về MCP trong phần giới thiệu ([this introduction](https://modelcontextprotocol.io/docs/getting-started/intro)) này.

Q Developer đã có khả năng sử dụng các công cụ được một thời gian. Tôi đã từng thảo luận về khả năng chạy các lệnh CLI và mô tả các tài nguyên AWS. Với việc Q Developer CLI hỗ trợ các công cụ và lời nhắc MCP, giờ đây tôi có khả năng thêm các công cụ bổ sung. Ví dụ, mặc dù tôi đã có khả năng mô tả các tài nguyên AWS của mình, tôi cũng cần mô tả lược đồ cơ sở dữ liệu (database schemas), định dạng thông báo, v.v., để xây dựng một ứng dụng. Hãy cùng xem cách tôi có thể cấu hình MCP để cung cấp ngữ cảnh bổ sung này.

Trong bài viết này, tôi sẽ cấu hình một MCP server để cung cấp cho Q Developer lược đồ cơ sở dữ liệu của tôi cho một Hệ thống quản lý học tập (LMS) đơn giản mà tôi đang làm việc. Mặc dù Q Developer rất giỏi trong việc viết SQL, nhưng nó không biết lược đồ của cơ sở dữ liệu của tôi. Cấu trúc bảng và các mối quan hệ được lưu trữ trong cơ sở dữ liệu và không phải là một phần của mã nguồn (source code) dự án của tôi. Do đó, tôi sẽ sử dụng một MCP server có thể truy vấn lược đồ cơ sở dữ liệu. Cụ thể, tôi đang sử dụng phiên bản tham chiếu chính thức của PostgreSQL để kết nối với Amazon Relational Database Service (RDS) của tôi. Hãy bắt đầu nào.

## Trước Model Context Protocol

Trước khi giới thiệu hỗ trợ MCP, Q Developer CLI đã cung cấp một bộ công cụ native, bao gồm khả năng thực thi các lệnh bash, tương tác với các tệp và hệ thống tệp, và thậm chí thực hiện các cuộc gọi đến các dịch vụ AWS. Tuy nhiên, khi truy vấn cơ sở dữ liệu, CLI bị giới hạn về khả năng của nó.

Ví dụ, trước khi cấu hình MCP server, tôi đã yêu cầu Q Developer "Viết một truy vấn liệt kê các sinh viên và số lượng tín chỉ mà mỗi sinh viên đang học". Trong hình ảnh bạn có thể thấy rằng Q Developer chỉ có thể cung cấp một truy vấn SQL chung chung (generic SQL query), vì nó thiếu kiến thức cụ thể về lược đồ cơ sở dữ liệu cho LMS của tôi.

![Blog-3](/images/3-Blog/Blog-3/mcp001.png)

Mặc dù đây là một khởi đầu tuyệt vời, nhưng tôi biết rằng Q Developer có thể làm được nhiều hơn thế nếu nó biết lược đồ cơ sở dữ liệu.

## Cấu hình Model Context Protocol

Việc giới thiệu hỗ trợ MCP trong Q Developer CLI cho phép tôi dễ dàng cấu hình các MCP servers. Tôi cấu hình một hoặc nhiều MCP servers trong một tệp có tên là `mcp.json`. Tôi có thể lưu trữ cấu hình trong thư mục chính (home directory) của mình (ví dụ: `~/.aws/amazonq/mcp.json`) và nó được áp dụng cho tất cả các dự án trên máy của tôi. Ngoài ra, tôi có thể lưu trữ cấu hình trong thư mục gốc của không gian làm việc (workspace root) (ví dụ: `.amazonq/mcp.json`) để nó được chia sẻ giữa các thành viên dự án. Đây là một ví dụ về cấu hình cho PostgreSQL MCP server:

```bash
JSON
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-postgres",
        "postgresql://USERNAME:PASSWORD@HOST:5432/DBNAME"
      ]
    }
  }
}
```

Với MCP server đã được cấu hình, hãy xem Amazon Q Developer nâng cao trải nghiệm của tôi như thế nào.

## Sau Model Context Protocol

Đầu tiên, tôi bắt đầu một phiên Q Developer mới và ngay lập tức thấy được những lợi ích. Ngoài các công cụ hiện có, Q Developer giờ đây có quyền truy cập vào PostgreSQL. Điều này có nghĩa là tôi có thể dễ dàng khám phá lược đồ của cơ sở dữ liệu của mình, hiểu cấu trúc của các bảng, và thậm chí thực thi các truy vấn SQL phức tạp, tất cả đều không cần phải viết thêm bất kỳ mã tích hợp nào.

![Blog-3](/images/3-Blog/Blog-3/mcp002.png)

Hãy kiểm tra MCP server bằng cách yêu cầu Q Developer "Liệt kê các bảng cơ sở dữ liệu". Như bạn có thể thấy trong ví dụ, Q Developer giờ đây hiểu rằng tôi đang hỏi về cơ sở dữ liệu PostgreSQL và sử dụng MCP server để liệt kê ba bảng của tôi: students, courses, and enrollment.

![Blog-3](/images/3-Blog/Blog-3/mcp003.png)

Quay lại ví dụ từ phần trước của bài đăng này. Bây giờ, khi tôi hỏi Q Developer "Viết một truy vấn liệt kê các sinh viên và số lượng tín chỉ mà mỗi sinh viên đang học", nó không còn phản hồi bằng một truy vấn chung chung nữa. Thay vào đó, Q Developer trước tiên mô tả các bảng liên quan trong cơ sở dữ liệu của tôi, tạo ra truy vấn SQL thích hợp, và sau đó thực thi nó, cung cấp cho tôi kết quả mong muốn.

![Blog-3](/images/3-Blog/Blog-3/mcp004.png)

Tất nhiên, Q Developer có thể làm được nhiều hơn là chỉ viết truy vấn. Q Developer có thể sử dụng MCP server để viết mã Java truy cập cơ sở dữ liệu, tạo kiểm thử đơn vị (unit tests) cho tầng dữ liệu (data layer), lập tài liệu về cơ sở dữ liệu, và nhiều hơn nữa. Ví dụ, tôi đã yêu cầu Q Developer "Tạo một sơ đồ thực thể-quan hệ (ER - entity-relationship) sử dụng cú pháp Mermaid". Q Developer đã có thể tạo ra một biểu diễn trực quan về lược đồ cơ sở dữ liệu, giúp tôi hiểu rõ hơn về mối quan hệ giữa các thực thể khác nhau.

![Blog-3](/images/3-Blog/Blog-3/mcp005.png)

Việc tích hợp MCP vào Q Developer CLI đã tinh giản đáng kể quy trình làm việc của tôi bằng cách cho phép tôi thêm các công cụ bổ sung khi cần.

## Kết luận

Việc bổ sung hỗ trợ MCP trong Amazon Q Developer CLI cung cấp một cách chuẩn hóa để chia sẻ ngữ cảnh và truy cập các nguồn dữ liệu. Trong bài đăng này, tôi đã chứng minh cách tôi có thể sử dụng tính năng tích hợp MCP của Q Developer CLI để nhanh chóng thiết lập kết nối với cơ sở dữ liệu PostgreSQL, khám phá lược đồ, và tạo các truy vấn SQL phức tạp mà không cần phải viết thêm bất kỳ mã tích hợp nào. Trong tương lai, tôi rất hào hứng muốn xem cách bạn có thể tận dụng MCP để nâng cao hơn nữa quy trình làm việc phát triển của mình. Tôi khuyến khích bạn khám phá các khả năng của MCP ([explore the MCP capabilities](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/what-is.html)) và kho lưu trữ [AWS MCP Servers](https://github.com/awslabs/mcp) trên GitHub.

TAGS: [Developer Tools](https://aws.amazon.com/blogs/devops/tag/developer-tools/), [Development](https://aws.amazon.com/blogs/devops/tag/development/)
