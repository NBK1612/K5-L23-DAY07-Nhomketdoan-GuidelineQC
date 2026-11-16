# Guideline gán nhãn — v0.1 (bản cá nhân) — 2A202603201 - Bùi Quang Huy

- Phiên bản: v0.1 (bản cá nhân, viết từ v0 sau khi xem đủ 40 ảnh)
- Ngày: 2026-11-16
- Người viết: 2A202603201 - Bùi Quang Huy (Nhóm Kết Đoàn)
- Loại nhãn: bounding box trên ảnh đường phố, công cụ CVAT, xuất COCO 1.0

> Những chỗ đánh dấu **[chưa chốt, cần họp]** là chỗ tôi chưa chắc, cần hỏi reviewer hoặc đem ra Pha 2.
> Các chỗ còn lại là quyết định theo quy tắc (có Rule ID).

---

## 1. Mục tiêu

Nhãn dùng để huấn luyện mô hình **phát hiện người và phương tiện trên ảnh 2D** cho camera giao thông.
Mô hình cần tìm ra mọi người và phương tiện **thật** có thể tham gia giao thông, kể cả khi đang đỗ hoặc đang được trưng bày.

- Lỗi nguy hiểm nhất là **bỏ sót người hoặc xe thật**, nhất là người đi xe máy hoặc xe đạp. Hệ thống phía sau sẽ không phản ứng với vật bị bỏ sót.
- Lỗi nghiêm trọng thứ hai là **gán vật giả thành vật thật** (tượng, ma-nơ-canh, đồ chơi, hình trên billboard, ảnh phản chiếu). Mô hình học từ các nhãn này sẽ báo động sai.
- Ngoài phạm vi: phân đoạn, theo dõi qua nhiều khung, nhận dạng danh tính, biển số.

## 2. Lớp và thuộc tính

### Lớp

| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Xe chở người thân thấp, có nắp capo nhô ra phía trước, một hàng cửa sổ bên hông, chiều dài ≤ khoảng 2 lần chiều cao: sedan, hatchback, SUV, taxi, xe diễu hành có người lái thật. | img_015, img_023, img_032; xe được chở trên rơ-moóc img_002 | Pickup → `truck` (R-10); xe mô hình img_038 → không gán (R-05) |
| `truck` | Xe chở hàng có cabin và thùng/khung tách riêng: pickup, xe tải nhẹ, xe tải, đầu kéo kèm rơ-moóc. | img_001, img_007, img_012, img_036, img_037; đầu kéo img_002 | Van thân liền → `van`; đài tưởng niệm hình pickup img_027 → không gán |
| `van` | Xe một khối thân liền, mũi ngắn hoặc phẳng, chở người hoặc hàng, thân dài ≈ một xe con, chỉ có cửa trượt/cửa hông như xe con, không có cửa khách riêng kiểu xe buýt. | img_005 (Hiace bạc), img_016 | Minibus Coaster dài có cửa khách img_009 → `bus` |
| `bus` | Xe chở khách thân dài rõ rệt hơn xe con (≥ khoảng 1,5 lần), có cửa lên xuống kiểu xe khách và một dãy dài cửa sổ hành khách. | img_009, img_031, img_037 (xe xanh phía sau), img_040 | Van img_005 |
| `motorcycle` | Xe máy, xe tay ga, mô tô, kể cả sidecar. **Box chỉ bao xe.** | img_005, img_008, img_022 (có sidecar), img_026 | Xe ba bánh tuk-tuk → `three_wheeler` |
| `bicycle` | Xe đạp hai bánh không động cơ. **Box chỉ bao xe.** | img_006, img_029, img_034 | Xích lô (ba bánh) → `three_wheeler` |
| `three_wheeler` | Xe ba bánh chở người/hàng: tuk-tuk, auto rickshaw, xích lô. | img_004, img_016, img_023, img_035, img_040 | Xe máy có sidecar img_022 → `motorcycle` |
| `person` | Người thật nhìn thấy được: đi bộ, người lái, người ngồi sau, người trong xe nếu thấy rõ đầu. | img_005, img_014, img_034 | Tượng img_017, ma-nơ-canh img_033, người trên billboard img_018, tranh tường img_026 → không gán |
| `ignore_region` | Vùng bỏ qua (không phải vật): đám đông/bãi xe dày không tách được, vật thật quá nhỏ, mặt gương có ảnh phản chiếu. | Bãi xe img_030, đám đông sau rào img_019, gương img_020 | Không dùng ignore cho tượng/đồ chơi (R-05) |

