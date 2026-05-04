# Chương 11: Thay đổi dữ liệu (Mutating Data)

Trong chương này, chúng ta sẽ học cách tạo, cập nhật và xóa hóa đơn bằng React Server Actions.

### Trong chương này...
Dưới đây là các chủ đề chúng ta sẽ đề cập:
- React Server Actions là gì và cách sử dụng chúng để thay đổi dữ liệu.
- Cách làm việc với biểu mẫu (forms) và Server Components.
- Các thực hành tốt nhất khi làm việc với đối tượng `FormData` gốc, bao gồm xác thực kiểu dữ liệu.
- Cách xác thực lại bộ nhớ đệm (cache) phía máy khách bằng API `revalidatePath`.
- Cách tạo các phân đoạn tuyến đường động (dynamic route segments) với ID cụ thể.

## Server Actions là gì?

React Server Actions cho phép bạn chạy mã không đồng bộ trực tiếp trên máy chủ. Chúng loại bỏ việc phải tạo các điểm cuối API để thay đổi dữ liệu. Thay vào đó, bạn viết các hàm không đồng bộ thực thi trên máy chủ và có thể được gọi từ các Client hoặc Server Components của bạn.

## Sử dụng biểu mẫu với Server Actions

Trong React, bạn có thể sử dụng thuộc tính `action` trong phần tử `<form>` để gọi các hành động. Hành động này sẽ tự động nhận đối tượng `FormData` chứa dữ liệu đã nhập.

```tsx
// Server Component
export default function Page() {
  async function create(formData: FormData) {
    'use server';
    // Logic để thay đổi dữ liệu...
  }

  return <form action={create}>...</form>;
}
```

## Tạo một hóa đơn

Các bước để tạo một hóa đơn mới:
1. Tạo một biểu mẫu để ghi lại dữ liệu nhập của người dùng.
2. Tạo một Server Action và gọi nó từ biểu mẫu.
3. Bên trong Server Action, trích xuất dữ liệu từ đối tượng `formData`.
4. Xác thực và chuẩn bị dữ liệu để chèn vào cơ sở dữ liệu.
5. Chèn dữ liệu và xử lý các lỗi.
6. Xác thực lại bộ nhớ đệm và chuyển hướng người dùng quay lại trang hóa đơn.

### Trích xuất dữ liệu từ FormData

Sử dụng phương thức `.get(name)` để lấy giá trị từ `formData`.

```tsx
// app/lib/actions.ts
'use server';

import { z } from 'zod';
import { revalidatePath } from 'next/cache';
import { redirect } from 'next/navigation';
import postgres from 'postgres';

const sql = postgres(process.env.POSTGRES_URL!, { ssl: 'require' });

const FormSchema = z.object({
  id: z.string(),
  customerId: z.string(),
  amount: z.coerce.number(),
  status: z.enum(['pending', 'paid']),
  date: z.string(),
});

const CreateInvoice = FormSchema.omit({ id: true, date: true });

export async function createInvoice(formData: FormData) {
  const { customerId, amount, status } = CreateInvoice.parse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });

  const amountInCents = amount * 100;
  const date = new Date().toISOString().split('T')[0];

  await sql`
    INSERT INTO invoices (customer_id, amount, status, date)
    VALUES (${customerId}, ${amountInCents}, ${status}, ${date})
  `;

  revalidatePath('/dashboard/invoices');
  redirect('/dashboard/invoices');
}
```

## Cập nhật một hóa đơn

Các bước để cập nhật một hóa đơn:
1. Tạo một phân đoạn tuyến đường động mới với ID hóa đơn.
2. Đọc ID hóa đơn từ các tham số trang (`params`).
3. Lấy dữ liệu hóa đơn cụ thể từ cơ sở dữ liệu.
4. Điền sẵn dữ liệu vào biểu mẫu.
5. Cập nhật dữ liệu hóa đơn trong cơ sở dữ liệu.

### Tuyến đường động (Dynamic Routes)

Next.js cho phép bạn tạo các phân đoạn tuyến đường động bằng cách bọc tên thư mục trong dấu ngoặc vuông, ví dụ: `[id]`.

Ví dụ về Server Action để cập nhật:

```tsx
// app/lib/actions.ts
const UpdateInvoice = FormSchema.omit({ id: true, date: true });

export async function updateInvoice(id: string, formData: FormData) {
  const { customerId, amount, status } = UpdateInvoice.parse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });

  const amountInCents = amount * 100;

  await sql`
    UPDATE invoices
    SET customer_id = ${customerId}, amount = ${amountInCents}, status = ${status}
    WHERE id = ${id}
  `;

  revalidatePath('/dashboard/invoices');
  redirect('/dashboard/invoices');
}
```

## Xóa một hóa đơn

Để xóa một hóa đơn, bạn tạo một Server Action và gọi nó từ một nút bấm bọc trong một thẻ `<form>`.

```tsx
// app/lib/actions.ts
export async function deleteInvoice(id: string) {
  await sql`DELETE FROM invoices WHERE id = ${id}`;
  revalidatePath('/dashboard/invoices');
}
```

---

**Tổng kết:** Bạn đã học được cách sử dụng Server Actions để thay đổi dữ liệu, cách cập nhật bộ nhớ đệm bằng `revalidatePath` và cách chuyển hướng người dùng bằng `redirect`.
