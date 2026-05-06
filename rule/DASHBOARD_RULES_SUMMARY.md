# Tổng hợp Quy tắc và Giải pháp tối ưu cho Dự án Next.js (Chương 1 - 9)

Tệp này tóm tắt các vấn đề thường gặp và cách giải quyết tốt nhất được rút ra từ dự án Next.js Dashboard. Bạn có thể sử dụng các quy tắc này làm tiêu chuẩn cho các dự án sau.

---

## 1. Khởi tạo và Cấu trúc (Project Setup)
- **Vấn đề:** Quản lý gói chậm và chiếm dung lượng ổ đĩa lớn.
- **Giải pháp:** Sử dụng `pnpm` thay cho npm/yarn để tăng tốc độ cài đặt và tiết kiệm không gian nhờ cơ chế content-addressable store.
- **Quy tắc:** Luôn sử dụng `npx create-next-app@latest` với các mẫu (example) chuẩn để có cấu trúc thư mục `/app`, `/public`, `/lib` đồng nhất.

## 2. Định dạng giao diện (Styling)
- **Vấn đề:** Xung đột tên lớp CSS và khó bảo trì khi dự án lớn dần.
- **Giải pháp:**
    - Sử dụng **Tailwind CSS** cho các lớp tiện ích (utility classes) nhanh chóng.
    - Sử dụng **CSS Modules** (`.module.css`) khi muốn tách biệt CSS hoàn toàn cho từng component.
    - Sử dụng thư viện **`clsx`** để quản lý các tên lớp có điều kiện (ví dụ: thay đổi màu sắc dựa trên trạng thái `status`).

## 3. Tối ưu hóa tài nguyên (Fonts & Images)
- **Vấn đề:** Thay đổi bố cục đột ngột (Layout Shift - CLS) khi phông chữ hoặc hình ảnh đang tải.
- **Giải pháp:**
    - **Fonts:** Sử dụng `next/font/google` để tự động tải và lưu trữ phông chữ tại máy chủ, loại bỏ yêu cầu mạng bổ sung.
    - **Images:** Luôn sử dụng thành phần `<Image />` của Next.js thay cho thẻ `<img>`.
- **Quy tắc:** Bắt buộc chỉ định `width` và `height` cho hình ảnh để trình duyệt dành sẵn không gian, tránh xê dịch giao diện.

## 4. Định tuyến và Bố cục (Routing & Layouts)
- **Vấn đề:** Lặp lại mã nguồn cho các thành phần dùng chung (Header, Sidebar) và làm mất trạng thái (state) khi chuyển trang.
- **Giải pháp:**
    - Sử dụng **Nested Layouts** (tệp `layout.tsx` trong các thư mục con).
    - **Partial Rendering:** Chỉ các trang con (`page.tsx`) thay đổi nội dung, còn Layout dùng chung sẽ không bị render lại.
- **Quy tắc:** Đặt các logic dùng chung cho một nhóm trang vào thư mục `(group_name)` (Route Groups) để tổ chức code sạch sẽ mà không ảnh hưởng đến URL.

## 5. Điều hướng mượt mà (Navigation)
- **Vấn đề:** Tải lại toàn bộ trang (Full page refresh) làm ứng dụng bị chậm và mất cảm giác Single Page App.
- **Giải pháp:** Sử dụng thành phần `<Link />` thay cho thẻ `<a>`.
- **Quy tắc:** Sử dụng Hook `usePathname()` để nhận biết đường dẫn hiện tại và làm nổi bật liên kết đang hoạt động (Active Link).

## 6. Truy vấn Dữ liệu (Data Fetching)
- **Vấn đề:** Lộ thông tin bảo mật cơ sở dữ liệu và hiện tượng "Request Waterfall" (yêu cầu sau đợi yêu cầu trước).
- **Giải pháp:**
    - Sử dụng **React Server Components** để truy vấn trực tiếp DB từ Server.
    - Sử dụng **`Promise.all()`** để thực hiện các truy vấn không phụ thuộc nhau một cách song song.
- **Quy tắc:** Luôn ưu tiên lấy dữ liệu tại Server để tận dụng tốc độ mạng nội bộ và giảm tải cho trình duyệt khách.

## 7. Xử lý độ trễ và Streaming
- **Vấn đề:** Trang web bị "treo" (trắng trang) nếu có một yêu cầu dữ liệu quá chậm.
- **Giải pháp:**
    - **Streaming toàn trang:** Sử dụng tệp `loading.tsx` để hiển thị giao diện tạm thời (Skeleton).
    - **Streaming từng thành phần:** Sử dụng **React `Suspense`** để bọc quanh các component chậm, cho phép các phần khác hiện ra trước.