**Vật không thuộc lớp nào** (cone, ngựa, xe đẩy hàng rong, chim, biển báo): không gán, không ignore.
**Vật thật nhưng không vẽ box được** (quá nhỏ, quá dày): dùng `ignore_region` theo R-03 và R-04.

### Thuộc tính

| Thuộc tính | Mức | Nghĩa và ngưỡng |
|---|---|---|
| `occluded_level` | `0` | Bị vật khác che < 10% diện tích vật (so với hình dung cả vật nếu không bị che). |
| | `1` | Bị che từ 10% đến < 40%. |
| | `2` | Bị che từ 40% đến 80%. Người ngồi trong khoang xe (R-11) tối thiểu là mức 2. |
| | `3` | Bị che > 80%. |
| `truncated` | `no` / `yes` | `yes` khi box chạm mép ảnh **và** một phần vật nằm ngoài khung. Phần bị cắt mép **không** tính vào `occluded_level`. |
| `can_review` | tick / không | Tick trên box đã vẽ khi còn nghi ngờ về lớp, ranh giới, hoặc vật có đủ điều kiện gán hay không (kể cả vật mức 3 mà không chắc lớp). Nghi ngờ về một vùng **chưa vẽ box** (không có box để tick): ghi vào mục "Câu hỏi chưa chốt" ở cuối mục 4 để mang ra họp. |

## 3. Quy tắc vẽ box

| ID | Quy tắc |
|---|---|
| R-01 | Box ôm sát **phần nhìn thấy** của vật, sai lệch không quá ~2 px mỗi cạnh. Không đoán phần bị che sau vật khác. Gương chiếu hậu và ăng-ten **gắn trên chính xe đó** tính vào box (hình phản chiếu bên trong gương thì xử lý theo R-06). Bóng đổ thì không. |
| R-02 | **Người và xe hai bánh/ba bánh là các box riêng.** Mỗi người lái và mỗi người ngồi sau có một box `person` (bao cả mũ bảo hiểm). Xe có một box riêng theo lớp của nó, **không** cố bao phần người. Hai box được phép chồng nhau. |
| R-03 | Vật thật có chiều cao box < 20 px thì không vẽ box. Nếu có cụm ≥ 3 vật nhỏ như vậy ở gần nhau, vẽ một `ignore_region` bao cụm. Vật nhỏ đứng lẻ (< 3 vật) thì bỏ qua, không vẽ box và không vẽ ignore. |
| R-04 | Đám đông hoặc bãi xe dày: khi ≥ 5 vật cùng lớp chồng nhau quá 50% và không vẽ được box riêng theo R-01, vẽ **một** `ignore_region` bao vùng đó. Vật ở phía trước vẫn tách được thì vẫn vẽ box riêng (box riêng được nằm chồng lên vùng ignore). |
| R-05 | Tượng, ma-nơ-canh, đồ chơi, mô hình thu nhỏ, đài tưởng niệm, hình trên billboard/tranh tường/màn hình, nhân vật hoạt hình: **không vẽ box, không dùng ignore**. Đây là mẫu âm, mô hình cần học rằng chúng không phải người hay xe. |
| R-06 | Ảnh phản chiếu trong gương, kính, mặt đường ướt: không vẽ box cho vật phản chiếu. Nếu mặt phản chiếu cho hình rõ như vật thật (gương chiếu hậu), vẽ `ignore_region` bao mặt gương. Phản chiếu mờ trên mặt đường thì không cần ignore. |
| R-07 | Không gán xe chủ (ego), tức xe chứa máy ảnh, dù thấy cửa, nắp capo hay gương của xe đó. |
| R-08 | Xe chở xe: đầu kéo cộng rơ-moóc là **một** box `truck`. Mỗi xe được chở là một box `car` riêng nếu đạt R-03, `occluded_level` tính theo phần bị khung che. |
| R-09 | Hàng gắn hoặc chất cố định trên xe (hàng trong thùng pickup, bó hàng trên nóc) **tính vào** box xe. Vật do người cầm hoặc vác (ống dài img_029) **không tính** vào box xe. |
| R-10 | Pickup → `truck`. SUV, crossover → `car`. Sidecar tính vào box `motorcycle`. Xe đẩy hàng rong không phải phương tiện, nên không gán. |
| R-11 | Người ngồi trong khoang xe (sau kính, hoặc trong khoang hở như tuk-tuk): vẽ `person` khi thấy rõ đầu và box ≥ 20 px. Box chỉ bao phần người thấy được, `occluded_level` ≥ 2. Người lái/ngồi sau xe máy, xe đạp **không** thuộc R-11 mà theo R-02, mức che tính bình thường. |
| R-12 | Ảnh không có vật đủ điều kiện vẫn phải có mặt trong Task và file COCO, với 0 annotation. |

