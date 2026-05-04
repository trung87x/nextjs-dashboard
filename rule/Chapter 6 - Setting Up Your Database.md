# Chương 6: Thiết lập cơ sở dữ liệu

Trước khi có thể tiếp tục xây dựng bảng điều khiển, bạn sẽ cần một số dữ liệu. Trong chương này, bạn sẽ thiết lập một cơ sở dữ liệu PostgreSQL từ một trong các [tích hợp của Vercel](https://vercel.com/marketplace?category=storage).

### Trong chương này...
Dưới đây là các chủ đề chúng ta sẽ đề cập:
- Đẩy dự án của bạn lên GitHub.
- Thiết lập tài khoản Vercel và liên kết kho lưu trữ GitHub của bạn để triển khai và xem trước tức thì.
- Tạo và liên kết dự án của bạn với cơ sở dữ liệu Postgres.
- Đổ dữ liệu mẫu (seed) vào cơ sở dữ liệu.

## Tạo một kho lưu trữ trên GitHub

Để bắt đầu, hãy đẩy kho lưu trữ của bạn lên GitHub nếu bạn chưa làm như vậy. Điều này sẽ giúp việc thiết lập cơ sở dữ liệu và triển khai dễ dàng hơn.

Nếu bạn mới sử dụng GitHub, chúng tôi khuyên bạn nên sử dụng [GitHub Desktop App](https://desktop.github.com/) để đơn giản hóa quy trình làm việc.

## Tạo tài khoản Vercel

Truy cập [vercel.com/signup](https://vercel.com/signup) để tạo tài khoản. Chọn gói "hobby" miễn phí. Chọn **Continue with GitHub** để kết nối tài khoản GitHub và Vercel của bạn.

## Kết nối và triển khai dự án

Tiếp theo, bạn sẽ được đưa đến màn hình nơi bạn có thể chọn và nhập (import) kho lưu trữ GitHub mà bạn vừa tạo. Đặt tên cho dự án và nhấp vào **Deploy**.

Bằng cách kết nối kho lưu trữ GitHub, mỗi khi bạn đẩy (push) các thay đổi lên nhánh `main`, Vercel sẽ tự động triển khai lại ứng dụng của bạn mà không cần cấu hình thêm.

## Tạo cơ sở dữ liệu Postgres

Tiếp theo, để thiết lập cơ sở dữ liệu, nhấp vào **Continue to Dashboard** và chọn tab **Storage** từ bảng điều khiển dự án của bạn. Chọn **Create Database**. Chọn nhà cung cấp Postgres (ví dụ: Vercel Postgres) và nhấp vào **Continue**.

Chọn khu vực (region) và gói lưu trữ của bạn. Khu vực mặc định cho tất cả các dự án Vercel là Washington D.C (iad1), chúng tôi khuyên bạn nên chọn khu vực này nếu có sẵn để giảm [độ trễ (latency)](https://developer.mozilla.org/en-US/docs/Web/Performance/Understanding_latency) cho các yêu cầu dữ liệu.

Sau khi kết nối, hãy điều hướng đến tab **.env.local**, nhấp vào **Show secret** và **Copy Snippet**. Hãy chắc chắn rằng bạn đã hiển thị các bí mật trước khi sao chép chúng.

Mở trình chỉnh sửa mã của bạn và đổi tên tệp `.env.example` thành `.env`. Dán nội dung đã sao chép từ Vercel vào đó.

**Quan trọng:** Hãy kiểm tra tệp `.gitignore` và đảm bảo rằng `.env` nằm trong danh sách các tệp bị bỏ qua để tránh việc lộ bí mật cơ sở dữ liệu của bạn khi đẩy lên GitHub.

## Đổ dữ liệu mẫu (Seed) vào cơ sở dữ liệu

Bây giờ cơ sở dữ liệu của bạn đã được tạo, hãy đổ một số dữ liệu ban đầu vào đó.

Chúng tôi đã bao gồm một API mà bạn có thể truy cập trong trình duyệt, API này sẽ chạy một tập lệnh seed để cung cấp cho cơ sở dữ liệu một bộ dữ liệu ban đầu. Tập lệnh này sử dụng SQL để tạo các bảng và sử dụng dữ liệu từ tệp `placeholder-data.ts` để lấp đầy chúng.

Hãy đảm bảo máy chủ phát triển cục bộ của bạn đang chạy bằng `pnpm run dev` và truy cập [localhost:3000/seed](http://localhost:3000/seed) trong trình duyệt của bạn. Khi hoàn tất, bạn sẽ thấy thông báo "Database seeded successfully" trong trình duyệt. Sau khi hoàn thành, bạn có thể xóa tệp này.

**Xử lý sự cố:**
- Đảm bảo bạn đã hiển thị các bí mật cơ sở dữ liệu trước khi sao chép vào tệp `.env`.
- Tập lệnh sử dụng `bcrypt` để băm mật khẩu người dùng. Nếu `bcrypt` không tương thích với môi trường của bạn, bạn có thể cập nhật tập lệnh để sử dụng `bcryptjs` thay thế.
- Nếu bạn gặp bất kỳ vấn đề nào khi seed và muốn chạy lại tập lệnh, bạn có thể xóa bất kỳ bảng hiện có nào bằng cách chạy lệnh `DROP TABLE tablename` trong giao diện truy vấn cơ sở dữ liệu của mình.

## Thực thi các truy vấn

Hãy thực thi một truy vấn để đảm bảo mọi thứ đang hoạt động như mong đợi. Chúng ta sẽ sử dụng một Route Handler khác, `app/query/route.ts`, để truy vấn cơ sở dữ liệu.

Bỏ chú thích (uncomment) cho tệp này và truy cập [localhost:3000/query](http://localhost:3000/query) trong trình duyệt. Bạn sẽ thấy kết quả trả về từ cơ sở dữ liệu.

---

**Tuyệt vời!** Với cơ sở dữ liệu hiện đã được thiết lập và tích hợp, bạn có thể tiếp tục xây dựng ứng dụng của mình.
