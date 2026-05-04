# Chương 7: Lấy dữ liệu (Fetching Data)

Bây giờ bạn đã tạo và đổ dữ liệu vào cơ sở dữ liệu, hãy cùng thảo luận về các cách khác nhau để lấy dữ liệu cho ứng dụng của bạn và xây dựng trang tổng quan dashboard.

### Trong chương này...
Dưới đây là các chủ đề chúng ta sẽ đề cập:
- Tìm hiểu một số cách tiếp cận để lấy dữ liệu: API, ORM, SQL, v.v.
- Cách Server Components giúp bạn truy cập tài nguyên back-end an toàn hơn.
- Request waterfall là gì.
- Cách triển khai lấy dữ liệu song song bằng kỹ thuật JavaScript.

## Các cách lấy dữ liệu

### Tầng API (API layer)

API là một lớp trung gian giữa mã ứng dụng và cơ sở dữ liệu của bạn. Bạn có thể sử dụng API trong các trường hợp:
- Sử dụng các dịch vụ của bên thứ ba cung cấp API.
- Lấy dữ liệu từ phía máy khách (client), bạn cần một tầng API chạy trên máy chủ để tránh lộ bí mật cơ sở dữ liệu.

Trong Next.js, bạn có thể tạo các điểm cuối API (API endpoints) bằng [Route Handlers](https://nextjs.org/docs/app/building-your-application/routing/route-handlers).

### Truy vấn cơ sở dữ liệu

Khi xây dựng ứng dụng full-stack, bạn cần viết logic để tương tác với cơ sở dữ liệu. Với các cơ sở dữ liệu quan hệ như Postgres, bạn có thể sử dụng SQL hoặc một [ORM](https://vercel.com/docs/storage/vercel-postgres/using-an-orm) như Prisma.

Nếu bạn sử dụng **React Server Components** (lấy dữ liệu trên máy chủ), bạn có thể bỏ qua tầng API và truy vấn trực tiếp cơ sở dữ liệu mà không sợ lộ thông tin bảo mật cho phía máy khách.

## Sử dụng Server Components để lấy dữ liệu

Mặc định, các ứng dụng Next.js sử dụng React Server Components. Một số lợi ích của việc sử dụng chúng để lấy dữ liệu:
- Hỗ trợ `Promises`, cung cấp giải pháp cho các tác vụ không đồng bộ như lấy dữ liệu một cách tự nhiên. Bạn có thể sử dụng cú pháp `async/await` mà không cần `useEffect` hay `useState`.
- Chạy trên máy chủ, giúp giữ các logic và quá trình lấy dữ liệu tốn kém ở phía máy chủ, chỉ gửi kết quả về máy khách.
- Có thể truy vấn trực tiếp cơ sở dữ liệu mà không cần tầng API bổ sung.

## Sử dụng SQL

Đối với ứng dụng dashboard này, chúng ta sẽ sử dụng thư viện `postgres.js` và SQL vì:
- SQL là tiêu chuẩn ngành để truy vấn cơ sở dữ liệu quan hệ.
- Hiểu SQL cơ bản giúp bạn nắm vững các nguyên tắc cốt lõi của cơ sở dữ liệu.
- SQL rất linh hoạt, cho phép bạn lấy và thao tác dữ liệu cụ thể.
- Thư viện `postgres.js` cung cấp khả năng bảo vệ chống lại [SQL injection](https://github.com/porsager/postgres?tab=readme-ov-file#query-parameters).

Bạn có thể gọi `sql` ở bất cứ đâu trên máy chủ (Server Component). Chúng tôi đã để các truy vấn trong tệp `/app/lib/data.ts` để bạn dễ dàng quản lý.

## Lấy dữ liệu cho trang tổng quan dashboard

Hãy lấy dữ liệu cho trang `/app/dashboard/page.tsx`:

```tsx
// /app/dashboard/page.tsx
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import { fetchRevenue, fetchLatestInvoices, fetchCardData } from '@/app/lib/data';

export default async function Page() {
  const revenue = await fetchRevenue();
  const latestInvoices = await fetchLatestInvoices();
  const {
    numberOfInvoices,
    numberOfCustomers,
    totalPaidInvoices,
    totalPendingInvoices,
  } = await fetchCardData();

  return (
    <main>
      <h1 className={`${lusitana.className} mb-4 text-xl md:text-2xl`}>
        Dashboard
      </h1>
      <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        <Card title="Collected" value={totalPaidInvoices} type="collected" />
        <Card title="Pending" value={totalPendingInvoices} type="pending" />
        <Card title="Total Invoices" value={numberOfInvoices} type="invoices" />
        <Card title="Total Customers" value={numberOfCustomers} type="customers" />
      </div>
      <div className="mt-6 grid grid-cols-1 gap-6 md:grid-cols-4 lg:grid-cols-8">
        <RevenueChart revenue={revenue} />
        <LatestInvoices latestInvoices={latestInvoices} />
      </div>
    </main>
  );
}
```

## Request Waterfall là gì?

"Waterfall" đề cập đến một chuỗi các yêu cầu mạng phụ thuộc vào việc hoàn thành các yêu cầu trước đó. Trong lấy dữ liệu, mỗi yêu cầu chỉ có thể bắt đầu sau khi yêu cầu trước đó đã trả về dữ liệu.

Ví dụ: `await fetchRevenue()` phải chạy xong thì `await fetchLatestInvoices()` mới bắt đầu chạy.

Điều này không hẳn là xấu trong mọi trường hợp (ví dụ bạn cần ID người dùng trước khi lấy danh sách bạn bè của họ), nhưng nó có thể ảnh hưởng đến hiệu suất nếu các yêu cầu không phụ thuộc vào nhau.

## Lấy dữ liệu song song (Parallel data fetching)

Một cách phổ biến để tránh waterfall là thực hiện tất cả các yêu cầu dữ liệu cùng một lúc - song song.

Trong JavaScript, bạn có thể sử dụng `Promise.all()` hoặc `Promise.allSettled()`. Ví dụ, trong hàm `fetchCardData()` của tệp `data.ts`:

```typescript
export async function fetchCardData() {
  try {
    const invoiceCountPromise = sql`SELECT COUNT(*) FROM invoices`;
    const customerCountPromise = sql`SELECT COUNT(*) FROM customers`;
    const invoiceStatusPromise = sql`SELECT SUM(CASE WHEN status = 'paid' THEN amount ELSE 0 END) AS "paid", ...`;

    const data = await Promise.all([
      invoiceCountPromise,
      customerCountPromise,
      invoiceStatusPromise,
    ]);
    // ...
  }
}
```

Bằng cách sử dụng kỹ thuật này, bạn có thể bắt đầu thực hiện tất cả các việc lấy dữ liệu cùng lúc, giúp ứng dụng nhanh hơn.

---

**Lưu ý:** Vẫn còn một nhược điểm: điều gì xảy ra nếu một yêu cầu dữ liệu chậm hơn tất cả các yêu cầu khác? Chúng ta sẽ tìm hiểu ở chương tiếp theo.
