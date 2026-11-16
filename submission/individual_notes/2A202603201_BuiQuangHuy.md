# Ghi chú cá nhân — 2A202603201 - Bùi Quang Huy

## 1. Pha 1: ba ca khó tôi tìm ra trong 40 ảnh

| Ảnh                                 | v0 không trả lời được gì                                                                                                                         | Quyết định ban đầu của tôi                                                                                                                                 |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| img_005, img_026, img_029           | Người đi xe máy/xe đạp: một box chung hay tách người và xe? Box xe có bao người không? Vật người cầm (ống dài img_029) có tính vào box xe không? | Tách riêng: mỗi người một box `person`, xe một box riêng chỉ bao xe, hai box được chồng nhau (R-02). Vật do người cầm không tính vào box xe (R-09)         |
| img_002                             | Xe đầu kéo chở nhiều ô tô: mấy box, ô tô được chở có gán không, người đứng trên rơ-moóc tính sao                                                 | 1 `truck` bao cả đầu kéo và rơ-moóc; mỗi ô tô được chở ≥ 20 px là một `car`, `occluded_level` theo phần khung che (R-08)                                   |
| img_017, img_027, img_038 / img_020 | v0 không nói gì về tượng, đài tưởng niệm hình pickup, xe mô hình, và xe trong gương chiếu hậu                                                    | Tượng/mô hình: không gán, không ignore, làm mẫu âm (R-05). Gương có hình rõ như xe thật: một `ignore_region` bao mặt gương (R-06); không gán xe chủ (R-07) |

Ca tôi chưa chốt, mang ra Pha 2 hỏi: img_011 (Predator là người hóa trang hay tượng), img_024 (xe vàng trên nóc là mô hình hay xe thật), img_028 (box `truck` có bao tượng ngựa không), img_033 (xe tải mép phải là thật hay phản chiếu), img_040 (xe máy thấy qua gương).

## 2. Pha 2: chỗ tôi bất đồng với nhóm

- Bản của tôi khác bản 101 ở: pickup (tôi xếp `truck` theo R-10, 101 xếp `car`); tuk-tuk và xích lô (tôi gộp vào một lớp `three_wheeler`, 101 tách `tuk_tuk` và `cyclo`); tủ đồ chơi img_038 (tôi không ignore, 101 dùng `ignore_region`).

So bản cá nhân với kết quả nhóm chốt trong `guideline_v1.0_agreed.md` (mục 0). Cả nhóm đã thống nhất các điểm dưới trong họp Pha 2.

| # (mục 0 v1.0) | Vấn đề | Đề xuất ban đầu của tôi | Nhóm chốt (v1.0) | Tôi đồng ý? Vì sao |
|---|---|---|---|---|
| #4 | Ngưỡng `occluded_level` | <10 / 10–40 / 40–80 / >80 | <10 / 10–<40 / 40–<70 / ≥70 | Đồng ý; dùng mốc chung trùng nhiều bản nhất để cả nhóm đo giống nhau |
| #7 | Gương chiếu hậu img_020 | `ignore_region` bao mặt gương | Không gán, không ignore | Đồng ý; đa số 4/5, phản chiếu không phải vật thật nên không gán |
| #8 | Người trong xe kín | Gán nếu thấy rõ đầu qua kính | Không gán người sau kính; xe hở vẫn gán | Đồng ý; người sau kính khó vẽ box nhất quán, xe hở vẫn gán người |
| #9 | Tượng ngựa img_028 | Chưa chốt | Box `truck` chỉ bao thân xe | Đồng ý; tượng là vật trang trí, không phải hàng chở |
| #10 | Predator img_011 | Chưa chốt | Không gán (tượng) | Đồng ý; Predator lớn hơn người thật, đứng yên như vật trưng bày nên coi là tượng |
| #11, #12 | Xe máy kim loại img_011, xe vàng img_024 | Chưa chốt | Gán lớp xe + `drivable=no` | Đồng ý; dùng `drivable=no` ghi nhận xe không chạy được, tránh tranh luận thật/giả |
| #15 | Bia mộ img_027 | Không gán | `truck` + `drivable=no` (R-14) | Đồng ý; vật hình xe cỡ thật vẫn có ích nếu đánh `drivable=no` để lọc khi huấn luyện |

## 3. Pha 3: ảnh tôi đã gán

- Số ảnh được giao: **24** (`submission/qc/assignments/2A202603201.txt`); file nhãn `submission/labels/2A202603201_BuiQuangHuy.json`, `validate_labels.py` báo hợp lệ, đủ 24 ảnh.
- Số box đã vẽ: **276** (person 135, car 56, motorcycle 45, ignore_region 12, bicycle 12, bus 5, van 4, three_wheeler 4, truck 3) · Số lần tick `can_review`: **3**
- Ca lưỡng lự nhất khi gán: **img_007.jpg** (ảnh tôi tick `can_review` nhiều nhất: 1 box).

## 4. Pha 4–5: tôi lệch ở đâu

- Số ca trong `calibration_candidates.csv` có tên tôi: **728** (lệch occluded_level 197, bỏ sót/thừa 143, IoU thấp 140, bất đồng ignore 108, lệch drivable 88, lệch truncated 27, sai lớp 22, can_review 3).
- Đồng thuận của tôi với các bạn (gộp các cặp có tôi, 387 cặp box ghép): IoU TB 0.841, cùng lớp 94.3%, cùng `occluded_level` 49.1%.
- Ảnh tôi lệch nhiều nhất: img_021.jpg (92 ca), img_032.jpg (91 ca), img_011.jpg (66 ca).
- Nguyên nhân:
  - lệch `occluded_level` (197 ca) và bất đồng vùng ignore (108 ca): **guideline mơ hồ**, cả nhóm cùng lệch vì ước lượng % che và ranh giới vùng dày bằng mắt; v1.1 thêm R-15 để chốt cách ước lượng
  - lệch `drivable` (88 ca): **tôi chưa nắm quy tắc**, đặt `drivable=no` cho box `person` trong khi mục 2.2 quy định `person` luôn `yes`; v1.1 thêm R-17
  - bỏ sót/thừa (143 ca) và IoU thấp (140 ca): mỗi người dừng vẽ ở ngưỡng vật nhỏ khác nhau và ôm box chưa đều (R-01, R-03)
- Quyết định calibration quan trọng nhất với tôi: R-17 (`person` luôn `drivable=yes`), vì lỗi này đến từ cách tôi hiểu quy tắc.

## 5. Dùng AI

- Claude Code: so guideline cá nhân với yêu cầu trong GUIDE/RUBRIC/template, đổi tên file sang mã 201 và bỏ thông tin cá nhân, thống nhất ký hiệu `[chưa chốt, cần họp]`, tạo khung ghi chú này. Cách tôi kiểm lại: tự mở lại các ảnh được dẫn, đối chiếu từng quyết định với ảnh, sửa chỗ không đồng ý.

## 6. Bài học

- Một điều tôi sẽ làm khác khi viết guideline lần sau: ghi rõ thuộc tính nào áp cho lớp nào ngay trong bảng thuộc tính, kèm giá trị mặc định. Tôi đặt `drivable=no` cho 88 box `person` vì hiểu sai phạm vi của `drivable`; nếu guideline có dòng "`person`: luôn để mặc định" ngay cạnh định nghĩa thuộc tính thì đã tránh được.
