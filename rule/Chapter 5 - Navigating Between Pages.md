# Chương 5: Điều hướng giữa các trang

Trong chương trước, bạn đã tạo layout và các trang cho bảng điều khiển. Bây giờ, hãy thêm một số liên kết để cho phép người dùng điều hướng giữa các tuyến đường trong dashboard.

### Trong chương này...
Dưới đây là các chủ đề chúng ta sẽ đề cập:
- Cách sử dụng thành phần `next/link`.
- Cách hiển thị liên kết đang hoạt động bằng hook `usePathname()`.
- Cách thức hoạt động của điều hướng trong Next.js.

## Tại sao cần tối ưu hóa điều hướng?

Để liên kết giữa các trang, theo truyền thống bạn sẽ sử dụng thẻ HTML `<a>`. Hiện tại, các liên kết ở thanh bên (sidebar) đang sử dụng thẻ `<a>`, nhưng hãy để ý điều gì xảy ra khi bạn điều hướng giữa các trang Trang chủ, Hóa đơn và Khách hàng trên trình duyệt của mình.

Bạn có thấy không?
Có một sự tải lại toàn bộ trang (full page refresh) mỗi khi bạn điều hướng!

## Thành phần <Link>

Trong Next.js, bạn có thể sử dụng thành phần `<Link />` để liên kết giữa các trang trong ứng dụng của mình. `<Link>` cho phép bạn thực hiện **điều hướng phía máy khách (client-side navigation)** bằng JavaScript.

Để sử dụng thành phần `<Link />`, hãy mở tệp `/app/ui/dashboard/nav-links.tsx` và nhập thành phần `Link` từ `next/link`. Sau đó, thay thế thẻ `<a>` bằng `<Link>`:

```tsx
// /app/ui/dashboard/nav-links.tsx
import {
  UserGroupIcon,
  HomeIcon,
  DocumentDuplicateIcon,
} from '@heroicons/react/24/outline';
import Link from 'next/link';

// ...

export default function NavLinks() {
  return (
    <>
      {links.map((link) => {
        const LinkIcon = link.icon;
        return (
          <Link
            key={link.name}
            href={link.href}
            className="flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3"
          >
            <LinkIcon className="w-6" />
            <p className="hidden md:block">{link.name}</p>
          </Link>
        );
      })}
    </>
  );
}
```

Như bạn có thể thấy, thành phần `Link` tương tự như thẻ `<a>`, nhưng thay vì dùng `<a href="...">`, bạn sử dụng `<Link href="...">`.

Lưu các thay đổi của bạn và kiểm tra trên trình duyệt. Bây giờ bạn đã có thể điều hướng giữa các trang mà không thấy trang bị tải lại toàn bộ. Mặc dù các phần của ứng dụng được kết xuất trên máy chủ, nhưng không có sự tải lại trang, giúp ứng dụng có cảm giác mượt mà như một ứng dụng web gốc (native web app). Tại sao lại như vậy?

### Chia nhỏ mã (Code splitting) và Tìm nạp trước (Prefetching)

Để cải thiện trải nghiệm điều hướng, Next.js tự động chia nhỏ mã ứng dụng của bạn theo từng phân đoạn tuyến đường. Điều này khác với các ứng dụng Single Page Application (SPA) truyền thống, nơi trình duyệt tải toàn bộ mã ứng dụng ngay lần tải trang đầu tiên.

Việc chia nhỏ mã theo tuyến đường giúp các trang trở nên độc lập. Nếu một trang cụ thể xảy ra lỗi, phần còn lại của ứng dụng vẫn hoạt động bình thường.

Hơn nữa, trong môi trường sản xuất (production), bất khi nào các thành phần `<Link>` xuất hiện trong khung hình của trình duyệt, Next.js sẽ tự động **tìm nạp trước (prefetch)** mã cho tuyến đường được liên kết ở chế độ nền. Khi người dùng nhấp vào liên kết, mã của trang đích đã được tải sẵn, giúp việc chuyển trang diễn ra gần như tức thì!

## Kỹ thuật: Hiển thị liên kết đang hoạt động

Một mô hình UI phổ biến là hiển thị liên kết đang hoạt động để cho người dùng biết họ đang ở trang nào. Để làm được điều này, bạn cần lấy đường dẫn hiện tại từ URL. Next.js cung cấp một hook gọi là `usePathname()` để bạn có thể kiểm tra đường dẫn.

Vì `usePathname()` là một React hook, bạn cần chuyển tệp `nav-links.tsx` thành một Thành phần Máy khách (Client Component). Thêm chỉ thị `"use client"` vào đầu tệp, sau đó nhập `usePathname` từ `next/navigation`:

```tsx
// /app/ui/dashboard/nav-links.tsx
'use client';

import {
  UserGroupIcon,
  HomeIcon,
  DocumentDuplicateIcon,
} from '@heroicons/react/24/outline';
import Link from 'next/link';
import { usePathname } from 'next/navigation';
import clsx from 'clsx';

// ...

export default function NavLinks() {
  const pathname = usePathname();
  return (
    <>
      {links.map((link) => {
        const LinkIcon = link.icon;
        return (
          <Link
            key={link.name}
            href={link.href}
            className={clsx(
              'flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3',
              {
                'bg-sky-100 text-blue-600': pathname === link.href,
              },
            )}
          >
            <LinkIcon className="w-6" />
            <p className="hidden md:block">{link.name}</p>
          </Link>
        );
      })}
    </>
  );
}
```

Lưu và kiểm tra trên trình duyệt. Bây giờ bạn sẽ thấy liên kết đang hoạt động được làm nổi bật với màu xanh.

---

**Chúc mừng!** Bạn đã học được cách liên kết giữa các trang và tận dụng tính năng điều hướng phía máy khách trong Next.js.
