# Chương 2: Định dạng CSS

Hiện tại, trang chủ của bạn chưa có bất kỳ định dạng nào. Hãy cùng tìm hiểu các cách khác nhau để định dạng ứng dụng Next.js của bạn.

### Trong chương này...
Dưới đây là các chủ đề chúng ta sẽ đề cập:
- Cách thêm tệp CSS toàn cục vào ứng dụng của bạn.
- Hai cách định dạng khác nhau: Tailwind và CSS Modules.
- Cách thêm tên lớp (class names) có điều kiện bằng gói tiện ích `clsx`.

## Kiểu dáng toàn cục (Global styles)

Nếu bạn nhìn vào bên trong thư mục `/app/ui`, bạn sẽ thấy một tệp có tên là `global.css`. Bạn có thể sử dụng tệp này để thêm các quy tắc CSS cho tất cả các tuyến đường trong ứng dụng của mình - chẳng hạn như các quy tắc thiết lập lại CSS (CSS reset), các kiểu dáng toàn trang cho các phần tử HTML như liên kết, và nhiều hơn nữa.

Bạn có thể nhập `global.css` vào bất kỳ thành phần nào trong ứng dụng, nhưng thông thường tốt nhất là nên thêm nó vào thành phần cấp cao nhất của bạn. Trong Next.js, đây là [root layout](https://nextjs.org/docs/app/api-reference/file-conventions/layout#root-layouts).

Thêm kiểu dáng toàn cục vào ứng dụng của bạn bằng cách điều hướng đến `/app/layout.tsx` và nhập tệp `global.css`:

```typescript
// /app/layout.tsx
import '@/app/ui/global.css';

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

Khi máy chủ phát triển vẫn đang chạy, hãy lưu các thay đổi của bạn và xem trước chúng trong trình duyệt. Trang chủ của bạn bây giờ sẽ có một số định dạng cơ bản.

Nhưng khoan đã, bạn chưa thêm bất kỳ quy tắc CSS nào, vậy các kiểu dáng đó đến từ đâu?
Nếu bạn nhìn vào bên trong `global.css`, bạn sẽ nhận thấy một số chỉ thị `@tailwind`:

```css
/* /app/ui/global.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

## Tailwind

[Tailwind](https://tailwindcss.com/) là một khung (framework) CSS giúp tăng tốc quá trình phát triển bằng cách cho phép bạn nhanh chóng viết các [lớp tiện ích](https://tailwindcss.com/docs/utility-first) trực tiếp trong mã React của mình.

Trong Tailwind, bạn định dạng các phần tử bằng cách thêm tên lớp. Ví dụ, thêm `"text-blue-500"` sẽ làm cho văn bản trong thẻ `<h1>` có màu xanh lam:

```jsx
<h1 className="text-blue-500">Tôi có màu xanh!</h1>
```

Mặc dù các kiểu dáng CSS được chia sẻ toàn cục, nhưng mỗi lớp được áp dụng riêng lẻ cho từng phần tử. Điều này có nghĩa là nếu bạn thêm hoặc xóa một phần tử, bạn không phải lo lắng về việc duy trì các bảng kiểu riêng biệt, xung đột kiểu dáng hoặc kích thước gói CSS tăng lên khi ứng dụng của bạn mở rộng.

Khi bạn sử dụng `create-next-app` để bắt đầu một dự án mới, Next.js sẽ hỏi bạn có muốn sử dụng Tailwind hay không. Nếu bạn chọn "có", Next.js sẽ tự động cài đặt các gói cần thiết và cấu hình Tailwind trong ứng dụng của bạn.

Hãy thử nghiệm với Tailwind! Sao chép mã bên dưới và dán nó lên trên phần tử `<p>` trong `/app/page.tsx`:

```jsx
// /app/page.tsx
<div
  className="relative w-0 h-0 border-l-[15px] border-r-[15px] border-b-[26px] border-l-transparent border-r-transparent border-b-black"
/>
```

Nếu bạn thích viết các quy tắc CSS truyền thống hoặc muốn tách biệt kiểu dáng khỏi JSX - CSS Modules là một sự thay thế tuyệt vời.

## CSS Modules

[CSS Modules](https://nextjs.org/docs/app/getting-started/css#css-modules) cho phép bạn giới hạn phạm vi CSS cho một thành phần bằng cách tự động tạo các tên lớp duy nhất, nhờ đó bạn cũng không phải lo lắng về xung đột kiểu dáng.

Trong khóa học này chúng ta sẽ tiếp tục sử dụng Tailwind, nhưng hãy dành một chút thời gian để xem cách bạn có thể đạt được kết quả tương tự như ví dụ trên bằng CSS Modules.

Bên trong `/app/ui`, tạo một tệp mới có tên `home.module.css` và thêm các quy tắc CSS sau:

```css
/* /app/ui/home.module.css */
.shape {
  height: 0;
  width: 0;
  border-bottom: 30px solid black;
  border-left: 20px solid transparent;
  border-right: 20px solid transparent;
}
```

Sau đó, bên trong tệp `/app/page.tsx`, hãy nhập (import) các kiểu dáng và thay thế các tên lớp Tailwind từ thẻ `<div>` bạn đã thêm bằng `styles.shape`:

```jsx
// /app/page.tsx
import styles from '@/app/ui/home.module.css';

// ...
<div className={styles.shape} />
// ...
```

Lưu các thay đổi và xem trước trong trình duyệt. Bạn sẽ thấy hình tam giác tương tự như trước.

Tailwind và CSS Modules là hai cách phổ biến nhất để định dạng các ứng dụng Next.js. Việc sử dụng cách nào là tùy thuộc vào sở thích của bạn - thậm chí bạn có thể sử dụng cả hai trong cùng một ứng dụng!

## Sử dụng thư viện clsx để chuyển đổi tên lớp

Có những trường hợp bạn cần định dạng một phần tử theo điều kiện dựa trên trạng thái (state) hoặc một điều kiện nào đó khác.

[clsx](https://www.npmjs.com/package/clsx) là một thư viện cho phép bạn chuyển đổi các tên lớp một cách dễ dàng. Dưới đây là cách sử dụng cơ bản:

- Giả sử bạn muốn tạo một thành phần `InvoiceStatus` nhận vào thuộc tính `status`. Trạng thái có thể là `'pending'` hoặc `'paid'`.
- Nếu là `'paid'`, bạn muốn màu sắc là xanh lá cây. Nếu là `'pending'`, bạn muốn màu sắc là xám.

Bạn có thể sử dụng `clsx` để áp dụng các lớp theo điều kiện như sau:

```typescript
import clsx from 'clsx';

export default function InvoiceStatus({ status }: { status: string }) {
  return (
    <span
      className={clsx(
        'inline-flex items-center rounded-full px-2 py-1 text-sm',
        {
          'bg-gray-100 text-gray-500': status === 'pending',
          'bg-green-500 text-white': status === 'paid',
        },
      )}
    >
      {/* ... */}
    </span>
  );
}
```

## Các giải pháp định dạng khác

Ngoài các phương pháp đã thảo luận, bạn cũng có thể định dạng ứng dụng Next.js của mình bằng:
- **Sass**: Cho phép bạn nhập các tệp `.css` và `.scss`.
- **Thư viện CSS-in-JS**: Chẳng hạn như `styled-jsx`, `styled-components`, và `emotion`.

Hãy xem tài liệu [CSS documentation](https://nextjs.org/docs/app/building-your-application/styling) để biết thêm thông tin.

---

**Làm tốt lắm!** Bạn đã tìm hiểu về các cách khác nhau để định dạng một ứng dụng Next.js.
