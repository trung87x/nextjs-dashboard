# Chương 15: Thêm Metadata (Adding Metadata)

Metadata đóng vai trò quan trọng đối với SEO (tối ưu hóa công cụ tìm kiếm) và khả năng chia sẻ liên kết. Trong chương này, chúng ta sẽ học cách thêm metadata vào ứng dụng Next.js.

### Trong chương này...
Dưới đây là các chủ đề chúng ta sẽ đề cập:
- Metadata là gì và tại sao nó lại quan trọng.
- Các loại metadata phổ biến.
- Cách thêm hình ảnh Open Graph và Favicon bằng metadata.
- Cách sử dụng Metadata API của Next.js.

## Metadata là gì?

Metadata cung cấp các chi tiết bổ sung về một trang web. Nó không hiển thị trực tiếp cho người dùng mà nằm ẩn bên trong mã HTML (thường là trong thẻ `<head>`). Thông tin này rất quan trọng đối với các công cụ tìm kiếm và các hệ thống khác cần hiểu nội dung trang web của bạn.

## Tại sao Metadata lại quan trọng?

- **SEO**: Giúp công cụ tìm kiếm chỉ mục trang web hiệu quả hơn, cải thiện thứ hạng.
- **Mạng xã hội**: Các metadata như Open Graph giúp hiển thị các bản xem trước hấp dẫn khi liên kết được chia sẻ trên Facebook, Twitter, v.v.

## Các loại Metadata phổ biến

- **Tiêu đề (Title)**: Tiêu đề hiển thị trên tab trình duyệt.
- **Mô tả (Description)**: Tóm tắt nội dung trang, thường hiển thị trong kết quả tìm kiếm.
- **Open Graph**: Cải thiện cách đại diện trang web khi chia sẻ trên mạng xã hội.
- **Favicon**: Biểu tượng nhỏ hiển thị trên tab trình duyệt hoặc thanh địa chỉ.

## Metadata API trong Next.js

Next.js cung cấp Metadata API mạnh mẽ với hai cách triển khai:
1. **Dựa trên cấu hình (Config-based)**: Xuất một đối tượng `metadata` tĩnh hoặc hàm `generateMetadata` động trong tệp `layout.js` hoặc `page.js`.
2. **Dựa trên tệp (File-based)**: Sử dụng các tệp đặc biệt như `favicon.ico`, `opengraph-image.jpg`, `robots.txt`, `sitemap.xml`.

### Thêm Favicon và Hình ảnh Open Graph

Chỉ cần di chuyển tệp `favicon.ico` và `opengraph-image.jpg` vào thư mục gốc của `/app`. Next.js sẽ tự động nhận diện và sử dụng chúng.

### Tiêu đề trang và Mô tả

Trong `layout.tsx` gốc:

```tsx
import { Metadata } from 'next';

export const metadata: Metadata = {
  title: {
    template: '%s | Acme Dashboard',
    default: 'Acme Dashboard',
  },
  description: 'Bảng điều khiển chính thức của khóa học Next.js.',
  metadataBase: new URL('https://next-learn-dashboard.vercel.sh'),
};
```

Trong một trang cụ thể (ví dụ: `/dashboard/invoices/page.tsx`):

```tsx
export const metadata: Metadata = {
  title: 'Invoices',
};
```

Kết quả tiêu đề hiển thị sẽ là: `Invoices | Acme Dashboard`. Ký tự `%s` trong template sẽ được thay thế bằng tiêu đề cụ thể của trang.

---

**Tổng kết:** Bạn đã hoàn thành việc thêm metadata cho ứng dụng của mình, giúp nó chuyên nghiệp hơn và thân thiện hơn với các công cụ tìm kiếm.
