# Chương 8: Kết xuất Tĩnh và Động (Static and Dynamic Rendering)

Trong chương trước, bạn đã lấy dữ liệu cho trang Tổng quan Dashboard. Tuy nhiên, chúng ta đã thảo luận sơ qua về hai hạn chế của thiết lập hiện tại:
1. Các yêu cầu dữ liệu đang tạo ra một "waterfall" ngoài ý muốn.
2. Dashboard đang ở trạng thái tĩnh, vì vậy mọi cập nhật dữ liệu sẽ không được phản ánh trên ứng dụng của bạn.

### Trong chương này...
Dưới đây là các chủ đề chúng ta sẽ đề cập:
- Kết xuất tĩnh là gì và nó có thể cải thiện hiệu suất ứng dụng của bạn như thế nào.
- Kết xuất động là gì và khi nào nên sử dụng nó.
- Các cách tiếp cận khác nhau để làm cho dashboard của bạn trở nên động.
- Mô phỏng việc lấy dữ liệu chậm để xem điều gì xảy ra.

## Kết xuất Tĩnh (Static Rendering) là gì?

Với kết xuất tĩnh, việc lấy dữ liệu và kết xuất diễn ra trên máy chủ tại **thời điểm xây dựng** (khi bạn triển khai) hoặc khi [revalidating data](https://nextjs.org/docs/app/building-your-application/data-fetching/fetching-caching-and-revalidating#revalidating-data).

Bất cứ khi nào người dùng truy cập ứng dụng của bạn, kết quả đã được lưu trong bộ nhớ đệm (cached) sẽ được phục vụ. Một số lợi ích của kết xuất tĩnh:
- **Trang web nhanh hơn**: Nội dung được kết xuất trước có thể được lưu vào bộ nhớ đệm và phân phối toàn cầu. Điều này đảm bảo người dùng trên toàn thế giới có thể truy cập nội dung nhanh chóng và đáng tin cậy hơn.
- **Giảm tải cho máy chủ**: Vì nội dung đã được lưu vào bộ nhớ đệm, máy chủ của bạn không cần phải tạo nội dung động cho mỗi yêu cầu của người dùng.
- **SEO**: Nội dung được kết xuất trước giúp các trình thu thập thông tin của công cụ tìm kiếm dễ dàng lập chỉ mục hơn.

Kết xuất tĩnh rất hữu ích cho giao diện người dùng không có dữ liệu hoặc dữ liệu được chia sẻ giữa các người dùng, chẳng hạn như một bài đăng blog tĩnh hoặc trang sản phẩm. Nó có thể không phù hợp cho một dashboard có dữ liệu cá nhân hóa và được cập nhật thường xuyên.

## Kết xuất Động (Dynamic Rendering) là gì?

Với kết xuất động, nội dung được kết xuất trên máy chủ cho từng người dùng tại **thời điểm yêu cầu** (khi người dùng truy cập trang). Một số lợi ích của kết xuất động:
- **Dữ liệu thời gian thực**: Cho phép ứng dụng hiển thị dữ liệu thời gian thực hoặc dữ liệu được cập nhật thường xuyên.
- **Nội dung dành riêng cho người dùng**: Dễ dàng phục vụ nội dung được cá nhân hóa (như dashboard hoặc hồ sơ người dùng) và cập nhật dữ liệu dựa trên tương tác của người dùng.
- **Thông tin tại thời điểm yêu cầu**: Cho phép bạn truy cập thông tin chỉ có thể biết được tại thời điểm yêu cầu, chẳng hạn như cookie hoặc các tham số tìm kiếm (search parameters) của URL.

## Mô phỏng việc lấy dữ liệu chậm

Ứng dụng dashboard chúng ta đang xây dựng là động. Tuy nhiên, vẫn còn một vấn đề: điều gì xảy ra nếu một yêu cầu dữ liệu chậm hơn tất cả các yêu cầu khác?

Hãy mô phỏng việc lấy dữ liệu chậm. Trong `app/lib/data.ts`, bỏ chú thích cho `console.log` và `setTimeout` bên trong hàm `fetchRevenue()`:

```typescript
// app/lib/data.ts
export async function fetchRevenue() {
  try {
    // Chúng ta trì hoãn phản hồi giả tạo cho mục đích demo.
    // Đừng làm điều này trong môi trường sản xuất thực tế :)
    console.log('Đang lấy dữ liệu doanh thu...');
    await new Promise((resolve) => setTimeout(resolve, 3000));

    const data = await sql<Revenue[]>`SELECT * FROM revenue`;

    console.log('Việc lấy dữ liệu hoàn tất sau 3 giây.');

    return data;
  } catch (error) {
    console.error('Database Error:', error);
    throw new Error('Không thể lấy dữ liệu doanh thu.');
  }
}
```

Bây giờ hãy mở [http://localhost:3000/dashboard/](http://localhost:3000/dashboard/) và để ý xem trang mất bao lâu để tải.

Việc thêm độ trễ 3 giây giả tạo này mô phỏng một yêu cầu dữ liệu chậm. Kết quả là toàn bộ trang web của bạn bị chặn không hiển thị giao diện cho người dùng trong khi dữ liệu đang được lấy. Điều này dẫn đến một thách thức chung:

**Với kết xuất động, ứng dụng của bạn chỉ nhanh bằng yêu cầu dữ liệu chậm nhất của nó.**

---

**Làm tốt lắm!** Bạn vừa tìm hiểu về kết xuất tĩnh và động trong Next.js. Trong chương tiếp theo, chúng ta sẽ xem cách cải thiện trải nghiệm người dùng khi có các yêu cầu dữ liệu chậm.
