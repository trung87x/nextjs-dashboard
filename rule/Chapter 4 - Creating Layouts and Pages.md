# Chương 4: Tạo Layout và Trang

Cho đến nay, ứng dụng của bạn chỉ mới có một trang chủ. Hãy cùng tìm hiểu cách bạn có thể tạo thêm nhiều tuyến đường (routes) với layout và trang.

### Trong chương này...
Dưới đây là các chủ đề chúng ta sẽ đề cập:
- Tạo các tuyến đường cho bảng điều khiển (dashboard) bằng cách sử dụng định tuyến dựa trên hệ thống tệp (file-system routing).
- Hiểu vai trò của các thư mục và tệp khi tạo các phân đoạn tuyến đường (route segments) mới.
- Tạo một layout lồng nhau (nested layout) có thể được chia sẻ giữa nhiều trang dashboard.
- Hiểu thế nào là colocation (cùng vị trí), partial rendering (kết xuất một phần) và root layout (layout gốc).

## Định tuyến lồng nhau (Nested routing)

Next.js sử dụng hệ thống định tuyến dựa trên tệp, trong đó các thư mục được sử dụng để tạo các tuyến đường lồng nhau. Mỗi thư mục đại diện cho một phân đoạn tuyến đường (route segment) tương ứng với một phân đoạn URL.

Bạn có thể tạo các giao diện người dùng (UI) riêng biệt cho từng tuyến đường bằng cách sử dụng các tệp `layout.tsx` và `page.tsx`.

`page.tsx` là một tệp đặc biệt của Next.js, nó xuất ra một thành phần React và tệp này là bắt buộc để tuyến đường có thể truy cập được. Trong ứng dụng của bạn, bạn đã có một tệp trang: `/app/page.tsx` - đây là trang chủ liên kết với tuyến đường `/`.

Để tạo một tuyến đường lồng nhau, bạn có thể lồng các thư mục vào nhau và thêm các tệp `page.tsx` bên trong chúng. Ví dụ:

`/app/dashboard/page.tsx` sẽ liên kết với đường dẫn `/dashboard`. Hãy cùng tạo trang này để xem nó hoạt động như thế nào!

## Tạo trang Dashboard

Tạo một thư mục mới có tên là `dashboard` bên trong `/app`. Sau đó, tạo một tệp `page.tsx` mới bên trong thư mục `dashboard` với nội dung sau:

```tsx
// /app/dashboard/page.tsx
export default function Page() {
  return <p>Dashboard Page</p>;
}
```

Bây giờ, hãy đảm bảo rằng máy chủ phát triển đang chạy và truy cập [http://localhost:3000/dashboard](http://localhost:3000/dashboard). Bạn sẽ thấy dòng chữ "Dashboard Page".

Đây là cách bạn có thể tạo các trang khác nhau trong Next.js: tạo một phân đoạn tuyến đường mới bằng cách sử dụng thư mục và thêm tệp `page` bên trong đó.

Bằng cách sử dụng tên đặc biệt cho các tệp trang, Next.js cho phép bạn [đặt cùng vị trí (colocate)](https://nextjs.org/docs/app/getting-started/project-structure#colocation) các thành phần UI, tệp kiểm thử và mã liên quan khác cùng với các tuyến đường của bạn. Chỉ có nội dung bên trong tệp trang mới có thể truy cập công khai. Ví dụ: các thư mục `/ui` và `/lib` được đặt cùng vị trí bên trong thư mục `/app` cùng với các tuyến đường của bạn.

## Thực hành: Tạo các trang trong Dashboard

Hãy thực hành tạo thêm các tuyến đường. Trong bảng điều khiển của bạn, hãy tạo thêm hai trang:
1. **Trang Khách hàng (Customers Page)**: Trang này có thể truy cập tại [http://localhost:3000/dashboard/customers](http://localhost:3000/dashboard/customers). Hiện tại, nó sẽ trả về một phần tử `<p>Customers Page</p>`.
2. **Trang Hóa đơn (Invoices Page)**: Trang hóa đơn có thể truy cập tại [http://localhost:3000/dashboard/invoices](http://localhost:3000/dashboard/invoices). Hiện tại, nó cũng trả về một phần tử `<p>Invoices Page</p>`.

## Tạo Layout cho Dashboard

Các bảng điều khiển thường có một số kiểu điều hướng được chia sẻ trên nhiều trang. Trong Next.js, bạn có thể sử dụng tệp `layout.tsx` đặc biệt để tạo giao diện người dùng được chia sẻ giữa nhiều trang. Hãy tạo một layout cho các trang dashboard!

Bên trong thư mục `/dashboard`, thêm một tệp mới có tên là `layout.tsx` và dán mã sau:

```tsx
// /app/dashboard/layout.tsx
import SideNav from '@/app/ui/dashboard/sidenav';

export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <div className="flex h-screen flex-col md:flex-row md:overflow-hidden">
      <div className="w-full flex-none md:w-64">
        <SideNav />
      </div>
      <div className="grow p-6 md:overflow-y-auto md:p-12">{children}</div>
    </div>
  );
}
```

Một vài điều đang diễn ra trong đoạn mã này:
Đầu tiên, bạn đang nhập thành phần `<SideNav />` vào layout của mình. Bất kỳ thành phần nào bạn nhập vào tệp này sẽ là một phần của layout.

Thành phần `<Layout />` nhận một thuộc tính `children`. Thành phần con này có thể là một trang hoặc một layout khác. Trong trường hợp của bạn, các trang bên trong `/dashboard` sẽ tự động được lồng bên trong thành phần `<Layout />`.

Hãy kiểm tra xem mọi thứ có hoạt động chính xác hay không bằng cách lưu các thay đổi và kiểm tra localhost của bạn.

Một lợi ích của việc sử dụng layout trong Next.js là khi điều hướng, chỉ các thành phần trang được cập nhật trong khi layout sẽ không được kết xuất lại (re-render). Đây được gọi là [partial rendering (kết xuất một phần)](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#4-partial-rendering), giúp giữ nguyên trạng thái React ở phía máy khách trong layout khi chuyển đổi giữa các trang.

## Layout gốc (Root layout)

Trong Chương 3, bạn đã nhập phông chữ `Inter` vào một layout khác: `/app/layout.tsx`. Đây được gọi là một [root layout](https://nextjs.org/docs/app/api-reference/file-conventions/layout#root-layouts) và nó là bắt buộc trong mọi ứng dụng Next.js.

Bất kỳ giao diện người dùng nào bạn thêm vào root layout sẽ được chia sẻ trên tất cả các trang trong ứng dụng của bạn. Bạn có thể sử dụng root layout để sửa đổi các thẻ `<html>` và `<body>`, cũng như thêm siêu dữ liệu (metadata).

Vì layout mới mà bạn vừa tạo (`/app/dashboard/layout.tsx`) là duy nhất cho các trang dashboard, nên bạn không cần thêm bất kỳ UI nào vào root layout ở trên.

---

**Tuyệt vời!** Ứng dụng dashboard đang dần được hình thành.
