# Ghi chú cá nhân — 2A202603101 - Nguyễn Xuân Anh

## 1. Pha 1: ba ca khó tôi tìm ra trong 40 ảnh
| Ảnh | v0 không trả lời được gì | Quyết định ban đầu của tôi |
|---|---|---|
| `img_004.jpg` / `img_035.jpg` | Xe tuk-tuk 3 bánh (`img_004`) và xe Auto Rickshaw (`img_035`): taxonomy v0 chỉ có 5 lớp không chứa xe 3 bánh có động cơ và buồng chở khách này. | Quyết định đề xuất bổ sung lớp mới **`tuk_tuk`** để phân loại chính xác phương tiện đặc thù châu Á này, thay vì ép vào `motorcycle` hay `car`. |
| `img_006.jpg` / `img_009.jpg` / `img_023.jpg` | Đoàn xích lô du lịch tại Huế chở khách: phương tiện 3 bánh chạy bằng sức người đạp, v0 không có quy tắc nào cho xích lô. | Quyết định đề xuất thêm lớp **`cyclo`** cho khung xe xích lô và gán từng box **`person`** cho người đạp xe lẫn du khách ngồi trên ghế; đồng thời đề xuất thêm lớp **`bicycle`** cho xe đạp chở mít (`img_003`). |
| `img_032.jpg` / `img_040.jpg` | Xe buýt công cộng đón trả khách tại trạm (`img_032`) và đại lộ Bangkok (`img_040`): v0 chỉ có `car`, `truck`, `van`, hoàn toàn thiếu xe buýt chở khách công cộng. | Quyết định đề xuất bổ sung lớp **`bus`** vì xe buýt có kích thước và động học khác biệt hoàn toàn so với xe tải chở hàng (`truck`) hay xe chở khách mini (`van`). |

## 2. Pha 2: chỗ tôi bất đồng với nhóm
(Sẽ cập nhật sau phiên họp thống nhất Pha 2)

## 3. Pha 3: ảnh tôi đã gán
(Sẽ cập nhật sau khi phân chia ảnh và hoàn thành gán nhãn Pha 3)

## 4. Pha 4–5: tôi lệch ở đâu
(Sẽ cập nhật sau khi chạy so sánh đo IAA và họp calibration)

## 5. Dùng AI
- Công cụ: Antigravity Assistant.
- Mục đích: Hỗ trợ khảo sát cấu trúc taxonomy ADAS quốc tế, chuẩn hóa định nghĩa lớp và định lượng hóa các ngưỡng che khuất theo phần trăm diện tích.
- Kiểm tra kết quả: Tự mình kiểm tra đối chiếu trực tiếp từng ca khó trên 40 bức ảnh trong `data/images/` để đảm bảo taxonomy mở rộng bám sát 100% ngữ cảnh thực tế của bộ dữ liệu giao thông châu Á.

## 6. Bài học
- Một điều tôi sẽ làm khác khi viết guideline lần sau: Cần phân tích kỹ bối cảnh địa phương của tập dữ liệu ngay từ đầu để xây dựng taxonomy đủ rộng và bao quát, tránh việc để mô hình phải "đoán mò" các phương tiện lai ghép đặc trưng của khu vực Đông Nam Á.
