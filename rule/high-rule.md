### 🛑 Tiêu chuẩn Audit "Tử thần" (Difficulty 1000%):

1.  **Render-Zero Tolerance**: Bất kỳ component nào re-render mà không có sự thay đổi dữ liệu hiển thị tương ứng sẽ bị coi là lỗi kiến trúc.
2.  **Logic-Density Optimization**: Code phải đạt mật độ logic cao nhất nhưng vẫn dễ hiểu. Nếu một tính năng tốn 20 dòng code trong khi có thể viết trong 5 dòng bằng một "pattern" tốt hơn, đó là code tốn kém.
3.  **Context-Splitting**: Không chấp nhận một Context duy nhất chứa quá nhiều loại dữ liệu khác nhau.
4.  **Hook-Purity**: Các hook phải hoàn toàn "tinh khiết", không được chứa logic UI hay các side-effect chồng chéo.
