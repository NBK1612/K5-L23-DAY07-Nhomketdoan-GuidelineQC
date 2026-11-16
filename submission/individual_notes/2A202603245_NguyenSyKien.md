# Ghi chú cá nhân — 2A202603245 - Nguyễn Sỹ Kiên

## 1. Pha 1: ba ca khó tôi tìm ra trong 40 ảnh
| Ảnh | v0 không trả lời được gì | Quyết định ban đầu của tôi |
|---|---|---|
| `img_011.jpg` | Người cosplay Predator đứng cạnh dàn xe mô tô độ quái dị bằng kim loại: v0 không có quy tắc nào cho nhân vật hoá trang quái vật và xe máy điêu khắc nghệ thuật. | Quyết định gán `person` cho Predator (người mặc đồ cosplay) và gán `motorcycle` cho xe máy độ; tuy nhiên gắn cờ **`can_review = true`** để trực tiếp rà soát thủ công lại bằng mắt thường và họp nhóm chốt xem xe này có phải phương tiện thật tham gia giao thông không. |
| `img_017.jpg` / `img_027.jpg` / `img_033.jpg` | Tượng đài Công tước Wellington cưỡi ngựa đá (`img_017`), bia mộ đá hoa cương tạc hình xe bán tải (`img_027`), ma-nơ-canh cửa hàng thời trang (`img_033`): v0 không phân biệt thực thể sống/thật với tượng/mẫu vật vô tri. | Quyết định coi tất cả là **mẫu âm (negative samples)** và **KHÔNG GÁN NHÃN** để chống hiện tượng xe tự lái phanh ma (phantom braking). |
| `img_028.jpg` / `img_029.jpg` | Xe bán tải cõng tượng con ngựa lớn trên thùng (`img_028`), xe máy chở bó ống thép siêu dài vươn ra 5 mét (`img_029`): v0 không nói rõ box xe ôm sát thân xe hay bao trùm toàn bộ vật cồng kềnh nhô ra. | Quyết định tạm thời gán `truck` bao quanh xe bán tải (`img_028`) và gán `motorcycle` ôm thân xe máy (`img_029`), đánh dấu **`can_review = true`** để đưa ra họp Pha 2 thống nhất quy tắc viền box cho hàng siêu trường siêu trọng. |

## 2. Pha 2: chỗ tôi bất đồng với nhóm

So bản cá nhân với kết quả nhóm chốt trong `guideline_v1.0_agreed.md` (mục 0). Cả nhóm đã thống nhất các điểm dưới trong họp Pha 2.

| # (mục 0 v1.0) | Vấn đề | Đề xuất ban đầu của tôi | Nhóm chốt (v1.0) | Tôi đồng ý? Vì sao |
|---|---|---|---|---|
| #4 | Ngưỡng `occluded_level` | <10 / 10–35 / 35–70 / ≥70 | <10 / 10–<40 / 40–<70 / ≥70 | Đồng ý; dùng mốc chung trùng nhiều bản nhất để cả nhóm đo giống nhau |
| #6 | Tủ đồ chơi img_038 | Một `ignore_region` bao tủ | Không gán, không ignore | Đồng ý; đồ chơi thu nhỏ là mẫu âm và phần lớn < 20 px |
| #9 | Tượng ngựa img_028 | `can_review` | Box `truck` chỉ bao thân xe | Đồng ý; tượng là vật trang trí, không phải hàng chở |
| #10 | Predator img_011 | `person` + `can_review` | Không gán (tượng) | Đồng ý; Predator lớn hơn người thật, đứng yên như vật trưng bày nên coi là tượng |
| #12, #15 | Xe vàng img_024 | `can_review` | `car` + `drivable=no` | Đồng ý; dùng `drivable=no` ghi nhận xe không chạy được, tránh tranh luận thật/giả |
| #15 | Bia mộ img_027 | Không gán | `truck` + `drivable=no` (R-14) | Đồng ý; vật hình xe cỡ thật vẫn có ích nếu đánh `drivable=no` để lọc khi huấn luyện |

## 3. Pha 3: ảnh tôi đã gán

- Số ảnh được giao: **24** (`submission/qc/assignments/2A202603245.txt`); file nhãn `submission/labels/2A202603245_NguyenSyKien.json`, `validate_labels.py` báo hợp lệ, đủ 24 ảnh.
- Số box đã vẽ: **253** (person 83, car 75, motorcycle 57, truck 14, ignore_region 13, bicycle 4, van 3, three_wheeler 2, bus 2) · Số lần tick `can_review`: **0**
- Ca lưỡng lự nhất khi gán: không tick `can_review`; ảnh có nhiều ca IAA liên quan đến tôi nhất là **img_032.jpg** (82 ca).

## 4. Pha 4–5: tôi lệch ở đâu

- Số ca trong `calibration_candidates.csv` có tên tôi: **600** (bất đồng ignore 139, bỏ sót/thừa 138, lệch occluded_level 138, IoU thấp 99, lệch drivable 51, lệch truncated 18, sai lớp 17).
- Đồng thuận của tôi với các bạn (gộp các cặp có tôi, 349 cặp box ghép): IoU TB 0.856, cùng lớp 95.1%, cùng `occluded_level` 60.5%.
- Ảnh tôi lệch nhiều nhất: img_032.jpg (82 ca), img_008.jpg (69 ca), img_034.jpg (53 ca).
- Nguyên nhân:
  - lệch `occluded_level` (138 ca) và bất đồng vùng ignore (139 ca): **guideline mơ hồ**, cả nhóm cùng lệch vì ước lượng % che và ranh giới vùng dày bằng mắt; v1.1 thêm R-15 để chốt cách ước lượng
  - lệch `drivable` (51 ca): chủ yếu do quy tắc đồ chơi/xe không chạy được (R-14) và lỗi `person` của một người khác
  - bỏ sót/thừa (138 ca) và IoU thấp (99 ca): mỗi người dừng vẽ ở ngưỡng vật nhỏ khác nhau và ôm box chưa đều (R-01, R-03)
- Quyết định calibration quan trọng nhất với tôi: R-15 (cách ước lượng `occluded_level`, phân vân chọn mức thấp hơn), vì đây là nhóm ca lệch nhiều nhất (427 ca toàn nhóm).

## 5. Dùng AI
- Công cụ: Antigravity Assistant.
- Mục đích: Hỗ trợ rà soát 40 ảnh, phân tích các rủi ro kỹ thuật (False Negative / False Positive) và chuẩn hoá cấu trúc bảng theo chuẩn COCO/CVAT.
- Cách kiểm tra: Bản thân tôi trực tiếp mở từng ảnh trong `data/images/`, tự mắt kiểm tra các ca khó (`can_review`), xác nhận thực tế và đưa ra quyết định độc lập theo góc nhìn của mình.

## 6. Bài học
- Một điều tôi sẽ làm khác khi viết guideline lần sau: Cần chú ý ngay từ đầu các tình huống đặc biệt về phản chiếu (gương, vũng nước) và các công trình nghệ thuật/mẫu âm để đưa ra quy tắc loại trừ sớm, tránh gây tranh cãi kéo dài khi gán nhãn.

