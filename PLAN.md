# Kids Coloring — Kế hoạch phát triển Android + backend AI

Phiên bản: 2.1 — Cập nhật: 10/09/2026

Repo mục tiêu: https://github.com/thanhtungtav4/Kids-Coloring

Tài liệu kỹ thuật viết bằng tiếng Việt cho đội phát triển; sản phẩm ưu tiên tiếng Anh cho thị trường quốc tế.

Tên dự án nội bộ: Kids Coloring. Tên thương mại chưa chốt. Tintilo chỉ là ý tưởng, chưa kiểm tra nhãn hiệu, tên miền hoặc tên trên cửa hàng.

Bản 2.1 tiếp tục định hướng trẻ em; giữ bản thử ở một chế độ và 10 tranh; bổ sung pipeline AI chỉ publish sau duyệt, contract cho `app/`/`backend/`, và giả thuyết thương mại Free + Premium theo tháng với quảng cáo có điều kiện.

## 1. Mục tiêu và quyết định nền tảng

Xây ứng dụng tô màu dành cho trẻ em: bé chọn một bức tranh, chọn màu rồi chạm vào từng vùng để hoàn thiện tranh. Người chơi là trẻ; người quyết định cài đặt và mua là phụ huynh. Android trước; có thể mở rộng iOS sau. Backend quản lý nội dung, gọi AI tạo tranh, kiểm duyệt, phát hành bộ tranh và xác minh mua hàng nếu triển khai thu phí.

AI là công cụ sản xuất nội dung cho quản trị viên, không phải chatbot hoặc ô nhập prompt cho trẻ em. Ảnh AI phải qua kiểm tra tự động và duyệt người trước khi được đưa vào catalog mà app nhìn thấy.

Mô hình thương mại cần kiểm chứng là **Free + Premium theo tháng**: người dùng Free có một phần tranh và quảng cáo không gây gián đoạn ở Library; Premium mở kho tranh, tranh mới và không có quảng cáo. Bản thử kỹ thuật vẫn tắt cả quảng cáo lẫn thanh toán.

### Giả định lập kế hoạch

- Một lập trình viên chính, đã quen Laravel; Flutter và xử lý ảnh có thể cần học thêm.
- Đã chốt: phục vụ trẻ em, định hướng quốc tế. Đề xuất thiết kế ban đầu: 4–8 tuổi, hai mức 4–5 và 6–8; kiểm chứng riêng từng nhóm với phụ huynh.
- Tiếng Anh là ngôn ngữ sản phẩm đầu tiên theo đề xuất triển khai quốc tế; mọi chuỗi UI dùng khóa dịch. Tiếng Việt hỗ trợ kiểm thử nội bộ, thêm ngôn ngữ khác theo nhu cầu.
- Quốc gia phát hành đầu tiên chưa chốt; không mặc định mở toàn bộ thị trường. Chọn một nhóm quốc gia sau đánh giá nội dung, hỗ trợ, dữ liệu và chi phí thu hút người dùng.
- Bé không cần đăng nhập để tô; tiến trình lưu trên máy. Tài khoản phụ huynh khi mua là phương án cần chốt riêng ở mục 11.3.
- Phát hành thử APK; bản thương mại ưu tiên Google Play, không lấy phân phối APK ngoài cửa hàng làm mô hình chính.
- Kiếm tiền là mục tiêu đã chốt; giả thuyết chính là Free + Premium theo tháng, cần kiểm chứng với phụ huynh. Gói mua một lần/lifetime là phương án dự phòng. Quảng cáo chỉ được bật sau khi kiểm tra chính sách trẻ em, vị trí hiển thị và chất lượng quảng cáo; tắt trong bản thử.
- Những con số thời gian, hiệu năng và chi phí trong tài liệu là giả định/mục tiêu nội bộ, không phải báo giá hoặc cam kết doanh thu.

### Nguyên tắc giữ phạm vi

1. Trải nghiệm tô phải tốt trước khi mở rộng kho tranh.
2. Nội dung mới tải về không được làm hỏng tranh đang tô.
3. Không xuất bản ảnh AI chưa duyệt.
4. Không để AI chạy trong request tải tranh của người chơi.
5. Không dùng lựa chọn độ khó thay cho xác định nhóm tuổi hoặc sự đồng ý của phụ huynh.
6. Không xem mã cài đặt ngẫu nhiên là dữ liệu mặc nhiên vô danh.

## 2. Phạm vi MVP và phần để sau

| Hạng mục | MVP bắt buộc | Để sau |
|---|---|---|
| Thư viện | Chủ đề, độ khó, tranh mới, tranh đang tô | Tìm kiếm nâng cao, đề xuất cá nhân |
| Cơ chế tô | Chạm đổ màu, hoàn tác/làm lại, bảng màu | Cọ vẽ, texture, gradient do người chơi chọn |
| Cách chơi | Tô tự do theo vùng, ảnh mẫu bật/tắt, không chấm sai | Tô theo số nếu thử nghiệm cho thấy cần; không ưu tiên thi đấu |
| Nội dung | Bản thử: 10 tranh, 3 chủ đề; bản thương mại: mục tiêu 30 tranh | Hàng trăm tranh, thêm chủ đề theo nhu cầu |
| Offline | 6 tranh có sẵn; 4 tranh tải từ backend ở bản thử | Đồng bộ tiến trình nhiều thiết bị |
| AI | Tạo bản nháp, xử lý vùng, duyệt thủ công | Tạo tranh trực tiếp cho người chơi |
| Thu phí | Bản thử tắt thanh toán; chuẩn bị một sản phẩm Premium theo tháng trên Play Console test | Gói lifetime, bộ mua một lần, tiền ảo |
| Quảng cáo | Bản thử tắt quảng cáo; chỉ pilot banner/nội dung không gây gián đoạn cho Free | Interstitial dày, quảng cáo trong editor, rewarded tự động |
| Tài khoản | Alpha: chỉ tài khoản admin, bé không đăng nhập | Tài khoản phụ huynh là lựa chọn có điều kiện trước commerce |
| Hạ tầng | Một backend, worker riêng theo tiến trình, storage | Microservices, Kubernetes, GPU tự vận hành |
| Chia sẻ | Lưu ảnh cục bộ trong khu vực phù hợp | Feed cộng đồng, upload tranh, bình luận |

Bản thử có 10 tranh: động vật 4, trái cây 3, phương tiện 3; 5 tranh đơn giản và 5 tranh nhiều chi tiết hơn. Tranh cho nhóm 4–5 tuổi dự kiến 5–12 vùng lớn; nhóm 6–8 tuổi dự kiến 12–30 vùng. Đây là ngưỡng thử nghiệm, không phải chuẩn phát triển trẻ em.

Bản thương mại dự kiến 30 tranh, thêm đại dương, khủng long và đồ chơi. Tỷ lệ miễn phí/trả phí chỉ chốt sau thử với phụ huynh; cấu hình 15 miễn phí + 15 trả phí là một phương án, không phải điều kiện cứng.

## 3. Thiết kế trải nghiệm người chơi

### 3.1 Trang chủ

- Hiển thị tranh để chọn ngay; không bắt đi qua nhiều màn chọn tuổi, độ khó rồi mới xem ảnh.
- Bộ lọc: Tất cả / Đơn giản / Nhiều chi tiết; chủ đề minh họa bằng ảnh. Phụ huynh chọn mức mặc định trong cài đặt, không hỏi ngày sinh bé chỉ để chọn tranh.
- Nhóm “Tô tiếp” đứng trước “Tranh mới”.
- Thẻ tranh gồm ảnh mẫu, trạng thái đã tải và tiến độ. Ở bản thử không có khóa; khi thương mại hóa, thông tin bán hàng tập trung trong khu vực phụ huynh.
- Chạm một tranh có quyền để tải và mở. Bé không bị đẩy thẳng vào màn mua hoặc lời mời xem quảng cáo; quản lý bộ tranh trong khu vực phụ huynh.
- Người dùng Free có thể thấy tối đa một vị trí quảng cáo không gây gián đoạn ở Library; Premium không tải hoặc hiển thị vị trí quảng cáo. Không đặt quảng cáo trong editor, trên canvas, trong popup hoàn thành hoặc ngay lúc mở app.
- Khi mất mạng, vẫn hiển thị tranh có sẵn và đã tải. Tranh chưa tải ghi rõ cần mạng.

