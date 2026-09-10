# Kids Coloring — Mobile mockup

Mockup tương tác dành cho app mobile trước khi triển khai Flutter/Android. Giao diện dọc tối đa 480 CSS px, dùng toàn bộ chiều rộng trên điện thoại.

## Mở xem thử

Tại thư mục gốc repository:

```bash
python3 -m http.server 8000 --directory mockup
```

Mở http://localhost:8000 trên trình duyệt. Để xem bằng điện thoại cùng Wi-Fi, dùng địa chỉ IP LAN của máy chạy server và cổng 8000.

## Các màn và thao tác

- Chọn tranh: cáo, khủng long, tên lửa; lọc theo chủ đề.
- Chạm vùng để tô, 12 màu, hoàn tác/làm lại, xem mẫu và tô lại.
- Tự lưu tiến trình trên trình duyệt, xem My art và xuất ảnh PNG.
- Khu vực phụ huynh, giảm chuyển động và luồng mua mô phỏng.
- Có thể dùng phím mũi tên để chọn vùng trên canvas, Enter/Space để tô.

## Cấu trúc

- `index.html`: điểm vào.
- `app.js`: chuyển màn, tô màu, lưu local và xuất ảnh.
- `styles.css`: CSS nền.
- `mobile.css`: lớp thiết kế ưu tiên điện thoại.
- `tokens.css`: màu, font và thông số giao diện.
- `assets/`: ba tranh mẫu PNG.

## Phạm vi

Đây là mockup HTML/CSS/JavaScript, chưa phải app Flutter hoặc APK. Chưa có backend, tài khoản, thanh toán thật hoặc đồng bộ dữ liệu. Dữ liệu có thể mất khi xóa dữ liệu trình duyệt. Cổng phép tính là tương tác minh họa, không xác minh danh tính phụ huynh.

Tranh mẫu do AI tạo; các vùng tô được nhận diện từ đường viền raster, chưa dùng manifest/RLE của kế hoạch production. Font Nunito có font hệ thống dự phòng khi không tải được Google Fonts.

## Thiết kế và kiểm tra

Áp dụng hướng dẫn redesign của [Hallmark](https://github.com/nutlope/hallmark), giữ nhận diện xanh/Nunito. Bố cục gồm tranh nổi bật, bộ sưu tập gọn và bảng màu phía dưới.

Đã kiểm tra cú pháp JavaScript, chọn màu/trạng thái thẻ tranh, đường dẫn tài sản và kích thước vùng chạm theo bố cục 320/375/414/768 px. Chưa kiểm thử trực quan hoặc tương tác trên trình duyệt/điện thoại thật.
