# Chương 13: Cải thiện khả năng truy cập (Improving Accessibility)

Trong chương này, chúng ta sẽ tìm hiểu cách cải thiện khả năng truy cập cho ứng dụng, tập trung vào việc xác thực biểu mẫu phía máy chủ và hiển thị lỗi một cách dễ tiếp cận.

### Trong chương này...
Dưới đây là các chủ đề chúng ta sẽ đề cập:
- Cách sử dụng `eslint-plugin-jsx-a11y` với Next.js để thực hiện các thực hành tốt nhất về khả năng truy cập.
- Cách triển khai xác thực biểu mẫu phía máy chủ.
- Cách sử dụng hook `useActionState` của React để xử lý và hiển thị lỗi biểu mẫu cho người dùng.

## Khả năng truy cập (Accessibility) là gì?

Khả năng truy cập (A11y) đề cập đến việc thiết kế và triển khai các ứng dụng web mà mọi người đều có thể sử dụng, bao gồm cả những người khuyết tật. Các khía cạnh bao gồm điều hướng bằng bàn phím, HTML ngữ nghĩa (semantic HTML), văn bản thay thế cho hình ảnh (alt text), tỷ lệ tương phản màu sắc, v.v.

## Sử dụng ESLint accessibility plugin trong Next.js

Next.js tích hợp sẵn `eslint-plugin-jsx-a11y` để giúp phát hiện sớm các vấn đề về khả năng truy cập.

Cài đặt ESLint:
```bash
pnpm add -D eslint eslint-config-next
```

Sau đó chạy lệnh kiểm tra:
```bash
pnpm lint
```
Nếu bạn thiếu thuộc tính `alt` trong thẻ `<Image>`, ESLint sẽ đưa ra cảnh báo.

## Cải thiện khả năng truy cập cho biểu mẫu

Các thực hành tốt đã thực hiện:
- **Semantic HTML**: Sử dụng `<input>`, `<select>`, v.v., thay vì các thẻ `<div>`.
- **Labelling**: Sử dụng `<label>` và thuộc tính `htmlFor` để hỗ trợ công nghệ hỗ trợ (AT).
- **Focus Outline**: Hiển thị viền rõ ràng khi một phần tử được lấy nét (focus).

## Xác thực biểu mẫu phía máy chủ

Xác thực phía máy chủ giúp đảm bảo dữ liệu an toàn và nhất quán trước khi gửi vào cơ sở dữ liệu.

### Sử dụng hook useActionState

Chúng ta sử dụng `useActionState` để quản lý trạng thái của biểu mẫu (bao gồm các thông báo lỗi).

Trong thành phần biểu mẫu (`create-form.tsx`):

```tsx
'use client';

import { useActionState } from 'react';
import { createInvoice, State } from '@/app/lib/actions';

export default function Form({ customers }: { customers: CustomerField[] }) {
  const initialState: State = { message: null, errors: {} };
  const [state, formAction] = useActionState(createInvoice, initialState);

  return <form action={formAction}>...</form>;
}
```

### Cập nhật Server Action

Trong `actions.ts`, sử dụng Zod với `safeParse()` để xác thực dữ liệu:

```tsx
export type State = {
  errors?: {
    customerId?: string[];
    amount?: string[];
    status?: string[];
  };
  message?: string | null;
};

export async function createInvoice(prevState: State, formData: FormData) {
  const validatedFields = CreateInvoice.safeParse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });

  if (!validatedFields.success) {
    return {
      errors: validatedFields.error.flatten().fieldErrors,
      message: 'Missing Fields. Failed to Create Invoice.',
    };
  }
  // ...
}
```

## Hiển thị lỗi một cách dễ tiếp cận

Sử dụng các thuộc tính ARIA để thông báo lỗi cho người dùng sử dụng trình đọc màn hình:

```tsx
<select
  id="customer"
  name="customerId"
  aria-describedby="customer-error"
>
  {/* ... */}
</select>

<div id="customer-error" aria-live="polite" aria-atomic="true">
  {state.errors?.customerId &&
    state.errors.customerId.map((error: string) => (
      <p className="mt-2 text-sm text-red-500" key={error}>
        {error}
      </p>
    ))}
</div>
```

- `aria-describedby`: Liên kết phần tử nhập liệu với thông báo lỗi.
- `aria-live="polite"`: Thông báo cho trình đọc màn hình khi nội dung bên trong `div` thay đổi một cách lịch sự (không ngắt lời người dùng).

---

**Tổng kết:** Bạn đã học cách cải thiện khả năng truy cập bằng cách kết hợp ESLint, xác thực phía máy chủ với `useActionState` và sử dụng các thuộc tính ARIA để tạo ra trải nghiệm tốt hơn cho mọi người dùng.
