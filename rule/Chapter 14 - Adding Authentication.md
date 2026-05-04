# Chương 14: Thêm xác thực (Adding Authentication)

Trong chương này, chúng ta sẽ thêm tính năng xác thực vào ứng dụng dashboard bằng cách sử dụng NextAuth.js.

### Trong chương này...
Dưới đây là các chủ đề chúng ta sẽ đề cập:
- Xác thực (Authentication) là gì.
- Cách thêm xác thực vào ứng dụng bằng NextAuth.js.
- Cách sử dụng Middleware để chuyển hướng người dùng và bảo vệ các tuyến đường của bạn.
- Cách sử dụng hook `useActionState` của React để xử lý trạng thái chờ (pending states) và lỗi biểu mẫu.

## Xác thực (Authentication) vs Phân quyền (Authorization)

- **Xác thực (Authentication)**: Kiểm tra xem người dùng có đúng là người mà họ tuyên bố hay không (Bạn là ai?). Ví dụ: Đăng nhập bằng tên người dùng và mật khẩu.
- **Phân quyền (Authorization)**: Xác định những phần nào của ứng dụng mà người dùng được phép sử dụng sau khi danh tính đã được xác nhận (Bạn được phép làm gì?).

## Thiết lập NextAuth.js

Cài đặt NextAuth.js:
```bash
pnpm i next-auth@beta
```

Tạo một khóa bí mật (`AUTH_SECRET`) và thêm nó vào tệp `.env`.

### Cấu hình NextAuth

Tạo tệp `auth.config.ts` ở gốc dự án:

```tsx
import type { NextAuthConfig } from 'next-auth';

export const authConfig = {
  pages: {
    signIn: '/login',
  },
  callbacks: {
    authorized({ auth, request: { nextUrl } }) {
      const isLoggedIn = !!auth?.user;
      const isOnDashboard = nextUrl.pathname.startsWith('/dashboard');
      if (isOnDashboard) {
        if (isLoggedIn) return true;
        return false; // Chuyển hướng người dùng chưa xác thực về trang login
      } else if (isLoggedIn) {
        return Response.redirect(new URL('/dashboard', nextUrl));
      }
      return true;
    },
  },
  providers: [], // Danh sách các tùy chọn đăng nhập
} satisfies NextAuthConfig;
```

### Bảo vệ tuyến đường với Middleware

Sử dụng Middleware để kiểm tra xác thực trước khi kết xuất trang, giúp tăng cường bảo mật và hiệu suất.

Tạo tệp `middleware.ts` (tutorial gốc gọi là `proxy.ts` nhưng Next.js chuẩn sử dụng `middleware.ts`):

```tsx
import NextAuth from 'next-auth';
import { authConfig } from './auth.config';

export default NextAuth(authConfig).auth;

export const config = {
  matcher: ['/((?!api|_next/static|_next/image|.*\\.png$).*)'],
};
```

## Thêm Credentials provider

Nhà cung cấp thông tin xác thực (Credentials provider) cho phép người dùng đăng nhập bằng email và mật khẩu.

Trong tệp `auth.ts`:
1. Sử dụng `zod` để xác thực email và mật khẩu.
2. Tạo hàm `getUser` để truy vấn người dùng từ cơ sở dữ liệu.
3. Sử dụng `bcrypt.compare` để so khớp mật khẩu đã mã hóa.

```tsx
// app/auth.ts
import Credentials from 'next-auth/providers/credentials';
import bcrypt from 'bcrypt';
// ... logic authorize ...
```

## Kết nối logic với Biểu mẫu đăng nhập

Sử dụng Server Action `authenticate` và hook `useActionState` trong thành phần `LoginForm` để xử lý quá trình đăng nhập và hiển thị thông báo lỗi.

```tsx
// app/lib/actions.ts
export async function authenticate(
  prevState: string | undefined,
  formData: FormData,
) {
  try {
    await signIn('credentials', formData);
  } catch (error) {
    // Xử lý lỗi xác thực...
  }
}
```

## Đăng xuất

Để đăng xuất, hãy gọi hàm `signOut` từ `auth.ts` bên trong một thẻ `<form>` trong thành phần `SideNav`.

---

**Tổng kết:** Bạn đã bảo vệ các tuyến đường của mình và triển khai hệ thống đăng nhập/đăng xuất an toàn bằng NextAuth.js.
