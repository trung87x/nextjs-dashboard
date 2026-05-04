# Chương 1: Bắt đầu

## Tạo dự án mới

Chúng tôi khuyên bạn nên sử dụng **pnpm** làm trình quản lý gói (package manager), vì nó nhanh hơn và hiệu quả hơn npm hoặc yarn. Nếu bạn chưa cài đặt pnpm, bạn có thể cài đặt nó trên toàn hệ thống bằng cách chạy lệnh:

```bash
npm install -g pnpm
```

Để tạo một ứng dụng Next.js, hãy mở terminal, `cd` vào thư mục bạn muốn lưu trữ dự án và chạy lệnh sau:

```bash
npx create-next-app@latest nextjs-dashboard --example "https://github.com/vercel/next-learn/tree/main/dashboard/starter-example" --use-pnpm
```

Lệnh này sử dụng `create-next-app`, một công cụ giao diện dòng lệnh (CLI) giúp thiết lập ứng dụng Next.js cho bạn. Trong lệnh trên, bạn cũng đang sử dụng cờ `--example` với [mẫu bắt đầu](https://github.com/vercel/next-learn/tree/main/dashboard/starter-example) cho khóa học này.

## Khám phá dự án

Khác với các hướng dẫn yêu cầu bạn viết mã từ đầu, phần lớn mã cho khóa học này đã được viết sẵn cho bạn. Điều này phản ánh tốt hơn quá trình phát triển thực tế, nơi bạn thường sẽ làm việc với các cơ sở mã hiện có.

Mục tiêu của chúng tôi là giúp bạn tập trung vào việc học các tính năng chính của Next.js mà không cần phải viết tất cả mã ứng dụng.

Sau khi cài đặt xong, hãy mở dự án trong trình chỉnh sửa mã của bạn và điều hướng đến thư mục `nextjs-dashboard`.

```bash
cd nextjs-dashboard
```

Hãy dành chút thời gian để khám phá dự án.

### Cấu trúc thư mục

Bạn sẽ nhận thấy rằng dự án có cấu trúc thư mục như sau:

- **/app**: Chứa tất cả các tuyến đường (routes), thành phần (components) và logic cho ứng dụng của bạn. Đây là nơi bạn sẽ làm việc chủ yếu.
- **/app/lib**: Chứa các hàm được sử dụng trong ứng dụng của bạn, chẳng hạn như các hàm tiện ích có thể tái sử dụng và các hàm lấy dữ liệu (data fetching).
- **/app/ui**: Chứa tất cả các thành phần giao diện người dùng (UI components) cho ứng dụng của bạn, chẳng hạn như thẻ (cards), bảng (tables) và biểu mẫu (forms). Để tiết kiệm thời gian, chúng tôi đã tạo kiểu sẵn cho các thành phần này.
- **/public**: Chứa tất cả các tài sản tĩnh cho ứng dụng của bạn, chẳng hạn như hình ảnh.
- **Tệp cấu hình (Config Files)**: Bạn cũng sẽ thấy các tệp cấu hình như `next.config.ts` ở thư mục gốc của ứng dụng. Hầu hết các tệp này được tạo và định cấu hình sẵn khi bạn bắt đầu một dự án mới bằng `create-next-app`. Bạn sẽ không cần phải sửa đổi chúng trong khóa học này.

Đừng ngần ngại khám phá các thư mục này, và đừng lo lắng nếu bạn chưa hiểu hết mọi thứ mã đang thực hiện.

### Dữ liệu mẫu (Placeholder data)

Khi bạn xây dựng giao diện người dùng, việc có một số dữ liệu mẫu sẽ rất hữu ích. Nếu cơ sở dữ liệu hoặc API chưa sẵn sàng, bạn có thể:

- Sử dụng dữ liệu mẫu ở định dạng JSON hoặc dưới dạng các đối tượng JavaScript.
- Sử dụng dịch vụ của bên thứ ba như [mockAPI](https://mockapi.io/).

Đối với dự án này, chúng tôi đã cung cấp một số dữ liệu mẫu trong `app/lib/placeholder-data.ts`. Mỗi đối tượng JavaScript trong tệp đại diện cho một bảng trong cơ sở dữ liệu của bạn. Ví dụ, cho bảng `invoices`:

```typescript
// app/lib/placeholder-data.ts
const invoices = [
  {
    customer_id: customers[0].id,
    amount: 15795,
    status: 'pending',
    date: '2022-12-06',
  },
  {
    customer_id: customers[1].id,
    amount: 20348,
    status: 'pending',
    date: '2022-11-14',
  },
  // ...
];
```

Trong chương về [thiết lập cơ sở dữ liệu của bạn](https://nextjs.org/learn/dashboard-app/setting-up-your-database), bạn sẽ sử dụng dữ liệu này để "seed" cơ sở dữ liệu của mình (đổ dữ liệu ban đầu vào).

### TypeScript

Bạn cũng có thể nhận thấy hầu hết các tệp có hậu tố `.ts` hoặc `.tsx`. Điều này là do dự án được viết bằng TypeScript. Chúng tôi muốn tạo ra một khóa học phản ánh bối cảnh web hiện đại.

Đừng lo lắng nếu bạn không biết TypeScript - chúng tôi sẽ cung cấp các đoạn mã TypeScript khi cần thiết.

Bây giờ, hãy xem tệp `/app/lib/definitions.ts`. Tại đây, chúng tôi xác định thủ công các kiểu dữ liệu sẽ được trả về từ cơ sở dữ liệu. Ví dụ, bảng `invoices` có các kiểu dữ liệu sau:

```typescript
// /app/lib/definitions.ts
export type Invoice = {
  id: string;
  customer_id: string;
  amount: number;
  date: string;
  // Trong TypeScript, đây được gọi là kiểu union chuỗi (string union type).
  // Nó có nghĩa là thuộc tính "status" chỉ có thể là một trong hai chuỗi: 'pending' hoặc 'paid'.
  status: 'pending' | 'paid';
};
```

Bằng cách sử dụng TypeScript, bạn có thể đảm bảo rằng mình không vô tình truyền sai định dạng dữ liệu cho các thành phần hoặc cơ sở dữ liệu, chẳng hạn như truyền một chuỗi thay vì một số cho thuộc tính `amount` của hóa đơn.

Nếu bạn là một nhà phát triển TypeScript:
- Chúng tôi đang khai báo các kiểu dữ liệu một cách thủ công, nhưng để đảm bảo an toàn kiểu dữ liệu (type-safety) tốt hơn, chúng tôi khuyên bạn nên sử dụng [Prisma](https://www.prisma.io/) hoặc [Drizzle](https://orm.drizzle.team/), những công cụ tự động tạo kiểu dữ liệu dựa trên sơ đồ cơ sở dữ liệu của bạn.
- Next.js sẽ tự động phát hiện nếu dự án của bạn sử dụng TypeScript và cài đặt các gói cũng như cấu hình cần thiết. Next.js cũng đi kèm với một [plugin TypeScript](https://nextjs.org/docs/app/building-your-application/configuring/typescript#typescript-plugin) cho trình chỉnh sửa mã của bạn, giúp tự động hoàn thành và kiểm tra kiểu dữ liệu.

## Chạy máy chủ phát triển (Development Server)

Chạy lệnh `pnpm i` để cài đặt các gói của dự án.

```bash
pnpm i
```

Sau đó chạy `pnpm dev` để khởi động máy chủ phát triển.

```bash
pnpm dev
```

`pnpm dev` khởi động máy chủ phát triển Next.js của bạn trên cổng `3000`. Hãy kiểm tra xem nó có hoạt động không.

Mở [http://localhost:3000](http://localhost:3000) trên trình duyệt của bạn. Trang chủ của bạn sẽ trông như thế này (được thiết kế cố tình không có kiểu dáng):

---

**Chúc mừng!** Bạn đã hoàn thành Chương 1. Bạn đã tạo một ứng dụng Next.js bằng ví dụ mẫu và chạy máy chủ phát triển thành công.
