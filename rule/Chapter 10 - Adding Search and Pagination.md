# Chương 10: Thêm Tìm kiếm và Phân trang (Adding Search and Pagination)

Trong chương này, chúng ta sẽ học cách thêm tính năng tìm kiếm và phân trang vào trang `/invoices`.

### Trong chương này...
Dưới đây là các chủ đề chúng ta sẽ đề cập:
- Tìm hiểu cách sử dụng các API của Next.js: `useSearchParams`, `usePathname`, và `useRouter`.
- Triển khai tìm kiếm và phân trang bằng cách sử dụng các tham số tìm kiếm của URL (URL search params).

## Tại sao nên sử dụng URL search params?

Chúng ta sẽ sử dụng URL search params để quản lý trạng thái tìm kiếm. Một số lợi ích của việc này là:
- **URL có thể đánh dấu và chia sẻ**: Người dùng có thể lưu trạng thái hiện tại của ứng dụng (bao gồm các truy vấn tìm kiếm) để tham khảo hoặc chia sẻ.
- **Kết xuất phía máy chủ (Server-side rendering)**: Các tham số URL có thể được tiêu thụ trực tiếp trên máy chủ để kết xuất trạng thái ban đầu.
- **Phân tích và theo dõi**: Dễ dàng theo dõi hành vi người dùng mà không cần thêm logic phức tạp ở phía máy khách.

## Triển khai Tìm kiếm

Các bước triển khai:
1. Ghi lại dữ liệu nhập của người dùng.
2. Cập nhật URL với các tham số tìm kiếm.
3. Giữ URL đồng bộ với trường nhập liệu.
4. Cập nhật bảng để phản ánh truy vấn tìm kiếm.

### 1. Ghi lại dữ liệu nhập của người dùng

Trong thành phần `<Search>` (`/app/ui/search.tsx`), thêm một hàm `handleSearch` và lắng nghe sự kiện `onChange` trên thẻ `<input>`.

```tsx
'use client';

export default function Search({ placeholder }: { placeholder: string }) {
  function handleSearch(term: string) {
    console.log(term);
  }

  return (
    <input
      placeholder={placeholder}
      onChange={(e) => handleSearch(e.target.value)}
    />
  );
}
```

### 2. Cập nhật URL với các tham số tìm kiếm

Sử dụng `useSearchParams`, `usePathname`, và `useRouter` từ `next/navigation`:

```tsx
'use client';

import { useSearchParams, usePathname, useRouter } from 'next/navigation';

export default function Search({ placeholder }: { placeholder: string }) {
  const searchParams = useSearchParams();
  const pathname = usePathname();
  const { replace } = useRouter();

  function handleSearch(term: string) {
    const params = new URLSearchParams(searchParams);
    if (term) {
      params.set('query', term);
    } else {
      params.delete('query');
    }
    replace(`${pathname}?${params.toString()}`);
  }
  // ...
}
```

### 3. Giữ URL và trường nhập liệu đồng bộ

Để đảm bảo trường nhập liệu đồng bộ với URL khi chia sẻ, bạn có thể truyền `defaultValue` bằng cách đọc từ `searchParams.get('query')`.

## Debouncing (Chống rung)

Để tránh việc gửi yêu cầu đến cơ sở dữ liệu trên mỗi phím nhấn, chúng ta sử dụng kỹ thuật **Debouncing**. Chúng ta sẽ sử dụng thư viện `use-debounce`.

```bash
pnpm i use-debounce
```

Cập nhật hàm `handleSearch` sử dụng `useDebouncedCallback`:

```tsx
import { useDebouncedCallback } from 'use-debounce';

const handleSearch = useDebouncedCallback((term) => {
  const params = new URLSearchParams(searchParams);
  params.set('page', '1');
  if (term) {
    params.set('query', term);
  } else {
    params.delete('query');
  }
  replace(`${pathname}?${params.toString()}`);
}, 300);
```

## Triển khai Phân trang

Tương tự như tìm kiếm, chúng ta sử dụng URL params để quản lý phân trang.

1. Trong `page.tsx`, lấy `totalPages` bằng hàm `fetchInvoicesPages(query)`.
2. Trong thành phần `<Pagination>`, sử dụng `usePathname` và `useSearchParams` để tạo URL cho từng trang bằng hàm `createPageURL`.

```tsx
const createPageURL = (pageNumber: number | string) => {
  const params = new URLSearchParams(searchParams);
  params.set('page', pageNumber.toString());
  return `${pathname}?${params.toString()}`;
};
```

---

**Tổng kết:** Bạn đã học cách xử lý tìm kiếm và phân trang bằng tham số URL thay vì trạng thái phía máy khách (client state), giúp ứng dụng hoạt động hiệu quả và thân thiện với người dùng hơn.