- **Quy tắc:** Di chuyển việc lấy dữ liệu xuống cấp thành phần thấp nhất có thể (Colocation) để tối ưu hóa việc sử dụng `Suspense`.

## 8. Tìm kiếm và Phân trang (Search & Pagination)
- **Vấn đề:** Trạng thái tìm kiếm bị mất khi người dùng nạp lại trang hoặc chia sẻ liên kết.
- **Giải pháp:** Quản lý trạng thái thông qua **URL Search Params** (`?query=abc&page=1`).
- **Quy tắc:** Sử dụng kỹ thuật **Debouncing** (với `use-debounce`) để giảm số lượng truy vấn DB khi người dùng đang gõ phím.

## 9. Thay đổi dữ liệu (Server Actions)
- **Vấn đề:** Phải tạo các API Route phức tạp và xử lý bảo mật phía Client.
- **Giải pháp:** Sử dụng **React Server Actions** để chạy mã trực tiếp trên Server.
- **Quy tắc:**
    - Sử dụng **Zod** để xác thực kiểu dữ liệu nghiêm ngặt.
    - Luôn gọi `revalidatePath` sau khi thay đổi dữ liệu để cập nhật bộ nhớ đệm.

## 10. Xử lý lỗi (Error Handling)
- **Vấn đề:** Ứng dụng hiển thị lỗi kỹ thuật khó hiểu hoặc bị "chết" hoàn toàn khi có sự cố.
- **Giải pháp:**
    - Sử dụng tệp **`error.tsx`** làm ranh giới lỗi (Error Boundary) toàn cục.
    - Sử dụng hàm **`notFound()`** và tệp **`not-found.tsx`** cho các trường hợp không tìm thấy tài nguyên (lỗi 404).
- **Quy tắc:** Đặt `redirect` bên ngoài khối `try/catch` để tránh xung đột với cơ chế nội bộ của Next.js.

## 11. Khả năng truy cập (Accessibility)
- **Vấn đề:** Người dùng sử dụng trình đọc màn hình hoặc chỉ dùng bàn phím khó tiếp cận ứng dụng.
- **Giải pháp:**
    - Sử dụng các thuộc tính **ARIA** (`aria-describedby`, `aria-live`) cho thông báo lỗi.
    - Xác thực biểu mẫu phía máy chủ với **`useActionState`** để phản hồi lỗi chi tiết.
- **Quy tắc:** Luôn chạy `pnpm lint` để kiểm tra các vi phạm về khả năng truy cập (JSX-a11y).

## 12. Xác thực người dùng (Authentication)
- **Vấn đề:** Cần bảo vệ dữ liệu nhạy cảm và hạn chế quyền truy cập vào Dashboard.
- **Giải pháp:** Sử dụng thư viện **NextAuth.js** để quản lý phiên làm việc (sessions).
- **Quy tắc:**
    - Sử dụng **Middleware** để tập trung logic bảo vệ tuyến đường (tránh kiểm tra thủ công ở từng trang).
    - Luôn lưu `AUTH_SECRET` trong biến môi trường để mã hóa cookie an toàn.
    - Sử dụng **bcrypt** để so sánh mật khẩu đã băm thay vì lưu mật khẩu văn bản thuần túy.

## 13. Tối ưu hóa SEO (Metadata)
- **Vấn đề:** Ứng dụng thiếu thông tin mô tả, tiêu đề không rõ ràng khi chia sẻ trên mạng xã hội hoặc tìm kiếm trên Google.
- **Giải pháp:** Sử dụng **Metadata API** của Next.js.
- **Quy tắc:**
    - Sử dụng `title.template` trong Root Layout để tạo tiêu đề trang nhất quán.
    - Luôn cung cấp `description` và `metadataBase` để tối ưu hóa SEO và hiển thị hình ảnh Open Graph chính xác.

## 14. Hoàn thiện và Kiểm thử (Final Polish)
- **Vấn đề:** Các trang placeholder (trang trống) làm giảm tính chuyên nghiệp của ứng dụng.
- **Giải pháp:** Tận dụng tối đa các Component dùng chung (như `Table`, `Search`) và các hàm lấy dữ liệu có sẵn trong `lib/data.ts` để hoàn thiện các trang phụ (ví dụ: trang Customers).
- **Quy tắc kiểm thử 100%:**
    - Kiểm tra luồng đăng nhập/đăng xuất (Auth Flow).
    - Kiểm tra khả năng hiển thị dữ liệu thực tế từ DB.
    - Kiểm tra tính năng tìm kiếm và phân trang trên mọi trang danh sách.
    - Đảm bảo không còn lỗi Console hay lỗi Build (`use client` directive).
