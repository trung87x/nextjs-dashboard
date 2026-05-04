# Chương 3: Tối ưu hóa Phông chữ và Hình ảnh

Trong chương trước, bạn đã học cách định dạng ứng dụng Next.js của mình. Hãy tiếp tục hoàn thiện trang chủ bằng cách thêm một phông chữ tùy chỉnh và một hình ảnh hero.

### Trong chương này...
Dưới đây là các chủ đề chúng ta sẽ đề cập:
- Cách thêm phông chữ tùy chỉnh bằng `next/font`.
- Cách thêm hình ảnh bằng `next/image`.
- Cách phông chữ và hình ảnh được tối ưu hóa trong Next.js.

## Tại sao cần tối ưu hóa phông chữ?

Phông chữ đóng một vai trò quan trọng trong thiết kế của một trang web, nhưng việc sử dụng phông chữ tùy chỉnh trong dự án có thể ảnh hưởng đến hiệu suất nếu các tệp phông chữ cần được tìm nạp và tải.

[Cumulative Layout Shift (CLS)](https://vercel.com/blog/how-core-web-vitals-affect-seo) là một chỉ số được Google sử dụng để đánh giá hiệu suất và trải nghiệm người dùng của một trang web. Với phông chữ, hiện tượng thay đổi bố cục (layout shift) xảy ra khi trình duyệt hiển thị văn bản ban đầu bằng phông chữ dự phòng hoặc phông chữ hệ thống, sau đó thay thế bằng phông chữ tùy chỉnh sau khi nó đã được tải xong. Việc thay thế này có thể làm thay đổi kích thước văn bản, khoảng cách hoặc bố cục, làm xê dịch các phần tử xung quanh nó.

Next.js tự động tối ưu hóa phông chữ trong ứng dụng khi bạn sử dụng mô-đun `next/font`. Nó tải xuống các tệp phông chữ tại thời điểm xây dựng (build time) và lưu trữ chúng cùng với các tài sản tĩnh khác của bạn. Điều này có nghĩa là khi người dùng truy cập ứng dụng của bạn, sẽ không có thêm các yêu cầu mạng cho phông chữ, điều này giúp cải thiện hiệu suất.

## Thêm phông chữ chính

Hãy thêm một phông chữ Google tùy chỉnh vào ứng dụng của bạn để xem nó hoạt động như thế nào.

Trong thư mục `/app/ui`, tạo một tệp mới có tên là `fonts.ts`. Bạn sẽ sử dụng tệp này để quản lý các phông chữ được sử dụng trong toàn bộ ứng dụng.

Nhập phông chữ `Inter` từ mô-đun `next/font/google` - đây sẽ là phông chữ chính của bạn. Sau đó, chỉ định [tập hợp con (subset)](https://fonts.google.com/knowledge/glossary/subsetting) mà bạn muốn tải. Trong trường hợp này là `'latin'`:

```typescript
// /app/ui/fonts.ts
import { Inter } from 'next/font/google';

export const inter = Inter({ subsets: ['latin'] });
```

Cuối cùng, thêm phông chữ vào phần tử `<body>` trong `/app/layout.tsx`:

```typescript
// /app/layout.tsx
import '@/app/ui/global.css';
import { inter } from '@/app/ui/fonts';

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className={`${inter.className} antialiased`}>{children}</body>
    </html>
  );
}
```

Bằng cách thêm `Inter` vào phần tử `<body>`, phông chữ này sẽ được áp dụng cho toàn bộ ứng dụng của bạn. Ở đây, bạn cũng đang thêm lớp `antialiased` của Tailwind để làm mượt phông chữ. Việc sử dụng lớp này không phải là bắt buộc, nhưng nó mang lại một nét tinh tế cho giao diện.

## Thực hành: Thêm phông chữ phụ

Bạn cũng có thể thêm phông chữ cho các phần tử cụ thể trong ứng dụng của mình.

Bây giờ đến lượt bạn! Trong tệp `fonts.ts`, hãy nhập một phông chữ phụ có tên là `Lusitana` và áp dụng nó cho phần tử `<p>` trong tệp `/app/page.tsx` của bạn. Ngoài việc chỉ định tập hợp con như bạn đã làm trước đó, bạn cũng nên chỉ định các độ đậm (weight) khác nhau của phông chữ. Ví dụ: `400` (bình thường) và `700` (đậm).

Cuối cùng, thành phần `<AcmeLogo />` cũng sử dụng `Lusitana`. Nó đã bị chú thích (comment out) để tránh lỗi, bây giờ bạn có thể bỏ chú thích cho nó:

```jsx
// /app/page.tsx
// ...
export default function Page() {
  return (
    <main className="flex min-h-screen flex-col p-6">
      <div className="flex h-20 shrink-0 items-end rounded-lg bg-blue-500 p-4 md:h-52">
        <AcmeLogo />
        {/* ... */}
      </div>
    </main>
  );
}
```

## Tại sao cần tối ưu hóa hình ảnh?

Next.js có thể cung cấp các tài sản tĩnh, như hình ảnh, trong thư mục gốc `/public`. Các tệp bên trong `/public` có thể được tham chiếu trong ứng dụng của bạn.

Với HTML thông thường, bạn sẽ thêm một hình ảnh như sau:

```html
<img
  src="/hero.png"
  alt="Ảnh chụp màn hình của dự án bảng điều khiển hiển thị phiên bản máy tính để bàn"
/>
```

Tuy nhiên, điều này có nghĩa là bạn phải thực hiện thủ công các việc sau:
- Đảm bảo hình ảnh của bạn phản hồi tốt trên các kích thước màn hình khác nhau.
- Chỉ định kích thước hình ảnh cho các thiết bị khác nhau.
- Ngăn chặn thay đổi bố cục khi hình ảnh đang tải.
- Tải chậm (Lazy load) các hình ảnh nằm ngoài khung hình (viewport) của người dùng.

Tối ưu hóa hình ảnh là một chủ đề lớn trong phát triển web. Thay vì triển khai các tối ưu hóa này một cách thủ công, bạn có thể sử dụng thành phần `next/image` để tự động tối ưu hóa hình ảnh của mình.

## Thành phần <Image>

Thành phần `<Image>` là một phần mở rộng của thẻ HTML `<img>`, và đi kèm với các tối ưu hóa hình ảnh tự động như:
- Tự động ngăn chặn thay đổi bố cục khi hình ảnh đang tải.
- Thay đổi kích thước hình ảnh để tránh gửi các hình ảnh lớn đến các thiết bị có khung hình nhỏ hơn.
- Tải chậm theo mặc định (hình ảnh chỉ tải khi chúng đi vào khung hình).
- Cung cấp hình ảnh ở các định dạng hiện đại như [WebP](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Image_types#webp) và [AVIF](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Image_types#avif_image), khi trình duyệt hỗ trợ.

## Thêm hình ảnh hero cho máy tính để bàn

Hãy sử dụng thành phần `<Image>`. Nếu bạn nhìn vào bên trong thư mục `/public`, bạn sẽ thấy có hai hình ảnh: `hero-desktop.png` và `hero-mobile.png`. Hai hình ảnh này hoàn toàn khác nhau và chúng sẽ được hiển thị tùy thuộc vào việc thiết bị của người dùng là máy tính để bàn hay điện thoại di động.

Trong tệp `/app/page.tsx`, hãy nhập thành phần từ `next/image`. Sau đó, thêm hình ảnh vào bên dưới phần chú thích:

```jsx
// /app/page.tsx
import Image from 'next/image';

export default function Page() {
  return (
    // ...
    <div className="flex items-center justify-center p-6 md:w-3/5 md:px-28 md:py-12">
      {/* Thêm hình ảnh Hero ở đây */}
      <Image
        src="/hero-desktop.png"
        width={1000}
        height={760}
        className="hidden md:block"
        alt="Ảnh chụp màn hình của dự án bảng điều khiển hiển thị phiên bản máy tính để bàn"
      />
    </div>
    //...
  );
}
```

Ở đây, bạn đang đặt `width` là `1000` và `height` là `760` pixel. Việc đặt chiều rộng và chiều cao cho hình ảnh là một thói quen tốt để tránh thay đổi bố cục, những giá trị này nên có tỷ lệ khung hình giống hệt với hình ảnh gốc.

Bạn cũng sẽ nhận thấy lớp `hidden` để loại bỏ hình ảnh khỏi DOM trên màn hình di động và `md:block` để hiển thị hình ảnh trên màn hình máy tính để bàn.

## Thực hành: Thêm hình ảnh hero cho điện thoại di động

Bây giờ đến lượt bạn! Bên dưới hình ảnh bạn vừa thêm, hãy thêm một thành phần `<Image>` khác cho `hero-mobile.png`.

- Hình ảnh nên có chiều rộng (`width`) là `560` và chiều cao (`height`) là `620` pixel.
- Nó nên được hiển thị trên màn hình di động và ẩn trên máy tính để bàn.

---

**Tuyệt vời!** Trang chủ của bạn hiện đã có phông chữ tùy chỉnh và hình ảnh hero được tối ưu hóa.