## 4. Ca khó

| Ảnh | Tình huống | Rule ID | Quyết định | Lý do |
|---|---|---|---|---|
| img_002 | Xe chở xe, người mặc áo phản quang đứng cuối rơ-moóc | R-08, R-11, R-01 | 1 `truck` bao cả đầu kéo và rơ-moóc. Mỗi xe được chở là `car`, xe tầng dưới có occ 1–2. 1 `person` occ 1 | Các xe được chở là xe thật. Mô hình cần phân biệt chúng với xe đang chạy, nhưng việc đó thuộc về ngữ cảnh chứ không thuộc lớp |
| img_004 | Hai tuk-tuk đỗ, có tài xế ngồi trong | R-02, R-11 | 2 `three_wheeler`. 2 `person` (tài xế) occ 2. Cone không gán | v0 không có lớp phù hợp cho tuk-tuk. Ép vào `car` hoặc `motorcycle` sẽ gây lệch giữa các annotator |
| img_005 | 4–5 người đi xe máy cận cảnh, van phía sau, xe con mép trái | R-02, R-01, R-11 | Mỗi người một `person`. Xe máy bị người che nhiều thì occ 2–3. Van occ 2. Xe con và van mép trái `truncated=yes` | Mẫu chuẩn cho R-02. Chỉ cần dự đoán số người và số xe nhìn thấy |
| img_006 | Dòng xe máy và xe đạp, nhiều người ở xa | R-02, R-03, R-04 | Người và xe máy/xe đạp phía trước có box riêng. Cụm xa < 20 px hoặc dày thì một `ignore_region` | Đây là ảnh kiểm tra lại quy tắc người đi xe máy ở cự ly xa |
| img_008 | Bãi xe máy dày ban đêm, 2 tài xế xe ôm | R-02, R-04, R-01 | 2 `person` và xe máy của họ. Xe đỗ đầu hàng tách được thì vẽ. Phần giữa dày thì `ignore_region` | Không thể tách từng xe đỗ chồng nhau một cách nhất quán |
| img_012 | Pickup chở hàng, xe con phía trước | R-10, R-09 | 1 `truck` bao cả hàng trong thùng (`truncated=yes`). 1 `car` | Pickup là xe chở hàng |
| img_017 | Tượng người cưỡi ngựa đội cone | R-05, R-12 | 0 annotation | Tượng là mẫu âm |
| img_020 | Xe trong gương chiếu hậu, thấy cửa xe chủ | R-06, R-07 | 1 `ignore_region` bao mặt gương. Không gán xe chủ | Xe trong gương có hình rõ như xe thật nên dùng ignore để khỏi tính lỗi bỏ sót |
| img_011 | Nhân vật Predator lớn và các xe máy độ bằng kim loại | R-05 | **[chưa chốt, cần họp]** Hỏi reviewer: người hóa trang hay tượng? Xe máy chạy được hay là tác phẩm điêu khắc? Tạm thời không gán | Thiếu thông tin để quyết định |
| img_019 | Xe diễu hành, hình chuột hoạt hình, đám đông sau rào | R-05, R-04, R-11 | `car` + tài xế `person`. Hình chuột không gán. Đám đông dày sau rào thì `ignore_region`, người ở hàng đầu tách được thì vẽ | Xe có người lái thật chạy trên đường |
| img_024 | Xe vàng trên nóc xe đen trong bảo tàng | R-05, mục 1 | Xe đen và các xe trưng bày là `car` (xe thật đang trưng bày vẫn gán, xem mục 1). **[chưa chốt, cần họp]** Hỏi reviewer: xe vàng là mô hình hay xe thật? | Kích thước nhỏ bất thường |
| img_026 | Đôi đi xe máy trước tranh tường cảng | R-02, R-05 | 2 `person` và 1 `motorcycle` (`truncated=yes`). Tranh tường không gán | Người trên tranh tường là hình vẽ, không phải người thật |
| img_027 | Đài tưởng niệm bằng đá hình pickup | R-05, R-12 | 0 annotation | Không phải phương tiện |
| img_028 | Tượng ngựa trắng đứng trên pickup cổ | R-05, R-09, R-10 | 1 `truck`, 1 `car` đỏ phía sau. Ngựa không gán box riêng. **[chưa chốt, cần họp]** Tượng ngựa là "hàng chất cố định" (R-09, box `truck` bao luôn ngựa) hay chỉ là vật trưng bày (box `truck` chỉ bao thân xe)? | R-05 và R-09 cho hai cách vẽ box khác nhau |
| img_029 | Xe máy và xe đạp cùng khiêng ống dài | R-02, R-09 | 2 `person`, 1 `motorcycle`, 1 `bicycle`. Ống không tính vào box | Vật do người cầm |
| img_030 | Bãi xe máy kín | R-04 | Phần lớn là `ignore_region`. Xe phía trước tách được thì vẽ | Không tách được từng xe một cách nhất quán |
| img_033 | Ma-nơ-canh, gương và cửa xe tải trắng | R-05, R-06 | Ma-nơ-canh không gán. **[chưa chốt, cần họp]** Phần xe tải ở mép phải là xe thật hay phản chiếu? Nếu đã vẽ thì tick `can_review`; nếu chưa vẽ thì ghi vào "Câu hỏi chưa chốt" | Ảnh chồng nhiều lớp nên khó xác định |
| img_035 | Auto rickshaw chở bó hàng cao | R-09 | `three_wheeler` bao cả bó hàng | Hàng chất trên xe |
| img_038 | Tủ kính đồ chơi, xe mô hình | R-05 | 0 annotation | Mẫu âm |
| img_040 | Người đội mũ bảo hiểm cận cảnh, taxi, tuk-tuk, SUV, bus | R-02, R-01, R-06 | `person` (truncated), `car` taxi (truncated), `three_wheeler`, `car` SUV, `bus` ở hậu cảnh, `motorcycle` (phần gương thấy được). **[chưa chốt, cần họp]** Nếu "phần gương" là gương của chính xe máy bị che thì giữ box theo R-01; nếu là xe máy hiện **trong** gương phản chiếu thì theo R-06 không vẽ box | Ảnh có đủ các lớp mới thêm |