### 3.2 Màn tô màu

- Tranh chiếm phần lớn màn hình; bảng màu ở dưới, nút lớn và ít chữ.
- Một ngón chạm để tô. Hai ngón phóng to/kéo nếu cần; khi bắt đầu gesture kéo, hủy hành động tô đang chờ. Tranh đơn giản phải tô được ở kích thước vừa màn hình, không phụ thuộc zoom.
- Phía trên: quay lại, hoàn tác, làm lại, bật/tắt mẫu.
- “Tô lại từ đầu” nằm trong menu và có xác nhận; không đặt sát nút tô thường dùng.
- Mẫu màu mở rộng theo yêu cầu, không che vùng tô.
- Không phạt chọn sai, không mất mạng chơi, không đếm ngược.
- Tắt âm/rung dễ dàng; hiệu ứng hoàn thành ngắn và có thể bỏ qua.

### 3.3 Một cách chơi trong bản đầu

Chọn màu bất kỳ, chạm vùng để tô; cho phép đổi màu một vùng nhiều lần. Ảnh mẫu chỉ tham khảo, không khóa màu và không báo sai. Bảng màu khởi đầu 12 màu lớn, không mở bộ chọn màu phức tạp cho bé.

Tiến độ dựa trên vùng đã có màu, kể cả màu trắng. Có nút hoàn thành bằng biểu tượng kèm nhãn ngắn; bé có thể kết thúc dù muốn để vùng trắng. Hoàn thành có hiệu ứng ngắn, không tự mở tranh mới hoặc ép tiếp tục.

Chế độ tô theo số không thuộc MVP. Schema có thể dự phòng trường mode nhưng không xây luồng thứ hai lúc này.

Ba màn chính: chọn tranh, tô màu, tranh của bé. Khu vực phụ huynh là màn tiện ích riêng cho cài đặt, mua/khôi phục, hỗ trợ, xuất ảnh; cổng người lớn không được xem là bằng chứng đồng ý thu thập dữ liệu.

### 3.4 Trạng thái và hành vi bắt buộc

| Tình huống | Hành vi |
|---|---|
| Thoát app hoặc bị hệ điều hành đóng | Mở lại đúng tranh và màu đã lưu |
| Mất mạng lúc tải | Giữ tiến trình, cho thử lại; chưa dùng gói tải dở |
| Hết dung lượng | Báo rõ; không tự xóa tranh đang tô |
| Tải lỗi checksum | Không mở gói; thử tải lại với giới hạn |
| Tranh cập nhật phiên bản | Tranh đang tô giữ bản cũ; bản mới là lựa chọn riêng |
| Chạm đường viền | Không tô sai vùng; có thể dùng vùng chạm hỗ trợ nhỏ đã kiểm thử |
| Tô màu trắng | Vẫn tính đã tô nhờ trạng thái riêng, không suy từ pixel màu trắng |
| Bấm mua nhưng hủy | Quay lại bình thường, không mất tiến trình |
| Xóa dữ liệu/gỡ ứng dụng | Tiến trình local có thể mất; nêu rõ từ đầu |

## 4. Kiến trúc kỹ thuật

### 4.1 Thành phần đề xuất

| Thành phần | Lựa chọn thiết kế | Trách nhiệm |
|---|---|---|
| Mobile | Flutter | Giao diện, tải nội dung, renderer, lưu local, tích hợp thanh toán |
| Backend | Laravel API | Catalog, quyền truy cập, điều phối tác vụ, quản trị |
| Admin | Filament, kiểm tra tương thích khi khóa phiên bản | CRUD và quy trình duyệt; màn sửa vùng là phần tùy biến |
| Database | MySQL | Metadata, phiên bản tranh, giao dịch, kiểm duyệt |
| Queue | Redis + Laravel workers | Gọi AI, xử lý ảnh, xuất gói; không chặn API |
| Xử lý ảnh | Python + OpenCV, chạy worker giới hạn tài nguyên | Làm sạch nét, phân vùng, sinh dữ liệu renderer |
| File | Một dịch vụ object storage tương thích S3 + CDN | Chứa bản gốc, gói tranh, ảnh xem trước |
| Local app | SQLite + thư mục file riêng | Tiến trình, catalog cache, trạng thái tải |

Không cần API Python công khai ở MVP. Laravel có thể giao công việc cho tiến trình xử lý nội bộ qua đầu vào JSON và file xác định; không ghép prompt thành lệnh shell. Chỉ tách thành service độc lập khi khối lượng hoặc nhu cầu triển khai đòi hỏi.

