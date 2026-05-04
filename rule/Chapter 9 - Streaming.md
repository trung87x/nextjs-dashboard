# Chương 9: Streaming (Truyền tải dữ liệu tuần tự)

Trong chương trước, bạn đã tìm hiểu về các phương pháp kết xuất khác nhau của Next.js. Chúng ta cũng đã thảo luận về việc các yêu cầu dữ liệu chậm có thể ảnh hưởng đến hiệu suất ứng dụng như thế nào. Hãy cùng xem cách cải thiện trải nghiệm người dùng khi có các yêu cầu dữ liệu chậm.

### Trong chương này...
Dưới đây là các chủ đề chúng ta sẽ đề cập:
- Streaming là gì và khi nào nên sử dụng nó.
- Cách triển khai streaming với `loading.tsx` và `Suspense`.
- Loading skeletons là gì.
- Nhóm tuyến đường (Route Groups) trong Next.js là gì và khi nào nên sử dụng chúng.
- Nơi đặt ranh giới (boundaries) React Suspense trong ứng dụng của bạn.

## Streaming là gì?

Streaming là một kỹ thuật truyền dữ liệu cho phép bạn chia nhỏ một tuyến đường thành các "mảnh" (chunks) nhỏ hơn và truyền chúng dần dần từ máy chủ đến máy khách khi chúng sẵn sàng.

Bằng cách sử dụng streaming, bạn có thể ngăn các yêu cầu dữ liệu chậm làm chặn toàn bộ trang web. Điều này cho phép người dùng nhìn thấy và tương tác với các phần của trang mà không cần đợi tất cả dữ liệu tải xong.

Có hai cách để triển khai streaming trong Next.js:
1. Ở cấp độ trang, với tệp `loading.tsx`.
2. Ở cấp độ thành phần, với `<Suspense>`.

## Streaming toàn bộ trang với loading.tsx

Trong thư mục `/app/dashboard`, tạo một tệp mới có tên là `loading.tsx`:

```tsx
// /app/dashboard/loading.tsx
export default function Loading() {
  return <div>Loading...</div>;
}
```

Một vài điều đang diễn ra ở đây:
1. `loading.tsx` là một tệp đặc biệt của Next.js được xây dựng trên React Suspense. Nó cho phép bạn tạo giao diện dự phòng (fallback UI) để hiển thị trong khi nội dung trang đang tải.
2. Vì `<SideNav>` là tĩnh, nó sẽ được hiển thị ngay lập tức. Người dùng có thể tương tác với nó trong khi nội dung động đang tải.
3. Người dùng không phải đợi trang tải xong mới có thể điều hướng đi nơi khác (điều hướng có thể ngắt quãng - interruptible navigation).

### Thêm loading skeletons (Khung xương tải dữ liệu)

Loading skeleton là một phiên bản đơn giản hóa của giao diện người dùng. Nhiều trang web sử dụng chúng như một trình giữ chỗ để cho người dùng biết nội dung đang được tải. Bất kỳ giao diện nào bạn thêm vào `loading.tsx` sẽ được gửi đến máy khách trước tiên.

Cập nhật `loading.tsx` để sử dụng `<DashboardSkeleton>`:

```tsx
// /app/dashboard/loading.tsx
import DashboardSkeleton from '@/app/ui/skeletons';

export default function Loading() {
  return <DashboardSkeleton />;
}
```

### Sửa lỗi loading skeleton với nhóm tuyến đường (Route Groups)

Hiện tại, `loading.tsx` cũng sẽ áp dụng cho các trang con như `/invoices` và `/customers`. Chúng ta có thể thay đổi điều này bằng **Route Groups**.

Tạo một thư mục mới có tên là `/(overview)` bên trong thư mục `dashboard`. Sau đó, di chuyển các tệp `loading.tsx` và `page.tsx` vào bên trong thư mục đó.

Route groups cho phép bạn tổ chức các tệp thành các nhóm logic mà không ảnh hưởng đến cấu trúc đường dẫn URL. Khi bạn tạo một thư mục sử dụng dấu ngoặc đơn `()`, tên của nó sẽ không được bao gồm trong đường dẫn URL. Vì vậy, `/dashboard/(overview)/page.tsx` vẫn là `/dashboard`.

## Streaming một thành phần (component)

Đôi khi bạn muốn streaming cụ thể hơn cho từng thành phần bằng cách sử dụng React Suspense.

Suspense cho phép bạn trì hoãn việc kết xuất các phần của ứng dụng cho đến khi một điều kiện nào đó được đáp ứng (ví dụ: dữ liệu đã được tải). Bạn có thể bọc các thành phần động trong Suspense và truyền cho nó một thành phần dự phòng.

Hãy thử với `<RevenueChart />`. Trước tiên, bạn cần di chuyển logic lấy dữ liệu vào bên trong chính thành phần đó thay vì lấy ở cấp độ trang.

Trong tệp `/app/dashboard/(overview)/page.tsx`:
1. Xóa hàm `fetchRevenue()` và dữ liệu của nó.
2. Nhập `Suspense` từ React và bọc nó quanh `<RevenueChart />`.
3. Truyền cho nó một thành phần dự phòng là `<RevenueChartSkeleton />`.

```tsx
// /app/dashboard/(overview)/page.tsx
import { Suspense } from 'react';
import { RevenueChartSkeleton } from '@/app/ui/skeletons';
// ...

export default async function Page() {
  // ...
  return (
    <main>
      {/* ... */}
      <div className="mt-6 grid grid-cols-1 gap-6 md:grid-cols-4 lg:grid-cols-8">
        <Suspense fallback={<RevenueChartSkeleton />}>
          <RevenueChart />
        </Suspense>
        {/* ... */}
      </div>
    </main>
  );
}
```

Sau đó, cập nhật thành phần `<RevenueChart>` để nó tự lấy dữ liệu của chính mình.

## Thực hành: Streaming <LatestInvoices>

Bây giờ đến lượt bạn! Hãy thực hành những gì vừa học bằng cách streaming thành phần `<LatestInvoices>`. Di chuyển `fetchLatestInvoices()` từ trang xuống thành phần và bọc nó trong ranh giới `Suspense` với fallback là `<LatestInvoicesSkeleton />`.

## Nhóm các thành phần Card

Để tránh hiệu ứng các thẻ (cards) xuất hiện rải rác làm người dùng khó chịu, bạn có thể nhóm chúng lại bằng một thành phần wrapper (ví dụ: `<CardWrapper />`) và bọc toàn bộ nhóm đó trong một ranh giới `Suspense` duy nhất.

## Quyết định vị trí đặt ranh giới Suspense

Vị trí đặt ranh giới Suspense phụ thuộc vào:
1. Bạn muốn người dùng trải nghiệm trang web như thế nào khi nó đang stream.
2. Bạn muốn ưu tiên nội dung nào.
3. Các thành phần có phụ thuộc vào việc lấy dữ liệu hay không.

Thông thường, tốt nhất là di chuyển việc lấy dữ liệu xuống các thành phần cần nó, sau đó bọc các thành phần đó trong Suspense. Đừng ngần ngại thử nghiệm để tìm ra trải nghiệm tốt nhất cho người dùng của bạn.

---

**Tuyệt vời!** Bạn đã học được cách sử dụng Suspense và loading skeletons để tạo ra trải nghiệm tải trang mượt mà hơn.
