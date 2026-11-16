# Ghi chú cá nhân — 2A202603101 - Nguyễn Xuân Anh

## 1. Pha 1: ba ca khó tôi tìm ra trong 40 ảnh
| Ảnh | v0 không trả lời được gì | Quyết định ban đầu của tôi |
|---|---|---|
| `img_004.jpg` / `img_035.jpg` | Xe tuk-tuk 3 bánh (`img_004`) và xe Auto Rickshaw (`img_035`): taxonomy v0 chỉ có 5 lớp không chứa xe 3 bánh có động cơ và buồng chở khách này. | Quyết định đề xuất bổ sung lớp mới **`tuk_tuk`** để phân loại chính xác phương tiện đặc thù châu Á này, thay vì ép vào `motorcycle` hay `car`. |
| `img_006.jpg` / `img_009.jpg` / `img_023.jpg` | Đoàn xích lô du lịch tại Huế chở khách: phương tiện 3 bánh chạy bằng sức người đạp, v0 không có quy tắc nào cho xích lô. | Quyết định đề xuất thêm lớp **`cyclo`** cho khung xe xích lô và gán từng box **`person`** cho người đạp xe lẫn du khách ngồi trên ghế; đồng thời đề xuất thêm lớp **`bicycle`** cho xe đạp chở mít (`img_003`). |
| `img_032.jpg` / `img_040.jpg` | Xe buýt công cộng đón trả khách tại trạm (`img_032`) và đại lộ Bangkok (`img_040`): v0 chỉ có `car`, `truck`, `van`, hoàn toàn thiếu xe buýt chở khách công cộng. | Quyết định đề xuất bổ sung lớp **`bus`** vì xe buýt có kích thước và động học khác biệt hoàn toàn so với xe tải chở hàng (`truck`) hay xe chở khách mini (`van`). |

## 2. Pha 2: chỗ tôi bất đồng với nhóm

So bản cá nhân với kết quả nhóm chốt trong `guideline_v1.0_agreed.md` (mục 0). Cả nhóm đã thống nhất các điểm dưới trong họp Pha 2.

| # (mục 0 v1.0) | Vấn đề | Đề xuất ban đầu của tôi | Nhóm chốt (v1.0) | Tôi đồng ý? Vì sao |
|---|---|---|---|---|
| #1 | Thêm lớp | 9 lớp, tách `tuk_tuk` và `cyclo` | `three_wheeler` gộp tuk-tuk, auto-rickshaw, xích lô | Đồng ý; 4/5 bản cùng hướng, gộp 3 bánh vào một lớp để người gán không lệch nhau |
| #2 | Xích lô | Lớp riêng `cyclo` | `three_wheeler` | Đồng ý; xích lô cùng dáng 3 bánh với tuk-tuk, tách lớp riêng dễ gây lệch |
| #3 | Pickup (img_001, img_012, img_037) | `car` | `truck` | Đồng ý; dùng tiêu chí nhìn thấy được là cabin tách khỏi thùng |
| #4 | Ngưỡng `occluded_level` | <5 / 5–30 / 30–70 / >70 | <10 / 10–<40 / 40–<70 / ≥70 | Đồng ý; dùng mốc chung trùng nhiều bản nhất để cả nhóm đo giống nhau |
| #6 | Tủ đồ chơi img_038 | Một `ignore_region` bao tủ | Không gán, không ignore | Đồng ý; đồ chơi thu nhỏ là mẫu âm và phần lớn < 20 px |
| #9 | Tượng ngựa img_028 | `can_review` | Box `truck` chỉ bao thân xe | Đồng ý; tượng là vật trang trí, không phải hàng chở |
| #10 | Predator img_011 | `person` (người hóa trang) | Không gán (tượng) | Đồng ý; Predator lớn hơn người thật, đứng yên như vật trưng bày nên coi là tượng |
| #13 | Ống dài img_029 | Box xe bao cả hàng | Box xe không bao ống | Đồng ý; ống do người cầm, vươn xa gấp nhiều lần thân xe |
| #15 | Bia mộ img_027, xe vàng img_024 | Không gán img_027; img_024 gán `car` | Gán lớp xe + `drivable=no` | Đồng ý; vật hình xe cỡ thật vẫn có ích nếu đánh `drivable=no` để lọc khi huấn luyện |

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