Queue nền và các cơ chế retry/timeout của Laravel là cơ sở cho lựa chọn điều phối này. [Tài liệu Laravel Queues](https://laravel.com/docs/queues)

### 4.2 Ranh giới giao tiếp

- App → Laravel: metadata, phiên bản catalog, yêu cầu tải, giao dịch.
- App → CDN: tải thumbnail và gói nội dung đã được cấp quyền.
- Admin → Laravel: tạo nháp, yêu cầu AI, sửa/duyệt, xuất bản.
- Worker → nhà cung cấp AI: tác vụ do admin khởi tạo; chỉ dùng prompt nội dung, không có dữ liệu trẻ em.
- Worker → storage: ghi tài sản mới vào vùng nháp; chưa công khai.
- Backend → Google Play: xác minh giao dịch và cập nhật quyền.

Không truyền từng lần chạm tô về server. App đã tải tranh phải tô được khi backend ngừng hoạt động.

### 4.3 Tổ chức mã nguồn dự kiến

```text
app/                         # Flutter client cho Android trước, iOS sau
├── lib/core/                 # config, routing, API client, local database
├── lib/features/             # onboarding, library, editor, my_art, parent, paywall
└── test/                     # widget, renderer, offline và contract tests

backend/                     # Laravel API + admin + workers
├── app/Modules/              # Catalog, AIContent, Review, Publishing, Progress, Billing
├── app/Jobs/                 # queue jobs, retry và idempotency
├── app/Services/             # AI provider, image pipeline, storage, Play Billing
├── image-worker/             # Python/OpenCV xử lý outline, region-map và manifest
├── database/migrations/      # schema database
├── routes/                   # public API và admin API tách vùng xác thực
└── tests/                    # API, pipeline, publish, billing và security

contracts/                   # OpenAPI, manifest schema, fixtures dùng chung
docs/                        # quyết định kiến trúc, vận hành, release checklist
```

Không tạo hoặc triển khai các thư mục trên trong giai đoạn lập plan. Đây là layout mục tiêu để tránh nhầm `mockup/` với app production.

Khóa phiên bản SDK/framework/package sau thử nghiệm ban đầu. Chưa chọn phiên bản cụ thể trong kế hoạch để tránh giả định sai tương thích Filament, Flutter, Billing và Android SDK.

## 5. Thử nghiệm kỹ thuật bắt buộc trước khi xây đầy đủ

Mục tiêu: chứng minh ảnh AI có thể chuyển thành tranh tô ổn định với chi phí biên tập chấp nhận được.

1. Tạo 20 ảnh thử ở hai mức độ chi tiết với một hoặc hai nhà cung cấp AI ứng viên. Ghi nhận và xử lý toàn bộ 20 ảnh, kể cả ảnh loại; tính tỷ lệ đạt trên toàn lô.
2. Chọn 5 ảnh điển hình để kiểm tra renderer sâu: hình đơn giản, nét hở, vùng nhỏ, vùng lồng nhau và hình nhiều chi tiết.
3. Làm pipeline phân vùng và màn Flutter tối thiểu.
4. Tô toàn bộ, zoom, hoàn tác, đóng/mở app trên điện thoại thật.
5. Đo tỷ lệ dùng được, thời gian sửa mỗi ảnh, RAM, tốc độ phản hồi.
6. Chốt định dạng dữ liệu và nhà cung cấp dựa trên kết quả, không chỉ ảnh đẹp.

**Cổng quyết định:** nếu phần lớn ảnh phải vẽ lại hoặc renderer không đạt mục tiêu, dừng mở rộng admin/AI hàng loạt. Chuyển MVP sang tranh line-art/SVG được chuẩn hóa thủ công, vẫn nhập qua backend; tiếp tục cải thiện AI riêng.

Mục tiêu thử nghiệm nội bộ ban đầu: ít nhất 70% toàn bộ lô thử có thể dùng sau tối đa 10 phút chỉnh mỗi ảnh; báo cáo riêng từng mức. Thời gian gồm sửa nét, kiểm tra vùng, gán màu và duyệt. Đây là ngưỡng ra quyết định, không phải tỷ lệ AI đã chứng minh. Khi đạt, thử thêm một lô mới để tránh kết luận từ lô may mắn.

Người vận hành nội dung là chủ dự án ở giai đoạn đầu. Sửa nét bằng công cụ ảnh ngoài admin được chọn sau POC; nhập lại bản outline để worker tái sinh vùng, màu mẫu và gói. Không sửa riêng RLE hoặc map sau khi đóng gói.

## 6. Chuẩn dữ liệu tranh

### 6.1 Gói nội dung v1

| File | Vai trò | Ràng buộc |
|---|---|---|
| `manifest.json` | ID, phiên bản, kích thước, vùng, màu mẫu, checksum | Schema có phiên bản; không chứa code thực thi |
| `outline.png` | Lớp nét đen trên nền trong suốt | Lossless; không dùng nền trắng đục che màu bên dưới |
| `region-map.png` | ID vùng cho từng pixel | PNG lossless; không resize/nén mất dữ liệu/đổi màu qua CDN |
| `regions.rle` | Các dải pixel theo vùng để tô nhanh | Tạo từ cùng map; kiểm tra giới hạn tọa độ |
| `preview.webp` | Tranh mẫu đã tô | Render từ cùng vùng và màu mẫu đã duyệt |
| `thumbnail.webp` | Ảnh xem trước nhỏ | Chỉ để hiển thị, không dùng xác định vùng |

ZIP là phương án đóng gói đề xuất. Trước giải nén phải giới hạn tổng dung lượng, số file, kích thước ảnh và từ chối đường dẫn vượt thư mục, file lồng bất thường hoặc bom giải nén.

### 6.2 Quy tắc vùng tô

- `region_id = 0`: không tô, ví dụ nét viền hoặc vùng loại trừ.
- ID còn lại ánh xạ duy nhất tới vùng; có thể mã hóa vào RGB 24-bit theo công thức cố định.
- Backend và app phải giải mã nhất quán; dùng dữ liệu pixel gốc, không đọc lại ảnh hiển thị đã lọc màu.
- Không nội suy ID khi zoom. Tọa độ chạm phải chuyển ngược qua pan/zoom về pixel nguồn.
- Mỗi vùng có ID, bounding box, diện tích, điểm gợi ý nằm thực sự trong vùng, màu mẫu và cờ tính tiến độ.
- Không mặc định centroid nằm trong vùng lõm; xác định điểm gợi ý bằng phép kiểm tra trong vùng.
- Màu mẫu là một giá trị cho mỗi vùng, có thể lặp giữa nhiều vùng.
- Bất kỳ thay đổi hình học hoặc ID vùng nào phải tăng `asset_version`.
- Không đổi ID của phiên bản đã phát hành. Tiến trình dùng khóa `(picture_id, asset_version, mode)`.

### 6.3 Hợp đồng dữ liệu cần khóa sau POC

- Bản đồ ID là nguồn chuẩn; RLE và preview đều sinh tự động, không sửa độc lập.
- ID RGB giải mã theo `id = R + (G << 8) + (B << 16)`, alpha luôn 255; không cho quản lý màu thay đổi ID.
- Đề xuất RLE v1: magic `KCR1`, width/height/regionCount dạng uint32 little-endian; mỗi vùng gồm ID, số span, rồi các bộ `(y, xStart, length)` uint32. Span sắp theo y/x, nằm trong ảnh, không chồng; tổng vùng/byte giới hạn theo manifest.
- Kiểm tra round-trip: giải mã RLE khớp toàn bộ bản đồ ID, trừ ID 0 vốn không tô. Decoder từ chối file cụt, số đếm vô lý, byte thừa hoặc tràn số.
- Định nghĩa vùng nền được tô/không tô trong manifest; vùng có lỗ không được tô xuyên.
- Fixture riêng cho mép anti-alias, vùng sát nhau và đường viền bán trong suốt. Phần màu có thể mở rộng có kiểm soát dưới lớp nét để tránh viền trắng, không thay ID tương tác của vùng khác.
- `schema_version` mô tả định dạng; `asset_version` mô tả một bản tranh. App kiểm tra khả năng đọc trước tải.
- Đây là đề xuất định dạng phải được POC xác nhận trước khi `backend/` và `app/` triển khai độc lập.

### 6.4 Thiết kế renderer

Lookup ID chỉ giải quyết việc “chạm vùng nào”, chưa giải quyết tốc độ hiển thị. Renderer cần cơ chế đổi màu một vùng mà không quét lại toàn bộ ảnh mỗi frame.

Phương án POC: canvas màu được cập nhật bằng span/RLE của vùng vừa đổi; lớp outline phủ phía trên. Công việc nặng chạy ngoài luồng UI nếu cần. Giữ bộ nhớ undo dưới dạng thay đổi ID/màu, không chụp toàn bộ bitmap mỗi lần chạm. Đo chi phí đưa ảnh cập nhật lên GPU; chỉ chuyển sang shader/texture lookup nếu POC cho thấy cần.

Mục tiêu thử nghiệm: ảnh nguồn 1024 × 1024; tối đa khoảng 40 vùng trong nội dung trẻ em bản đầu (khác ngưỡng kỹ thuật decoder). Một buffer RGBA kích thước này chiếm khoảng 4 MiB trước overhead, vì vậy không tải hàng chục tranh full-size cùng lúc.

Ảnh lớn hoặc vượt số vùng phải bị chặn ở bước xuất gói, không chờ app bị lỗi. Ngưỡng chính thức chốt theo thử nghiệm máy RAM 3–4 GB.

## 7. Pipeline AI tạo và xuất bản tranh

### 7.1 Đầu vào admin

- Chủ đề, chủ thể, độ khó, phong cách nét, bố cục, ngôn ngữ metadata.
- Số biến thể; hạn mức tiền và số lần thử.
- Prompt template có phiên bản và danh sách loại nội dung không nhận.
- Mặc định không yêu cầu tên thương hiệu hoặc nhân vật có bản quyền.

Nhà cung cấp AI chưa khóa. Tiêu chí chọn: quyền sử dụng đầu ra thương mại theo hợp đồng, chất lượng line-art, chi phí trên tranh được duyệt, độ ổn định API, chính sách lưu dữ liệu và khả năng truy vấn lại trạng thái job. Không mặc định ảnh AI luôn không có rủi ro sở hữu trí tuệ.

### 7.2 Các bước xử lý

1. Tạo `generation_job` và giữ trước hạn mức chi phí.
2. Worker gửi yêu cầu AI; lưu provider, model, prompt version, request ID và chi phí thực nếu có.
3. Tải ảnh về vùng nháp; kiểm tra MIME thực, kích thước, nội dung và trùng lặp.
4. Chuẩn hóa grayscale/threshold, giảm nhiễu có kiểm soát; lưu riêng bản gốc.
5. Xác định nét viền và vùng kín. Thử nối khe hở nhỏ; đánh dấu vùng đáng ngờ thay vì tự nối mạnh tay.
6. Phân vùng bằng connected components trên vùng trống; tính số vùng, diện tích, vùng chạm biên, điểm gợi ý.
7. Flag vùng quá nhỏ, nét hở làm hai bộ phận nhập làm một, chi tiết méo hoặc trùng hình.
8. Chủ dự án/biên tập viên sửa outline bằng công cụ ngoài đã chọn, nhập lại, chạy lại phân vùng; lưu chi phí công sửa. V1 không xây editor phức tạp trong admin.
9. Gán bảng màu mẫu theo vùng. Có thể nhờ AI gợi ý màu nhưng phải validate ID và định dạng.
10. Render `preview.webp` từ dữ liệu thật; không tạo riêng một ảnh màu AI khác bố cục rồi gọi đó là mẫu.
11. Chạy kiểm thử gói, tô thử trên trình xem admin, duyệt nội dung.
12. Ghi asset version bất biến, xác minh đủ file trên storage rồi cập nhật con trỏ published trong transaction DB. Storage và DB không cùng một transaction; khi DB lỗi, giữ file chưa tham chiếu để dọn sau. Job publish retry phải idempotent. Rollback đổi con trỏ về bản đã xác minh, không sửa file cũ.

Connected components và các hàm đo hình học trong OpenCV hỗ trợ phần phân vùng. Chúng không tự hiểu bộ phận vật thể và không bảo đảm mọi ảnh AI có vùng kín đúng nghĩa. [Tài liệu OpenCV](https://docs.opencv.org/4.x/d3/dc0/group__imgproc__shape.html)

### 7.3 Trạng thái

`draft → queued → generating → processing → needs_review → approved → published`

Nhánh khác: `failed`, `rejected`, `archived`. Mỗi chuyển trạng thái có người/job thực hiện, thời gian, lý do và phiên bản liên quan. Sửa tài sản sau khi duyệt làm mất trạng thái approved và cần duyệt lại. AI/worker không có quyền publish.

### 7.4 Chống lỗi và vượt chi phí

- Giới hạn tổng tiền/ngày, tiền/tháng, số job đồng thời và số biến thể mỗi yêu cầu.
- Unique job/idempotency key để bấm hai lần không sinh hai lô.
- Timeout không được tự hiểu là AI chưa tính tiền; kiểm tra request ID/status trước khi tạo lại nếu provider hỗ trợ.
- Retry lỗi tạm thời với backoff có giới hạn. Lỗi nội dung/chất lượng chuyển review, không lặp vô hạn.
- Có nút dừng tạo mới; không làm hỏng request đã gửi hoặc tác vụ đang hoàn tất.
- Lưu tỷ lệ loại và lý do; tính chi phí trên ảnh được xuất bản, không chỉ trên ảnh AI trả về.

## 8. Trang quản trị

| Module | Chức năng | Điều kiện hoàn thành |
|---|---|---|
| Dashboard | Hàng chờ, job lỗi, chi phí, số tranh duyệt | Không phải tải log thủ công để biết job đang ở đâu |
| Chủ đề/bộ tranh | Tên, bản dịch, thứ tự, miễn phí/trả phí | Sửa metadata không cần build lại app |
| Tranh | Bản gốc, version, độ khó, vùng, preview | Xem được lịch sử và phiên bản đang phát hành |
| AI Studio | Form cấu hình, template, số lượng, hạn mức | Gửi job và theo dõi trạng thái bất đồng bộ |
| Review | Overlay vùng, bấm thử, gán màu, ghi lỗi, duyệt | Người duyệt tô thử từ đúng gói sẽ gửi app |
| Publish | Xuất bản, gỡ khỏi catalog, rollback | Không phát hành asset thiếu hoặc chưa duyệt |
| Commerce | Product ID, bộ tranh tương ứng, trạng thái giao dịch | Không sửa quyền từ một cờ gửi lên bởi app |
| Vận hành | Retry có giới hạn, audit, cấu hình an toàn | Phân quyền, xác nhận thao tác nhạy cảm |

MVP không xây một trình Illustrator trong admin. Đợt đầu chỉ cần overlay vùng, đổi màu mẫu, loại vùng, nhập bản sửa và tô thử. Công cụ vẽ nối nét, gộp/tách vùng trực tiếp làm sau nếu số liệu chứng minh tiết kiệm công.

## 9. Dữ liệu và API

### 9.1 Bảng dữ liệu cốt lõi

| Bảng | Dữ liệu chính |
|---|---|
| `categories` | slug, tên theo ngôn ngữ, thứ tự, trạng thái |
| `packs`, `pack_pictures` | Bộ tranh, danh sách tranh, thứ tự |
| `pictures` | ID ổn định, chủ đề, độ khó, bản đang xuất bản |
| `picture_versions` | version, manifest path, hash, kích thước, số vùng, trạng thái |
| `generation_jobs` | Provider/model, prompt version, request ID, trạng thái, cost |
| `moderation_reviews` | Version, người duyệt, kết quả, lý do |
| `catalog_releases` | Release ID, nội dung công bố, version yêu cầu tối thiểu |
| `store_products` | Store/product ID và quyền mở bộ tranh |
| `purchases` | Token bảo vệ phù hợp, trạng thái xác minh/acknowledge/hoàn tiền |
| `entitlements` | Quyền truy cập đã xác minh và căn cứ cấp quyền |
| `subscription_plans` | Product ID, chu kỳ tháng, giá hiển thị, trạng thái |
| `ad_configs` | Placement, enabled, audience, max content rating, thời điểm áp dụng |
| `audit_logs` | Hành động quản trị, đối tượng, thời gian |

Dữ liệu hình học vùng để trong gói version, chưa cần mỗi pixel hoặc mỗi span là một hàng SQL. Không tạo bảng người chơi/thiết bị chỉ để đếm lượt tô.

### 9.2 API đề xuất

| Endpoint | Mục đích | Quyền |
|---|---|---|
| `GET /api/v1/bootstrap` | Config an toàn, catalog version, min schema | Public, rate limit |
| `GET /api/v1/categories` | Chủ đề và bản dịch | Public, cache |
| `GET /api/v1/pictures` | Danh sách có phân trang và filter | Public, chỉ published |
| `GET /api/v1/pictures/{id}` | Metadata và version tương thích | Public |
| `POST /api/v1/pictures/{id}/download` | Cấp thông tin tải; kiểm tra quyền với Premium | Theo quyền nội dung |
| `POST /api/v1/purchases/verify` | Xác minh giao dịch cửa hàng | Chống lạm dụng, idempotent |
| `POST /api/v1/purchases/restore` | Khôi phục quyền từ giao dịch đang sở hữu | Xác minh server |
| `POST /api/v1/store/notifications` | Nhận thông báo giao dịch | Xác thực nguồn server |

API admin/worker nằm trong vùng xác thực riêng:

| Endpoint | Mục đích | Quyền |
|---|---|---|
| `POST /api/v1/admin/generation-jobs` | Tạo lô AI bất đồng bộ | Admin/editor |
| `GET /api/v1/admin/generation-jobs` | Theo dõi job và chi phí | Admin/editor |
| `GET /api/v1/admin/pictures` | Xem bản nháp, review và version | Admin/editor/reviewer |
| `POST /api/v1/admin/content-reviews` | Ghi kết quả duyệt/reject | Reviewer |
| `POST /api/v1/admin/publications` | Publish hoặc rollback version đã duyệt | Publisher |

API app chỉ query `PUBLISHED` ở server-side. Không có endpoint public gọi AI, đọc prompt, đọc moderation result hoặc xem asset nháp.

Quy ước: ID ổn định; ngày UTC; mã lỗi có cấu trúc; phân trang cursor; giới hạn page size; ETag cho catalog; giới hạn body. Remote config chỉ điều khiển nội dung/tính năng đã được kiểm thử, không tải code hoặc bật quảng cáo chưa được khai báo.

## 10. Offline, cập nhật và quyền nội dung

- App mở bằng catalog local, sau đó cập nhật nền; server lỗi không được chặn toàn bộ màn hình.
- Tải vào thư mục tạm; xác minh hash và schema rồi đổi sang thư mục version hoàn chỉnh.
- Checksum giúp phát hiện hỏng dữ liệu, không thay thế HTTPS hoặc xác thực quyền tải.
- Tự lưu thay đổi màu theo transaction; mục tiêu không mất thao tác đã báo lưu.
- Cache có giới hạn; chỉ dọn file không còn tham chiếu bởi tranh đang tô/được ghim.
- Không ghi đè asset version cũ. Giữ bản cũ cho tiến trình cũ và có kế hoạch retention.
- Gỡ tranh khỏi catalog ngăn tải mới. Không thể bảo đảm thu hồi tức thì nội dung đã tải trên thiết bị offline.
- Nội dung có rủi ro an toàn: app kiểm tra danh sách thu hồi khi kết nối lại và xử lý bản tải theo chính sách đã công bố.
- Quyền mua được xác minh online và cache bằng dữ liệu cấp quyền có chữ ký; mua mới/khôi phục cần mạng.
- Bản đầu đề xuất không cắt ngang tranh đã tải chỉ vì backend mất kết nối. Khi nối lại mới đối soát hoàn tiền/thu hồi; bản tô cá nhân vẫn được bảo toàn.
- File trả phí phải tới thiết bị để chơi offline, do đó không thể chống trích xuất tuyệt đối. Không hứa DRM hoàn hảo.

## 11. Kiếm tiền và xác minh mua hàng

### 11.1 Giả thuyết thương mại và thứ tự kiểm chứng

1. APK thử nghiệm: Free, không quảng cáo, không thanh toán.
2. Google Play closed test: tạo một subscription Premium theo tháng, dùng giao dịch test và nhóm tester là phụ huynh.
3. Soft launch: Free có catalog giới hạn và tối đa một vị trí quảng cáo ở Library; Premium mở toàn bộ pack, tranh mới và remove ads.
4. Đo tỷ lệ bắt đầu trial/mua, hủy, restore, refund, chi phí nội dung và phản hồi phụ huynh trước khi mở rộng.
5. Nếu người dùng không cảm nhận được giá trị cập nhật hàng tháng, chuyển sang gói mua một lần/lifetime hoặc bán từng picture pack.

Không dùng vé ngày, xu, mở tranh 24 giờ hoặc bắt trẻ xem quảng cáo để tiếp tục tô. Mục tiêu là tạo giá trị cho phụ huynh, không tối đa hóa số lần bấm quảng cáo.

### 11.2 Luồng mua

App mở Google Play Billing → nhận purchase token → backend xác minh package/product/user và trạng thái subscription → cấp entitlement idempotent → acknowledge → app tải bộ tranh Premium.

Không cấp quyền khi `PENDING`; xử lý callback lặp, lỗi mạng, hủy, hết hạn, grace period, pause, refund và restore. Purchase token cần chống xử lý trùng và không xuất hiện trong log thông thường. Hướng xử lý server dựa trên hướng dẫn bảo vệ giao dịch của Google. [Google Play Billing security](https://developer.android.com/google/play/billing/security)

Trạng thái entitlement tối thiểu: `ACTIVE`, `GRACE_PERIOD`, `PAUSED`, `EXPIRED`, `REVOKED`. App có thể dùng quyền đã cache trong thời gian ngắn khi offline; lần xác minh lại phải do backend quyết định.

Không có tài khoản app vẫn cần thiết kế restore: ứng dụng truy vấn giao dịch qua tài khoản Google Play trên máy và gửi bằng chứng tới backend. Mã cài đặt local không phải căn cứ sở hữu. Quyền mua và tiến trình là hai thứ riêng: khôi phục quyền không khôi phục tranh đã tô nếu dữ liệu local bị xóa. Bảo vệ chống replay, token bị đánh cắp và cách ánh xạ quyền phải được kiểm thử trước thu tiền thật; cân nhắc tài khoản phụ huynh nếu cần đồng bộ/kiểm soát sở hữu mạnh hơn.

### 11.3 Thiết kế quyền tải trước khi tích hợp commerce

Không coi token hợp lệ là đủ chứng minh người gửi hiện tại sở hữu nó. Trước thu tiền thật phải có quyết định kiến trúc về chủ thể sở hữu và thông tin xác thực khi tải.

Đề xuất triển khai: trẻ dùng app không đăng nhập; chỉ phụ huynh tạo/đăng nhập tài khoản tối giản khi mua hoặc khôi phục nội dung server. Backend gắn giao dịch đã xác minh với tài khoản phụ huynh, phát access token ngắn hạn và refresh token có thể thu hồi. Đây là thay đổi có điều kiện cần chủ dự án chọn trước giai đoạn commerce, không bắt buộc ở APK thử.

- App không được lấy danh tính người dùng từ body để cấp quyền; lấy từ phiên đã xác thực.
- Verify kiểm tra store, package, product allowlist, trạng thái và ánh xạ tài khoản nếu có; một giao dịch cấp đúng một quyền.
- Token đã xử lý gửi lại từ cùng chủ thể trả về kết quả idempotent, không báo lỗi mua trùng hoặc cấp thêm quyền.
- Khôi phục dùng cùng tài khoản phụ huynh và giao dịch Google Play; token đã gắn tài khoản khác chuyển luồng hỗ trợ, không tự chuyển quyền.
- Endpoint download kiểm tra session, entitlement và version; phát URL ngắn hạn. URL là quyền tạm thời, không phải danh tính dài hạn.
- Logout thu hồi phiên server; quy tắc quyền offline và tranh cá nhân tách riêng. Chính sách thiết bị dùng chung phải được thử.
- Thêm password reset, xóa tài khoản và dữ liệu nếu chọn tài khoản phụ huynh; không dùng tài khoản này để lập hồ sơ trẻ.
- Nếu chủ dự án muốn mua hoàn toàn không tài khoản, thay bằng thiết kế receipt/capability riêng sau đánh giá replay/restore; chưa xem phương án đó đã giải quyết chỉ vì Google trả PURCHASED.

Điều kiện bắt đầu commerce: chọn một trong hai mô hình danh tính, cập nhật schema/API và ngân sách; xác nhận thử nghiệm sẵn sàng trả tiền. Không triển khai cả hai cùng lúc.

### 11.4 Quảng cáo là hạng mục có điều kiện

Đề xuất sản phẩm: Free có tối đa một banner/native placement ở Library, Premium không có quảng cáo; tuyệt đối không quảng cáo trong màn tô, canvas, popup hoàn thành hoặc ngay lúc mở app. Chưa bật quảng cáo trong APK thử và chỉ pilot sau khi có ma trận nhóm tuổi/quốc gia/SDK, cấu hình phục vụ trẻ em, privacy copy và cơ chế tắt từ xa. Khu vực phụ huynh hoặc việc chọn “tranh chi tiết” không tự làm quảng cáo trở nên hợp lệ.

Google Play yêu cầu app có trẻ em trong đối tượng tuân thủ Families; quảng cáo cho trẻ hoặc người chưa rõ tuổi phải dùng phiên bản SDK tự chứng nhận, không cá nhân hóa. Quảng cáo gây gián đoạn, kể cả rewarded, phải đóng được sau 5 giây. Không được đặt quảng cáo gây bấm nhầm. [Google Play Families](https://support.google.com/googleplay/android-developer/answer/9893335?hl=en)

## 12. Quyền riêng tư, bảo mật và kiểm duyệt

### 12.1 Lựa chọn thiết kế bảo vệ dữ liệu

- Không camera, microphone, GPS, upload ảnh cá nhân hoặc chat ở MVP.
- Không gửi nét tô, bản vẽ hoặc lịch sử lựa chọn chi tiết về server theo mặc định.
- Không gắn analytics/ads SDK ở bản thử kỹ thuật.
- Log server tối giản; che token, loại dữ liệu không cần, giới hạn lưu log vận hành mặc định 14 ngày nếu không có yêu cầu khác.
- Dữ liệu thanh toán có lịch lưu riêng phục vụ khôi phục và nghĩa vụ liên quan; không áp dụng máy móc 14 ngày.
- IP, mã thiết bị và mã cài đặt có thể liên quan nhận diện; không gọi là vô danh chỉ vì không có tên.
- Có chính sách riêng tư đúng dữ liệu thực tế, đầu mối hỗ trợ và luồng xử lý yêu cầu dữ liệu của phụ huynh.
- Phạm vi quốc gia và yêu cầu về sự đồng ý cần được rà soát trước phát hành; tài liệu này không thay thế tư vấn pháp lý.

Đối tượng sản phẩm là trẻ em; khai báo nhóm tuổi đúng với nội dung và kiểm thử. Không chọn danh mục thông thường hoặc thêm chữ “gia đình” để tránh yêu cầu dành cho trẻ em. [Hướng dẫn khai báo đối tượng Google Play](https://support.google.com/googleplay/android-developer/answer/9867159?hl=en)

### 12.2 Bảo mật hệ thống

- API key AI và khóa ký URL chỉ nằm ở backend/secret store; không có trong APK.
- Admin có MFA, role editor/reviewer/publisher và audit. Một người có thể giữ nhiều vai trò lúc đầu nhưng quyền hệ thống vẫn tách được.
- HTTPS; bucket nháp private; file Premium không dùng URL công khai cố định.
- Signed URL chỉ cấp sau kiểm tra quyền; TTL ngắn, đường dẫn version bất biến.
- Worker bị giới hạn CPU/RAM/thời gian; không xử lý file tùy ý trên máy chủ chính.
- Nếu tải ảnh từ URL AI trả về, giới hạn host được phép và chặn truy cập mạng nội bộ để giảm SSRF.
- Validate upload, JSON, ảnh và archive; không thực thi script từ SVG hoặc manifest.
- Chống lạm dụng public API bằng quota/rate limit; không dựa vào API key chung nhúng trong app như bí mật.
- Backup DB hằng ngày; bảo vệ/versioning tài sản; thử phục hồi trước ra mắt.

### 12.3 Checklist duyệt mỗi tranh

- [ ] Phù hợp đối tượng, không nội dung nhạy cảm/kinh dị hoặc biểu tượng ngoài ý muốn.
- [ ] Không nhân vật/thương hiệu được sử dụng khi chưa có quyền phù hợp.
- [ ] Hình thể không méo rõ rệt; không chữ rác hoặc watermark.
- [ ] Các vùng tô đúng cấu trúc, không tràn giữa bộ phận.
- [ ] Vùng nhỏ có thể thao tác trên điện thoại sau zoom.
- [ ] Mẫu màu khớp chính xác gói vùng tô.
- [ ] Toàn bộ vùng bắt buộc đều tô được, hoàn tất đạt 100%.
- [ ] Chế độ tô tự do, bật/tắt mẫu, undo/redo và save/load hoạt động.
- [ ] Có ghi nguồn, job/model/prompt version và người duyệt.

## 13. Kiểm thử và tiêu chí nghiệm thu

Các ngưỡng dưới đây là mục tiêu dự án; phải đo trên máy thật, không khẳng định hiệu năng trước khi làm POC.

| Nhóm | Bài kiểm thử | Điều kiện đạt |
|---|---|---|
| Renderer | Chạm hàng trăm vùng ở các mức zoom | Không đổi sai vùng, không tràn màu |
| Phản hồi | Tô trên máy RAM 3–4 GB đã chọn | Phản hồi nhìn thấy p95 dưới 100 ms với tranh giới hạn MVP |
| Khung hình | Pan/zoom liên tục | Mục tiêu gần 60 fps, điều tra mọi khựng dài |
| Local | Tô, background, force-stop, mở lại | Khôi phục thao tác đã xác nhận lưu |
| Undo/redo | 100 thao tác trộn màu và xóa | Đúng thứ tự, bộ nhớ có giới hạn |
| Offline | Airplane mode từ lúc khởi động | 6 tranh cài sẵn và tranh đã tải vẫn dùng được |
| Nội dung | File sai hash/schema/zip bất thường | Từ chối an toàn; không crash |
| Version | Xuất bản bản mới khi đang tô bản cũ | Tiến trình không bị remap sai vùng |
| Queue | Retry, timeout, bấm tạo hai lần | Không tự xuất bản hoặc gọi lại không kiểm soát |
| Commerce | Pending, hủy, mua lại, callback lặp, refund | Không cấp sai/trùng quyền; restore hoạt động |
| Security | Truy cập asset Premium khi chưa mua | Không được cấp URL tải hợp lệ |
| Privacy | Kiểm tra lưu lượng mạng và SDK manifest | Không gửi dữ liệu ngoài danh mục đã khai báo |
| Nội dung | Duyệt tranh theo mốc | Alpha 10/10; thương mại 30/30 nếu giữ mục tiêu; không giữ tranh lỗi để đủ số |

Thử với khoảng 5 cặp phụ huynh-con mỗi nhóm 4–5 và 6–8 nếu tuyển được. Ghi kết quả từng nhóm; mục tiêu ban đầu 4/5 trẻ mỗi nhóm tự chọn hình, màu và tô được sau hướng dẫn bằng hình ngắn. Mẫu nhỏ chỉ để phát hiện vấn đề, không chứng minh thị trường. Phụ huynh đồng ý và giám sát; không ghi hình hoặc thu dữ liệu không cần thiết.

## 14. Lộ trình và ước lượng công sức

Đơn vị: ngày làm việc tập trung của một dev, không bao gồm thời gian chờ duyệt cửa hàng. Có thể xếp việc chuẩn bị nội dung xen kẽ nhưng không giả định có thêm nhân sự miễn phí.

| Giai đoạn | Công việc | Ước lượng | Đầu ra/cổng nghiệm thu |
|---|---|---:|---|
| 0. Chốt yêu cầu | PRD gọn, nhóm thử, quy tắc monetization | 2–3 ngày | Chốt P0 và dữ liệu không thu |
| 1. POC kỹ thuật | Đánh giá đủ 20 ảnh, 5 fixture sâu, Flutter demo | 5–8 ngày | Tỷ lệ đạt toàn lô; quyết định định dạng |
| 2. Nền tảng | Repo, Laravel/DB, storage, auth admin, API contract | 4–6 ngày | Tải một gói version qua API |
| 3. App cốt lõi | 10 tranh, catalog, renderer một chế độ, save/cache, lỗi mạng | 10–15 ngày | APK alpha offline/online và thử trẻ |
| 4. Nội dung AI | Queue, hạn mức, review, publish, rollback | 7–10 ngày | Admin tạo tới xuất bản mà không sửa DB tay |
| 5. Thương mại | Quyết định danh tính, một sản phẩm, verify/restore | 4–6 ngày cơ sở | Tính lại nếu thêm tài khoản phụ huynh; chưa gồm ads |
| 6. QA và phát hành | 30 tranh, UI polish, privacy, store assets, test máy | 6–10 ngày | AAB release candidate và checklist đạt |
| 7. Sau ra mắt | Sửa lỗi, đo phản hồi, bổ sung nội dung có chọn lọc | Liên tục | Quyết định tiếp tục đầu tư bằng dữ liệu |

Tổng phát triển và QA cơ sở: 38–58 ngày làm việc; chưa gồm phát triển tài khoản phụ huynh nếu chọn, công cụ sửa ảnh tùy biến hoặc thêm quốc gia/ngôn ngữ. Phải ước lượng lại commerce sau quyết định danh tính. Cộng đệm 20–30% cho xử lý ảnh, học Flutter và tích hợp thanh toán: khoảng 46–75 ngày, tương đương khoảng 10–15 tuần full-time. Làm 15 giờ/tuần có thể kéo dài khoảng 6–10 tháng. Đây là dự toán lập kế hoạch, chưa phải báo giá cố định.

Nếu cần APK thử sớm, mốc đầu là hết giai đoạn 1: chỉ vài tranh và thao tác tô, chưa phải MVP thương mại đầy đủ. Muốn giảm thời gian ra mắt thì giữ quy trình sửa ảnh ngoài admin, chưa làm ads và chưa tự động hóa mọi thao tác biên tập.

## 15. Google Play và kế hoạch phát hành

- Xác định tài khoản developer, danh tính, quyền truy cập và người giữ khóa ký ngay từ đầu.
- Dùng APK cho cài thử; tạo AAB release có ký cho luồng Google Play. [Flutter Android release](https://docs.flutter.dev/deployment/android)
- Khóa min SDK theo thiết bị mục tiêu; target SDK theo yêu cầu Play tại lúc nộp, không nhầm hai khái niệm. [Yêu cầu target API](https://developer.android.com/google/play/requirements/target-sdk)
- Kiểm tra yêu cầu tương thích native libraries, kiến trúc CPU, page size và Billing version tại mốc release; kiểm tra từng plugin thực tế.
- Hoàn thiện tên, icon, ảnh chụp thật, mô tả, danh mục, độ tuổi, Data safety, privacy URL và thông tin hỗ trợ.
- Không ghi “offline hoàn toàn” nếu tải bộ mới/mua/restore cần Internet; mô tả chính xác “tranh đã tải chơi offline”.
- Không đưa tranh đẹp không có trong app vào screenshot; ghi rõ nội dung nào trả phí.
- Internal test → closed test nếu áp dụng → xin production access → phát hành giới hạn → tăng dần sau theo dõi lỗi.

Tài khoản cá nhân tạo sau 13/11/2023 hiện cần closed test với ít nhất 12 người tham gia liên tục 14 ngày trước khi xin quyền production. Đạt thời gian không có nghĩa tự động được duyệt. Lên lịch tuyển tester và dự phòng review ngoài công sức phát triển. [Google Play testing requirements](https://support.google.com/googleplay/android-developer/answer/14151465?hl=en)

Các chính sách được kiểm tra khi lập tài liệu ngày 09/09/2026; đối chiếu lại trong Play Console trước nộp. Kế hoạch không cam kết được cửa hàng chấp thuận.

## 16. Chi phí và bài toán kinh doanh

### 16.1 Cấu phần cần dự trù

| Khoản | Cách tính | Biện pháp kiểm soát |
|---|---|---|
| API/backend/worker | Máy chủ + DB + Redis + backup | Bắt đầu một cụm nhỏ, tách worker khi cần |
| Storage/CDN | GB lưu, lượt request, dữ liệu tải theo gói dịch vụ | Thumbnail nhỏ, tải theo yêu cầu, cache |
| AI | Chi phí mọi lần gọi, kể cả ảnh bị loại | Budget cứng, batch nhỏ, template đã thử |
| Biên tập | Phút sửa/duyệt × số tranh | Theo dõi lý do loại, đơn giản hóa phong cách |
| Cửa hàng | Tài khoản, phí dịch vụ/thuế theo điều kiện thực | Kiểm tra mức áp dụng trước định giá |
| Marketing | Nội dung giới thiệu, thử quảng cáo thu hút người dùng | Giới hạn ngân sách thử, không mua lượt cài/đánh giá giả |
| Hỗ trợ | Lỗi mua, restore, thiết bị, yêu cầu dữ liệu | FAQ, mã lỗi dễ tra, không log bí mật |

Chưa chọn nhà cung cấp AI/hosting nên không gán giá thị trường cụ thể. Có thể đặt phong bì ngân sách thử nội bộ 1–3 triệu đồng/tháng cho hạ tầng và AI, chưa gồm công dev, biên tập, quảng bá, phí cửa hàng và thuế; đây là mức trần đề xuất, không khẳng định đủ cho mọi lưu lượng.

### 16.2 Công thức nên theo dõi

- Chi phí nội dung trên tranh dùng được = (toàn bộ AI + xử lý + công sửa/duyệt) / số tranh xuất bản.
- Băng thông dự kiến = số lượt tải mới × dung lượng gói trung bình; cache trên thiết bị làm giảm tải lặp.
- Doanh thu mua bộ = số người mua hợp lệ × giá bán thực tế; phân biệt doanh thu gộp với tiền thực nhận.
- Lãi đóng góp = tiền thực nhận − hạ tầng − AI − nội dung − chi phí thu hút người dùng − chi phí hỗ trợ biến đổi.
- Điểm hòa vốn số đơn = chi phí cố định / phần đóng góp ròng mỗi đơn, chỉ có ý nghĩa nếu phần đóng góp dương.

Ví dụ giả định, không phải dự báo: 300 lượt tạo giá 1.000 đồng/lượt, duyệt được 120 tranh ⇒ riêng AI 2.500 đồng/tranh. Nếu biên tập 5 phút/tranh với chi phí quy đổi 60.000 đồng/giờ thì cộng thêm 5.000 đồng/tranh. Tổng đã 7.500 đồng/tranh trước chi phí khác. Điểm cần tối ưu có thể là công sửa, không phải đơn giá AI.

Không ước lượng thu nhập MMO chỉ từ lượt cài. Cần có dữ liệu trả tiền/giữ chân và chi phí thu hút thực tế trước khi tăng ngân sách.

## 17. Đo lường và vận hành lâu dài

### 17.1 Đợt thử nghiệm

Ưu tiên quan sát người dùng có đồng ý, phản hồi phụ huynh, crash qua kiểm thử và báo cáo tổng hợp cửa hàng. Chưa cần SDK theo dõi hành vi từng trẻ.

Theo dõi kỹ thuật từ server: lỗi API, thời gian job, chi phí AI, số tranh được duyệt, lỗi tải tổng hợp. Lượt tải không đồng nghĩa người dùng duy nhất, và không dùng IP để suy diễn hồ sơ trẻ.

### 17.2 Chỉ số ra quyết định

- Người thử có chọn và tô được tranh mà không cần giải thích không?
- Tranh dễ có vùng quá nhỏ không? Tranh chi tiết có bị rối không?
- Tỷ lệ lỗi/crash, mất tiến trình, tải thất bại.
- Tỷ lệ ảnh AI bị loại và thời gian sửa trung vị.
- Số giao dịch, restore, hoàn tiền và yêu cầu hỗ trợ.
- D1/D7 hoặc funnel hoàn thành chỉ đo khi đã có phương án dữ liệu phù hợp; không mặc định gắn mã bền vững cho trẻ để tính retention.

### 17.3 Nhịp vận hành đề xuất

- Hằng ngày: xem job lỗi, chi phí và lỗi tải/mua nghiêm trọng.
- Hằng tuần: duyệt lô nhỏ, phát hành 3–5 tranh chất lượng nếu đủ khả năng; xử lý phản hồi.
- Hằng tháng: rà chi phí/hiệu quả, dependency và chính sách; phục hồi thử backup theo lịch.
- Khi sự cố: tắt tạo AI hoặc mua mới bằng cấu hình nếu cần, nhưng giữ tô offline; rollback catalog về bản đã kiểm thử.

Mục tiêu vận hành ban đầu: RPO DB tối đa 24 giờ, RTO khoảng 4 giờ nếu hạ tầng cho phép. Kiểm thử khôi phục để xác nhận, không chỉ ghi mục tiêu trên giấy.

## 18. Rủi ro và phương án dự phòng

| Rủi ro | Dấu hiệu | Cách xử lý |
|---|---|---|
| Ảnh AI khó phân vùng | Nhiều nét hở, vùng nhập sai | Đổi template; sửa thủ công; dùng bộ tranh chuẩn hóa |
| Chi phí tạo tăng | Retry cao, nhiều ảnh bị loại | Dừng batch, giới hạn budget, so sánh cost/published |
| App chậm | Zoom giật, tô delay trên máy yếu | Giảm giới hạn ảnh/vùng, tối ưu renderer trước thêm tính năng |
| Mất tiến trình khi cập nhật | ID vùng thay đổi | Version bất biến, giữ asset cũ, không auto-migrate |
| Thu phí sai | Pending được mở, restore mất quyền | Server verify, idempotency, test refund/restore |
| Chính sách trẻ em | SDK thu dữ liệu không khai báo | Tắt ads/SDK, rà lại dữ liệu và phạm vi thị trường |
| Nội dung bị khiếu nại | Hình gần thương hiệu, sai độ tuổi | Gỡ catalog, truy nguồn, xử lý quyền theo quy trình |
| Nội dung trả phí bị sao chép | Gói tải bị chia sẻ | URL có hạn, quyền tải; chấp nhận giới hạn offline DRM |
| Khó có người dùng | Cài thấp, phụ huynh không thấy giá trị | Thử icon/screenshot trung thực, cải thiện kho tranh trước mua traffic |
| Quá tải scope | Admin sửa ảnh thành dự án riêng | Dùng công cụ ngoài, giữ MVP rõ, hoãn tính năng |

## 19. Backlog khởi động ngay

### Tuần đầu: kiểm chứng, chưa xây hết hệ thống

- [ ] Chốt Android-first, một chế độ tô, nhóm trẻ thử và dữ liệu không thu.
- [ ] Chọn 3 chủ đề cho 10 tranh alpha, chuẩn bị 20 brief AI thử và tiêu chí duyệt.
- [ ] Chọn AI ứng viên, kiểm tra quyền dùng thương mại và đặt budget thử.
- [ ] Viết schema manifest v1 và quy tắc mã hóa ID vùng.
- [ ] Tạo bộ fixture chuẩn gồm nét kín/hở, vùng lồng, vùng bé, màu trắng.
- [ ] Làm worker phân vùng đầu tiên và renderer Flutter tối thiểu.
- [ ] Build APK cài máy thật; đo chạm, zoom, lưu và mở lại.
- [ ] Ghi kết quả pass/fail, chi phí và thời gian sửa; quyết định tiếp tục hay đổi định dạng.

### Sau khi POC đạt

- [ ] Chốt phiên bản dependency và thiết lập môi trường dev/staging/production.
- [ ] Xây Laravel admin, catalog, storage, publish version.
- [ ] Hoàn thiện app và cache/offline trước commerce.
- [ ] Tích hợp queue AI, review, hạn mức và audit.
- [ ] Sản xuất và duyệt 30 tranh.
- [ ] Kiểm chứng nhu cầu mua, chốt danh tính/quyền tải rồi tích hợp bộ tranh và test restore/refund.
- [ ] Kiểm tra dữ liệu/SDK, hồ sơ Google Play và tester.
- [ ] Phát hành giới hạn; quyết định ads sau, không bật tự động.

## 20. Tiêu chí hoàn thành theo mốc

**APK alpha:** 10 tranh đã duyệt, một chế độ, 3 màn chính; bé chọn tranh/tô được; đóng mở không mất dữ liệu đã lưu; 6 tranh sẵn + tải tranh backend; hoạt động offline sau tải. Không cần thu tiền hoặc hoàn thiện toàn bộ AI Studio để đạt mốc này.

**Content beta:** admin tạo AI, xử lý, duyệt và xuất bản không cập nhật app; tỷ lệ đạt và công sửa được đo trên toàn lô; renderer và schema dùng cùng fixture; rollback không phá tiến trình.

**Commercial release:** mô hình thu tiền đã được chọn và kiểm chứng; quyền sở hữu/download/restore đã rõ; 30 tranh được duyệt nếu giữ mục tiêu này; backup/QA đạt; bản địa hóa tiếng Anh hoàn tất; hồ sơ phát hành phù hợp nhóm trẻ và quốc gia đã chọn.

Ưu tiên quyết định: kiểm chứng renderer + quy trình biến ảnh AI thành vùng tô trước. Backend đầy đủ chỉ có giá trị khi nội dung đầu ra thực sự chơi được.

## 21. Bản địa hóa và phát hành quốc tế

- Tiếng Anh làm bản gốc UI theo đề xuất; tiếng Việt dùng kiểm thử. Không hard-code chữ trong ảnh/tranh.
- Nhãn ngắn, biểu tượng dễ hiểu; không giả định trẻ đọc được tiếng Anh. Tên đồ vật có audio là phần sau, không bắt buộc để chơi.
- Nội dung tránh khuôn mẫu giới tính, chữ rác, biểu tượng gây hiểu nhầm theo văn hóa; phụ huynh xem nội dung mẫu trước mua.
- Chuỗi plural, định dạng giá, tiền tệ lấy từ store/locale; không tự quy đổi giá bằng tỷ giá cố định.
- Chọn 1–2 thị trường thử dựa trên khả năng hỗ trợ và nghĩa vụ dữ liệu. Chưa chọn US/EU mặc định chỉ vì dùng tiếng Anh.
- Trước mỗi thị trường: rà nghĩa vụ trẻ em, quyền riêng tư, thu phí/quảng cáo, điều khoản provider và khả năng hỗ trợ. Mở rộng sau khi vận hành nhóm đầu ổn.
- Tên thương hiệu phải kiểm tra cửa hàng/nhãn hiệu/tên miền trước icon chính thức; giữ Kids Coloring là tên nội bộ cho tới khi chủ dự án chốt.

## 22. Backlog kỹ thuật có phụ thuộc

| ID | Task | Phụ thuộc | Điều kiện nghiệm thu |
|---|---|---|---|
| P01 | Khóa P0 trẻ em, quốc tế, một chế độ | Không | Phạm vi alpha và giả định ghi rõ |
| P02 | Bộ fixture/manifest và decoder | P01 | Round-trip vùng khớp, file lỗi bị từ chối |
| P03 | AI benchmark toàn lô | P01 | Báo cáo cả ảnh loại và công sửa |
| P04 | Renderer trên máy thật | P02 | Chạm/zoom/màu trắng/undo không lỗi |
| P05 | Lưu local và khôi phục | P04 | Force-stop sau lưu không mất tranh |
| P06 | Backend admin nhập tranh + version | P02 | Publish nguyên tử ở DB, file đầy đủ |
| P07 | Catalog/tải/cache offline | P05, P06 | Tải lỗi không phá bản dùng được |
| P08 | 10 tranh alpha + UI tiếng Anh | P03, P07 | Hai nhóm trẻ thử riêng |
| P09 | Queue AI, review, budget, audit | P03, P06 | Không tự publish; retry không vượt hạn mức |
| P10 | Kiểm chứng phụ huynh trả tiền | P08 | Đề xuất bộ/giá/mô hình có bằng chứng |
| P11 | Chốt danh tính + session + entitlement | P10 | Review restore/replay/đổi máy/hoàn tiền |
| P12 | Commerce và kiểm thử cửa hàng | P11 | Pending/duplicate/refund/restore đạt |
| P13 | Nội dung thương mại + privacy + release | P09, P12 | Checklist quốc gia/SDK/store/backup đạt |

Các ô chưa chốt là quyết định sản phẩm/thiết kế trước mốc tương ứng, không phải lý do trì hoãn P01–P09. Chưa xây app hoặc backend chỉ bằng việc hoàn thành tài liệu này.
