# Chương 12: Xử lý lỗi (Handling Errors)

Trong chương này, chúng ta sẽ học cách xử lý lỗi một cách khéo léo bằng cách sử dụng các câu lệnh `try/catch` của JavaScript và các API của Next.js cho các ngoại lệ chưa được bắt (uncaught exceptions).

### Trong chương này...
Dưới đây là các chủ đề chúng ta sẽ đề cập:
- Cách sử dụng tệp đặc biệt `error.tsx` để bắt lỗi trong các phân đoạn tuyến đường của bạn và hiển thị giao diện dự phòng cho người dùng.
- Cách sử dụng hàm `notFound` và tệp `not-found` để xử lý lỗi 404 (cho các tài nguyên không tồn tại).

## Sử dụng try/catch trong Server Actions

Trước tiên, hãy thêm các câu lệnh `try/catch` vào Server Actions của bạn để xử lý lỗi một cách khéo léo.

**Lưu ý:** `redirect` nên được gọi bên ngoài khối `try/catch`. Điều này là do `redirect` hoạt động bằng cách ném ra một lỗi, lỗi này sẽ bị khối `catch` bắt lại nếu nằm bên trong.

```typescript
// app/lib/actions.ts
export async function createInvoice(formData: FormData) {
  // ...
  try {
    await sql`
      INSERT INTO invoices (customer_id, amount, status, date)
      VALUES (${customerId}, ${amountInCents}, ${status}, ${date})
    `;
  } catch (error) {
    return {
      message: 'Database Error: Failed to Create Invoice.',
    };
  }

  revalidatePath('/dashboard/invoices');
  redirect('/dashboard/invoices');
}
```

## Xử lý tất cả các lỗi với error.tsx

Tệp `error.tsx` có thể được sử dụng để xác định ranh giới UI cho một phân đoạn tuyến đường. Nó đóng vai trò là nơi bắt tất cả các lỗi không mong muốn và cho phép bạn hiển thị một giao diện dự phòng.

Tạo tệp `error.tsx` bên trong thư mục `/dashboard/invoices`:

```tsx
// /dashboard/invoices/error.tsx
'use client';

import { useEffect } from 'react';

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  useEffect(() => {
    console.error(error);
  }, [error]);

  return (
    <main className="flex h-full flex-col items-center justify-center">
      <h2 className="text-center">Đã xảy ra lỗi!</h2>
      <button
        className="mt-4 rounded-md bg-blue-500 px-4 py-2 text-sm text-white transition-colors hover:bg-blue-400"
        onClick={() => reset()}
      >
        Thử lại
      </button>
    </main>
  );
}
```

- `"use client"`: `error.tsx` cần phải là một Client Component.
- `reset`: Hàm này sẽ cố gắng kết xuất lại phân đoạn tuyến đường để phục hồi sau lỗi.

## Xử lý lỗi 404 với notFound

Một cách khác để xử lý lỗi là sử dụng hàm `notFound`. Trong khi `error.tsx` hữu ích để bắt các lỗi không mong muốn, `notFound` có thể được sử dụng khi bạn cố gắng lấy một tài nguyên không tồn tại.

Ví dụ, trong trang chỉnh sửa hóa đơn:

```tsx
// /dashboard/invoices/[id]/edit/page.tsx
import { fetchInvoiceById, fetchCustomers } from '@/app/lib/data';
import { notFound } from 'next/navigation';

export default async function Page(props: { params: Promise<{ id: string }> }) {
  const params = await props.params;
  const id = params.id;
  const [invoice, customers] = await Promise.all([
    fetchInvoiceById(id),
    fetchCustomers(),
  ]);

  if (!invoice) {
    notFound();
  }
  // ...
}
```

Để hiển thị UI tùy chỉnh cho lỗi 404, hãy tạo tệp `not-found.tsx` bên trong thư mục `/edit`:

```tsx
// /dashboard/invoices/[id]/edit/not-found.tsx
import Link from 'next/link';
import { FaceFrownIcon } from '@heroicons/react/24/outline';

export default function NotFound() {
  return (
    <main className="flex h-full flex-col items-center justify-center gap-2">
      <FaceFrownIcon className="w-10 text-gray-400" />
      <h2 className="text-xl font-semibold">404 Not Found</h2>
      <p>Không thể tìm thấy hóa đơn yêu cầu.</p>
      <Link
        href="/dashboard/invoices"
        className="mt-4 rounded-md bg-blue-500 px-4 py-2 text-sm text-white transition-colors hover:bg-blue-400"
      >
        Quay lại
      </Link>
    </main>
  );
}
```

Lưu ý rằng `notFound` sẽ có quyền ưu tiên cao hơn `error.tsx`, vì vậy bạn có thể sử dụng nó khi muốn xử lý các lỗi cụ thể hơn.

---

**Tổng kết:** Bạn đã học được cách xử lý lỗi khéo léo trong ứng dụng bằng `error.tsx` cho các lỗi chung và `notFound` cho các tài nguyên không tồn tại.