**Câu hỏi chưa chốt (mang ra họp):**

- img_011: nhân vật Predator là người hóa trang hay tượng? Xe máy độ là xe thật hay điêu khắc?
- img_024: xe vàng trên nóc là mô hình hay xe thật?
- img_028: box `truck` có bao tượng ngựa không (R-05 hay R-09)?
- img_033: xe tải trắng ở mép phải là xe thật hay phản chiếu?
- img_040: `motorcycle` "phần gương thấy được" là xe thật hay hình phản chiếu?

## 5. QC và phiên bản

- Ngưỡng QC lấy từ `taxonomy.json` → `rules`: IoU ghép 0.5; IAA IoU ≥ 0.85, cùng lớp ≥ 95%; box ≥ 50 px dùng ngưỡng 0.85, nhỏ hơn dùng 0.7; lỗi theo ảnh ≤ 2% khi Release.
- Ca mơ hồ: annotator tick `can_review` trên box (hoặc ghi vào "Câu hỏi chưa chốt" nếu chưa vẽ box). Nhóm chốt trong họp calibration và ghi rule ID vào `calibration_decisions.csv`.
- Thay đổi làm đổi cách chọn vật, lớp, box hay thuộc tính là `semantic` và phải tăng phiên bản. Sửa câu chữ là `editorial`.

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v0 | Bản khởi đầu của giảng viên | — |
| v0.1 (bản cá nhân 2A202603201 - Bùi Quang Huy) | Đề xuất thêm `bus`, `bicycle`, `three_wheeler`; ngưỡng `occluded_level` theo %; quy tắc R-01…R-12; bảng ca khó | img_004, img_009, img_006, img_002, img_017, img_020 |

Các đề xuất này sẽ được so với bản của các thành viên khác ở Pha 2 trước khi đưa vào `guideline_v1.0_agreed.md`.
