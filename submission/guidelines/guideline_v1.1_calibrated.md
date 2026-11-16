# Guideline gán nhãn — v1.1 (sau calibration) — Nhóm Kết Đoàn

- Phiên bản: v1.1 (v1.0 + quyết định calibration sau Pha 4)
- Ngày: 2026-11-16
- Người viết / duyệt: 2A202603201 soạn, cả nhóm duyệt (2A202603101, 2A202603103, 2A202603191, 2A202603201, 2A202603245)
- Loại nhãn: bounding box 2D, CVAT, xuất COCO 1.0
- Taxonomy đi kèm: `submission/taxonomy.json` (version `v1.0`, không đổi lớp/thuộc tính)

> **Trạng thái: v1.1 sau calibration.** Dựa trên v1.0 và kết quả IAA (`submission/qc/iaa_summary.md`: IoU TB 0.845, cùng lớp 95.2% trên 938 cặp box; 1781 ca trên 37 ảnh).
> Mọi quyết định ghi trong `submission/qc/calibration_decisions.csv`; tóm tắt ở mục 6. Thay đổi so với v1.0 đánh dấu **[v1.1]**.

---

## 0. Bất đồng giữa các bản cá nhân và quyết định

| # | Vấn đề | Các phương án (mã) | Nhóm chốt | Lý do |
|---|---|---|---|---|
| 1 | Thêm lớp nào ngoài v0 | `bus`, `bicycle`, `three_wheeler` (2A202603191, 2A202603103, 2A202603201, 2A202603245); `bus`, `bicycle`, `tuk_tuk`, `cyclo` (2A202603101) | Thêm `bus`, `bicycle`, `three_wheeler` | 4/5 bản trùng khớp; `bus` và `bicycle` có ở cả 5 bản. Không thêm `horse` vì 40 ảnh chỉ có tượng ngựa (img_017, img_028) |
| 2 | Xích lô (img_006, img_009, img_023, img_034, img_039) | `three_wheeler` (2A202603191, 2A202603103, 2A202603201, 2A202603245); lớp riêng `cyclo` (2A202603101) | `three_wheeler` | Cùng dáng 3 bánh với tuk-tuk; tách 2 lớp dễ lệch giữa người gán |
| 3 | Pickup (img_001, img_012, img_028, img_037) | `truck` (2A202603191, 2A202603103, 2A202603201, 2A202603245); `car` (2A202603101) | `truck` | Tiêu chí nhìn thấy được: cabin tách khỏi thùng. Pickup có nắp thùng (img_037) vẫn là `truck` |
| 4 | Ngưỡng `occluded_level` | <10/10–40/40–70/≥70 (2A202603191); <10/10–40/40–80/>80 (2A202603201); <10/10–35/35–70/≥70 (2A202603245); <5/5–30/30–70/>70 (2A202603101); 0/<30/30–70/≥70 (2A202603103) | <10 / 10–<40 / 40–<70 / ≥70 | Mốc 10 trùng ở 2A202603191, 2A202603201, 2A202603245; mốc 70 trùng ở 2A202603191, 2A202603103, 2A202603245, 2A202603101; mốc 40 theo 2A202603191, 2A202603201 |
| 5 | Vật nhỏ, đám đông | <20 px không vẽ box; vùng dày không tách được → một `ignore_region` (2A202603191, 2A202603103, 2A202603201, 2A202603245, 2A202603101) | Theo R-03, R-04 | Khớp `min_box_height_px = 20` trong taxonomy |
| 6 | Tủ đồ chơi img_038 | Không gán, không ignore (2A202603191, 2A202603103, 2A202603201); một `ignore_region` bao tủ (2A202603101, 2A202603245) | Mỗi xe mô hình/đồ chơi cao ≥ 20 px: vẽ box theo lớp xe + `drivable=no`; nhỏ hơn 20 px bỏ qua, không ignore | Leader 2A202603191 quyết mở rộng #15 cho cả đồ chơi: mô hình cần học nhận ra hình xe nhưng biết xe đó không chạy được, lọc được bằng `drivable` |
| 7 | Xe trong gương chiếu hậu img_020 | Không gán gì (2A202603191, 2A202603103, 2A202603101, 2A202603245); `ignore_region` bao mặt gương (2A202603201) | Không gán, không ignore | Đa số 4/5 |
| 8 | Người trong xe | Xe hở gán, xe kín sau kính không gán (2A202603191, 2A202603245); gán nếu thấy rõ người qua kính (2A202603201, 2A202603103); gán người trong tuk-tuk (2A202603101) | Xe hở / khoang hở: gán `person`. Xe kín chỉ thấy qua kính: không gán | 2A202603191 và 2A202603245 trùng; người sau kính khó vẽ box nhất quán |
| 9 | Tượng ngựa trên pickup img_028 | Box không bao ngựa (2A202603191, 2A202603103); `can_review` (2A202603101, 2A202603245); chưa chốt (2A202603201) | Box `truck` chỉ bao thân xe, không bao tượng | Chốt trong họp: tượng là vật trang trí, không phải hàng chở (R-05, R-09) |
| 10 | Predator img_011 | Người hóa trang → `person` (2A202603101, 2A202603245); tượng / hình nhân vật → không gán (2A202603191, 2A202603103); chưa chốt (2A202603201) | Không gán (coi là tượng, R-05) | Hòa 2–2 giữa các bản đã quyết, leader 2A202603191 quyết: kích thước lớn hơn người thật, đứng yên như vật trưng bày nên coi là tượng |
| 11 | Xe máy kim loại img_011 | `motorcycle` (2A202603101); `motorcycle` + `can_review` (2A202603191, 2A202603245); cần họp (2A202603103); chưa chốt (2A202603201) | `motorcycle` + `drivable=no` + `can_review` | 3/3 bản đã quyết đều gán; xe điêu khắc không chạy được nên `drivable=no` theo #15 |
| 12 | Xe vàng img_024 | Xe vàng đặt trên nóc xe đen trong bảo tàng (2A202603191, 2A202603103); xe vàng trên mái tòa nhà phía xa, tiền cảnh là xe AMC Pacer cận cảnh (2A202603101, 2A202603245); chưa chốt (2A202603201) | Xe vàng → `car` + `drivable=no`; xe cổ tiền cảnh → `car` + `drivable=yes` | Chốt trong họp: thay vì tranh luận xe thật hay mô hình, ghi nhận bằng thuộc tính `drivable` (#15) |
| 13 | Ống dài img_029 | Box xe không bao ống (2A202603191, 2A202603103, 2A202603201, 2A202603245); bao cả hàng (2A202603101) | Không bao ống | Vật do người cầm, vươn xa gấp nhiều lần thân xe |
| 14 | Xe diễu hành img_019 | `car` (2A202603191, 2A202603103, 2A202603201); `car` + `can_review` (2A202603101, 2A202603245) | `car` | Có người lái, chạy thật trên đường |
| 15 | Vật hình xe không chạy được (img_011, img_024, img_027, img_038) | Bia mộ img_027: không gán (cả 5 bản); xe máy điêu khắc img_011, xe vàng img_024: gán lớp xe + `can_review` (2A202603191, 2A202603101, 2A202603245) | Thêm thuộc tính `drivable` (`yes`/`no`). Mọi vật hình xe không chạy được (cỡ thật hoặc đồ chơi, mô hình thu nhỏ) cao ≥ 20 px: vẽ box theo lớp xe + `drivable=no` | Chốt trong họp. Giữ được thông tin cho mô hình (có thể lọc khi huấn luyện) và tránh tranh luận "thật hay giả" cho từng ca (R-14) |
| 16 | Hàng chất trên xe (img_001, img_012, img_035) | Tính vào box xe (2A202603191, 2A202603201, 2A202603245, 2A202603101); box chỉ bao thân xe, không bao hàng (2A202603103) | Tính vào box xe (R-09) | Đa số 4/5; hàng chất cố định là kích thước xe chiếm trên đường |

## 1. Mục tiêu

Nhãn dùng để huấn luyện mô hình **phát hiện người và phương tiện trên ảnh 2D** cho camera giao thông ở đường phố châu Á.

- Mô hình phải tìm được mọi người và phương tiện **thật**, kể cả xe đang đỗ hay đang trưng bày.
- **Lỗi nguy hiểm nhất:** bỏ sót người hoặc xe thật ở gần, nhất là người đi bộ, người đi xe máy, xe đạp.
- **Lỗi gây nhiễu:** gán vật giả (tượng, ma-nơ-canh, đồ chơi, hình in, ảnh phản chiếu) thành vật thật, làm mô hình báo động sai. Riêng vật hình xe không chạy được (xe trưng bày, điêu khắc, bia mộ, đồ chơi, mô hình) thì vẫn gán nhưng đánh `drivable=no`, để có thể lọc hoặc dùng riêng khi huấn luyện.
- **Lỗi sai lớp:** nhầm `bus`/`truck` với `car`, `three_wheeler` với `motorcycle` làm sai ước lượng kích thước và hành vi xe.
- Ngoài phạm vi: phân đoạn, theo dõi qua nhiều khung, nhận dạng danh tính, biển số.

## 2. Lớp và thuộc tính

### 2.1 Lớp

Phép thử: *"mô hình có phải đoán ra điều này không?"* Có thì là lớp. Chỉ dùng để lọc hoặc QC thì là thuộc tính.

| Lớp | Định nghĩa (khớp `taxonomy.json`) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Xe 4 bánh chở người, thân thấp, tối đa khoảng 9 chỗ: sedan, hatchback, SUV, taxi, xe diễu hành có người lái thật. | img_015, img_023, img_040 (taxi, SUV), img_019 (xe diễu hành), xe được chở trên rơ-moóc img_002 | Pickup → `truck`; xe mô hình img_038 → `car` + `drivable=no` (R-14) |
| `truck` | Xe chở hàng có cabin tách khỏi thùng hoặc khung hàng: pickup, xe tải nhẹ, xe tải, đầu kéo kèm rơ-moóc. | img_001, img_007, img_012, img_028, img_036, img_037; đầu kéo img_002 | Van thân liền → `van`; bia mộ đá hình pickup cỡ thật img_027 → `truck` + `drivable=no` (R-14) |
| `van` | Xe thân liền một khối, mui cao dạng hộp, dài cỡ một xe con, không có cửa khách riêng kiểu xe buýt. | img_005 (van bạc), img_016 (van trắng) | SUV, MPV mui thấp → `car`; xe dài có cửa khách → `bus` |
| `bus` | Xe chở khách thân dài rõ rệt (≥ khoảng 1,5 lần xe con), có cửa lên xuống kiểu xe khách và dãy dài cửa sổ hành khách, gồm cả minibus. | img_009, img_031, img_032, img_037 (xe xanh phía sau), img_040 | Van img_005 |
| `motorcycle` | Xe 2 bánh có động cơ: xe máy, xe tay ga, mô tô, kể cả mô tô có thùng bên (sidecar). Box chỉ bao xe. | img_005, img_008, img_022 (sidecar), img_026, img_030 | Tuk-tuk → `three_wheeler`; xe đạp → `bicycle` |
| `bicycle` | Xe đạp 2 bánh không động cơ, kể cả xe đạp chở hàng rong. Box bao xe và hàng chất trên xe. | img_003, img_010, img_029 | Xe máy → `motorcycle`; xích lô → `three_wheeler` |
| `three_wheeler` | Xe 3 bánh chở người hoặc hàng: tuk-tuk, auto-rickshaw, xe lôi, xích lô. | img_004, img_016, img_035 (auto-rickshaw); img_006, img_009, img_023, img_034, img_039 (xích lô) | Mô tô sidecar img_022 → `motorcycle` |
| `person` | Người thật nhìn thấy được: đi bộ, đứng, ngồi, người lái, người ngồi sau, người trong khoang hở, người hóa trang. | img_005, img_014, img_025, img_034, tài xế xe diễu hành img_019 | Tượng img_017, ma-nơ-canh img_033, người trên billboard img_018, tranh tường img_026, linh vật img_019 → không gán |
| `ignore_region` | **Vùng** (không phải vật) chứa vật thật không vẽ box riêng được: cụm vật quá nhỏ, đám đông hoặc bãi xe dày. Bỏ sót trong vùng này không tính lỗi. | Bãi xe img_030, đám đông sau rào img_019, phần xa img_005, img_021 | Không dùng để che tượng, ảnh in, phản chiếu (R-05, R-06) |

**Vật không thuộc lớp nào** (cone, ngựa, chim, xe đẩy hàng rong, gánh hàng, biển báo, ô dù): không gán, không ignore. Người đẩy xe hàng rong vẫn gán `person`.

### 2.2 Thuộc tính

**`occluded_level`:** phần trăm diện tích vật bị **vật khác** che, ước lượng theo hình dạng đầy đủ của vật. Phần nằm ngoài mép ảnh không tính ở đây mà tính vào `truncated`.

| Mức | Ngưỡng | Ví dụ |
|---|---|---|
| `0` | che < 10% | Người đi bộ img_034, xe tải img_036 |
| `1` | 10% ≤ che < 40% | Xe máy bị người ngồi che một phần yên (img_026); xe sau song sắt thưa (img_018) |
| `2` | 40% ≤ che < 70% | Van img_005 bị đám người che; người trong khoang tuk-tuk |
| `3` | che ≥ 70%, vẫn nhận ra lớp | Xe máy hàng sau chỉ thấy đầu xe (img_008) |

Không nhận ra lớp (thường khi che > 90%) thì không gán.

**`truncated`:**
- `yes`: box chạm mép ảnh (cách mép ≤ 2 px) **và** một phần vật nằm ngoài khung.
- `no`: các trường hợp còn lại.

**`drivable`** (chỉ có nghĩa với lớp xe; `person` và `ignore_region` để mặc định `yes`):
- `yes` (mặc định): xe thật chạy được, kể cả đang đỗ hay đang trưng bày.
- `no`: vật hình xe không chạy được: xe đặt trên nóc/mái để trưng bày, xe điêu khắc, bia mộ hoặc tượng hình xe, đồ chơi và mô hình xe thu nhỏ (R-14).

**`can_review`:** tick khi không chắc lớp, không chắc ranh giới box, hoặc vật rơi vào ca có ghi `can_review` ở mục 4. Vẫn phải chọn lớp mình cho là đúng nhất. Nghi ngờ ở vùng chưa vẽ box thì ghi vào `submission/qc/image_issues.csv` (cột `image,annotator,region,note`).

## 3. Quy tắc vẽ box

Mỗi quy tắc có mã để dẫn lại trong mục 4 và trong `calibration_decisions.csv`.

| ID | Quy tắc |
|---|---|
| R-01 | Box ôm sát **phần nhìn thấy** của vật, lệch không quá ~2 px mỗi cạnh. Không đoán phần bị che (không vẽ amodal). Gương và ăng-ten gắn trên chính xe đó tính vào box; bóng đổ thì không. Vật bị vật khác chắn thành hai phần rời: một box bao cả hai phần. |
| R-02 | **Người và xe hở là các box riêng.** Mỗi người lái, mỗi người ngồi sau có một box `person` (bao cả mũ bảo hiểm và chân thấy được). Xe có một box theo lớp của nó, không cố bao người. Hai box được chồng nhau. |
| R-03 | Vật thật có box cao < 20 px thì không vẽ box. Cụm ≥ 3 vật nhỏ như vậy ở gần nhau: vẽ một `ignore_region` bao cụm. Vật nhỏ đứng lẻ: bỏ qua. |
| R-04 | Đám đông, bãi xe dày: khi ≥ 5 vật chồng nhau quá 50% và không vẽ được box riêng theo R-01, vẽ **một** `ignore_region` bao vùng đó. Vật phía trước tách được thì vẫn vẽ box riêng; `ignore_region` không đè lên box riêng quá 30% diện tích box đó. |
| R-05 | Tượng người, ma-nơ-canh, búp bê, đồ chơi hình người, hình trên billboard, tranh tường, màn hình, linh vật, nhân vật hoạt hình: **không vẽ box, không dùng ignore**. Đây là mẫu âm. Vật **hình xe** không chạy được (kể cả đồ chơi, mô hình) thì theo R-14. |
| R-06 | Ảnh phản chiếu trong gương, kính, mặt đường ướt: không vẽ box, không dùng ignore. |
| R-07 | Không gán xe chủ (ego), tức xe chứa máy ảnh, dù thấy cửa, nắp capo, kính chắn gió, cần gạt nước, gương hay mui xe. |
| R-08 | Xe chở xe: đầu kéo cộng rơ-moóc là **một** box `truck`. Mỗi xe được chở là một box `car` riêng nếu đạt R-03, `occluded_level` theo phần khung sắt che. |
| R-09 | Hàng chất hoặc chằng cố định trên xe (hàng trong thùng pickup, bó hàng trên auto-rickshaw, giỏ hàng trên xe đạp) **tính vào** box xe. Vật do người cầm hoặc vác (ống dài img_029) **không tính**. |
| R-10 | Pickup → `truck`. SUV, crossover → `car`. Sidecar tính vào box `motorcycle`. Đầu kéo đang nối rơ-moóc: một box. Xe đẩy hàng rong: không gán. |
| R-11 | Người trong xe hở hoặc khoang hở (tuk-tuk, xích lô, sidecar, xe mui trần): vẽ `person` khi thấy rõ đầu và box ≥ 20 px, `occluded_level` theo phần bị thân xe che. Người trong xe kín (car, van, bus, cabin truck), chỉ thấy qua kính: không gán. |
| R-12 | Vật nhòe hoặc ảnh tối: gán nếu vẫn nhận ra lớp, box bao phần thân đậm (bỏ vệt mờ nhạt), tick `can_review`. Không nhận ra lớp thì không gán. |
| R-13 | Ảnh không có vật đủ điều kiện vẫn phải có trong Task và file COCO, với 0 annotation. |
| R-14 | Vật hình xe không chạy được (xe đặt trên nóc/mái để trưng bày, xe điêu khắc, bia mộ/tượng hình xe, đồ chơi và mô hình xe thu nhỏ): vẽ box theo lớp xe tương ứng (R-01) và đặt `drivable=no`. Box cao < 20 px thì bỏ qua, không dùng `ignore_region`. Xe thật đang đỗ hoặc trưng bày trên mặt đất: `drivable=yes`. Không chắc vật chạy được hay không: chọn `no` và tick `can_review`. |
| R-15 **[v1.1]** | Cách ước lượng `occluded_level`: hình dung đường bao **đầy đủ** của vật, ước lượng tỉ lệ diện tích bị vật khác che (không tính phần ngoài mép ảnh). Khi phân vân giữa hai mức liền kề, **chọn mức thấp hơn**. Người ngồi trên xe máy/xe đạp: phần thân bị xe che tính là che. |
| R-16 **[v1.1]** | `truncated=yes` chỉ khi mép box cách mép ảnh **≤ 2 px** và rõ ràng có phần vật nằm ngoài khung; vật đứng sát mép nhưng còn trọn trong ảnh → `no`. |
| R-17 **[v1.1]** | `person` luôn để `drivable=yes` (mặc định). Không đổi `drivable` ở bất kỳ box `person` nào. |

## 4. Ca khó

| Ảnh | Tình huống | Rule ID | Quyết định | Lý do |
|---|---|---|---|---|
| img_001 | Pickup trước quán, khách ngồi, tranh hot dog | R-09, R-10, R-05 | `truck` bao cả hàng trên thùng; mỗi người thật ≥ 20 px một `person`; tranh không gán | Hàng chất trên thùng tính vào xe |
| img_002 | Đầu kéo chở nhiều ô tô, người đứng cạnh | R-08, R-02 | 1 `truck` (đầu kéo + rơ-moóc); mỗi ô tô đạt R-03 một `car`, occ 1–2 theo khung che; người đứng cạnh là `person` | Ô tô được chở vẫn là xe thật |
| img_004 | Hai tuk-tuk, tài xế ngồi trong, cone | R-11 | 2 `three_wheeler`; tài xế thấy rõ đầu → `person` occ 2; cone không gán | Khoang hở |
| img_005 | Người đi xe máy chồng nhau, van, xe mép trái, hậu cảnh dày | R-02, R-04 | Mỗi người tách được một `person`; xe máy bị che nhiều occ 2–3; van occ 2; xe mép trái `truncated=yes`; hậu cảnh dày → `ignore_region` | Mẫu chuẩn của R-02 |
| img_006 | Đoàn xích lô, xe máy, người ở xa | R-02, R-03, R-04 | Xích lô → `three_wheeler`; người đạp và khách → `person`; cụm xa nhỏ hoặc dày → `ignore_region` | Kiểm tra R-02 ở cự ly xa |
| img_007 | Xe tải trong kho tối | R-12 | Xe nhìn rõ mép thân → `truck`; vùng tối không chắc → `can_review` | Ảnh tối |
| img_008 | Bãi xe máy đêm, tài xế áo phản quang | R-02, R-04 | Tài xế → `person` + xe của họ; xe đầu hàng tách được thì vẽ; phần dày → `ignore_region` | Không tách được từng xe |
| img_009 | Xích lô cận cảnh, minibus phía sau | R-02, R-11 | Xích lô → `three_wheeler`; người đạp, khách → `person`; minibus → `bus` | Có cửa khách, thân dài |
| img_011 | Predator, xe máy kim loại, đám người xa | R-05, R-03, R-14 | Predator → không gán (tượng); mỗi xe máy → `motorcycle` + `drivable=no` + `can_review`; người xa < 20 px → `ignore_region` | Quyết định #10, #11, #15 |
| img_012 | Pickup chở hàng, SUV | R-09, R-10 | Pickup → `truck` bao cả khối hàng; SUV → `car` | |
| img_013 | Đường ướt ban đêm phản chiếu đèn | R-06, R-12 | Chỉ gán người, xe thật; phản chiếu không gán; góc tối không chắc → `can_review` | |
| img_014 | Hai người đi bộ, xe máy nhòe phía sau | R-12 | 2 `person`; vật nhòe nhận ra lớp → gán + `can_review` | |
| img_015 | Qua kính xe chủ mưa, xe đỏ, xe bạc cắt mép | R-01, R-07 | `car` `truncated=yes`; kính và cần gạt của xe chủ không gán | |
| img_016 | Tuk-tuk, van trắng, Vespa, người cắt mép | R-02, R-11 | `three_wheeler` ×2, `van`, `motorcycle` + `person`; người mép ảnh `truncated=yes`; tuk-tuk bị người che occ 1–2 | Che và cắt mép tính riêng |
| img_017 | Tượng người cưỡi ngựa đội cone | R-05, R-13 | 0 annotation | Tượng |
| img_018 | Người trên billboard, xe sau hàng rào | R-05, R-01 | Người in không gán; xe `car`, occ theo song sắt và xe khác che | |
| img_019 | Xe diễu hành có tài xế, linh vật chuột, đám đông sau rào | R-11, R-05, R-04 | `car` + tài xế `person`; linh vật không gán; người hàng đầu tách được → `person`; khối khán giả → `ignore_region` | Quyết định #14 |
| img_020 | Gương chiếu hậu xe chủ, xe trong gương | R-06, R-07 | 0 annotation | Quyết định #7 |
| img_021 | Đường phố đông xe máy | R-02, R-04 | Hàng gần: `person` + `motorcycle` riêng; phần xa → `ignore_region` | |
| img_022 | Mô tô sidecar, 2 người | R-10, R-02, R-11 | 1 `motorcycle` bao cả sidecar; người lái và người trong thùng → `person` | |
| img_023 | Xích lô phía trước, sedan; mui xích lô người chụp ở đáy ảnh | R-02, R-07 | `three_wheeler` + người đạp `person`; sedan `car`; mui xích lô của người chụp không gán | Ego |
| img_024 | Xe cổ tiền cảnh, xe vàng đặt trên nóc/mái | R-14, R-01 | Xe cổ → `car` `drivable=yes` (`truncated` nếu chạm mép); xe vàng → `car` `drivable=no` | Quyết định #12 |
| img_025 | Phố đi bộ, xe đẩy hàng rong | R-03, R-10 | Người ≥ 20 px → `person`; xe đẩy hàng không gán | |
| img_026 | 2 người trên 1 xe máy, tranh tường | R-02, R-05 | 2 `person`, 1 `motorcycle`; tranh không gán | |
| img_027 | Bia mộ đá hình pickup cỡ thật | R-14 | 1 `truck` + `drivable=no` | Quyết định #15 |
| img_028 | Pickup cổ chở tượng ngựa, xe đỏ phía sau | R-10, R-05, R-09 | `truck` chỉ bao thân xe, không bao tượng; xe đỏ `car` occ 1 | Quyết định #9 |
| img_029 | Xe máy chở ống dài, xe đạp | R-02, R-09 | `motorcycle`, `bicycle`, `person` cho từng người; ống không tính vào box | Quyết định #13 |
| img_030 | Bãi xe máy dày, poster | R-04, R-05 | Hàng đầu tách được → `motorcycle` + `person`; phần dày → `ignore_region`; poster không gán | |
| img_031 | Phố đêm, xe máy đỗ trong bóng tối | R-12, R-04 | Xe nhìn rõ đầu/đuôi → gán; vùng quá tối không tách được → bỏ qua hoặc `ignore_region` nếu ≥ 5 vật | |
| img_032 | Xe buýt ở trạm, hàng người chờ | R-04, R-01 | `bus`; người tách được → `person`; đoạn chồng nhau → `ignore_region` | |
| img_033 | Ma-nơ-canh sau kính, phản chiếu | R-05, R-06 | Không gán ma-nơ-canh và phản chiếu; vật ở mép không chắc là thật → `can_review` | |
| img_035 | Auto-rickshaw chở hàng, nhiều người đu bám | R-09, R-11, R-02 | `three_wheeler` bao cả hàng; mỗi người thấy rõ đầu → `person` occ 2–3; vùng người chồng không tách được → `ignore_region` | |
| img_037 | Pickup có nắp thùng, xe buýt phía xa | R-10 | `truck`; xe buýt `bus` | Quyết định #3 |
| img_038 | Tủ kính xe mô hình, búp bê | R-14, R-05 | Mỗi xe mô hình cao ≥ 20 px → box theo lớp xe + `drivable=no`; xe < 20 px bỏ qua; búp bê không gán | Quyết định #6 |
| img_040 | Đại lộ: người cắt mép, taxi, tuk-tuk, SUV, bus, xe máy | R-01, R-02 | `person` `truncated=yes`; taxi `car` `truncated=yes`; `three_wheeler`, `car`, `bus`, `motorcycle` + `person` | |

## 5. QC và phiên bản

- **Ngưỡng QC** (mục `rules` trong `taxonomy.json`, không đổi): ghép box `match_iou = 0.5`; IoU box ≥ 50 px dùng 0.85, nhỏ hơn dùng 0.7; IAA đạt khi IoU trung bình ≥ 0.85 và đồng thuận lớp ≥ 95%; tỉ lệ ảnh lỗi ≤ 2% mới Release.
- **Ca mơ hồ khi gán:** tick `can_review`, hoặc ghi `image_issues.csv` nếu chưa vẽ box. Không tự sửa guideline trong Pha 3.
- **Người chốt ca mơ hồ:** cả nhóm biểu quyết trong họp; hòa thì người điều phối (2A202603191) quyết và ghi lý do. Mỗi quyết định calibration ghi một dòng trong `calibration_decisions.csv`, kèm Rule ID.
- Thay đổi làm đổi cách chọn vật, lớp, box hay thuộc tính là `semantic` (tăng phiên bản, kiểm lại cả lô). Sửa câu chữ là `editorial`.

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v0 | Bản khởi đầu của giảng viên | — |
| v1.0 | Thêm `bus`, `bicycle`, `three_wheeler`; thêm thuộc tính `drivable`; định nghĩa, ví dụ, phản ví dụ cho mọi lớp; ngưỡng % cho `occluded_level`, `truncated`; quy tắc R-01…R-14; bảng ca khó 35 ảnh | Tổng hợp 5 bản cá nhân 2A202603101, 2A202603103, 2A202603191, 2A202603201, 2A202603245 (mục 0) |
| v1.1 | Thêm R-15 (cách ước lượng `occluded_level`, phân vân chọn mức thấp hơn), R-16 (ngưỡng `truncated`), R-17 (`person` luôn `drivable=yes`); quy trình xử lý ca IAA ở mục 6 | IAA chưa đạt: 427 ca lệch `occluded_level`, 84 ca `truncated`, 88 ca `person` để `drivable=no` |

## 6. Quyết định calibration (sau Pha 4)

Áp dụng cho từng nhóm ca trong `calibration_candidates.csv`; mỗi ca một dòng trong `calibration_decisions.csv`. Người chốt: 2A202603191 (leader). Người sửa: 2A202603245. Người kiểm lại: 2A202603101.

> **Thực tế thực hiện:** nhóm chưa sửa tay trên CVAT. Nhãn cuối (`labels_calibrated.json`) lấy theo đồng thuận đa số 3 người (`consensus_draft.json`, box trung bình, lớp và thuộc tính theo đa số) và áp tự động R-17. Các ca cần xem ảnh (bỏ sót/thừa, IoU thấp, ignore, sai lớp, R-15, R-16) **chưa được sửa**, chuyển sang QC vòng 1; nếu QC ra Rework thì sửa theo bảng dưới ở vòng 2.

| Loại ca | Số ca | Quyết định | Quy tắc | Phạm vi |
|---|---|---|---|---|
| Lệch `occluded_level` | 427 | Lấy mức theo đa số người gán; nếu ba người khác nhau hoặc chỉ hai người lệch, người sửa ước lượng lại theo R-15 | R-15 | `lo` (R-15 áp cho cả lô) |
| Lệch `truncated` | 84 | Xét lại theo R-16 | R-16 | `anh` |
| Lệch `drivable` của `person` | 88 | Đặt `drivable=yes` (lỗi của một người gán, guideline đã rõ) | R-17 | `anh` |
| Lệch `drivable` của xe | 39 | Áp dụng R-14: xe thật chạy được → `yes`; xe trưng bày, điêu khắc, bia mộ, đồ chơi/mô hình → `no` | R-14 | `anh` |
| Bỏ sót / thừa box | 415 | Vật được đa số (≥ 2/3) người gán vẽ → giữ; chỉ một người vẽ → người sửa xem ảnh, giữ nếu là vật thật thuộc taxonomy, cao ≥ 20 px, không nằm trong vùng ignore hợp lệ; ngược lại xoá | R-01, R-03, R-05 | `anh` |
| IoU thấp | 318 | Chỉnh box ôm sát phần nhìn thấy | R-01 | `anh` |
| Bất đồng vùng ignore | 347 | Vẽ lại `ignore_region` chỉ ở vùng thỏa R-03/R-04, không đè quá 30% lên box riêng | R-03, R-04 | `anh` |
| Sai lớp | 45 | Chọn lớp theo định nghĩa mục 2.1 và R-10 | R-10 | `anh` |
| Box có `can_review` | 18 | Xử lý theo quy tắc liên quan của ca, bỏ cờ sau khi chốt | R-01…R-17 | `anh` |
