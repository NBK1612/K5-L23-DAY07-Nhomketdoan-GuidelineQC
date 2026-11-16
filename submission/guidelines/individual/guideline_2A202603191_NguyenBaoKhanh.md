# Guideline gán nhãn — v0.1 (bản cá nhân) — 2A202603191 - Nguyễn Bảo Khánh | Nhóm G-05_t-004_Nhóm kết đoàn

- Phiên bản: v0.1-2A202603191 (bản cá nhân, viết từ v0 sau khi xem đủ 40 ảnh)
- Ngày: 2026-11-16
- Người viết: 2A202603191 - Nguyễn Bảo Khánh
- Loại nhãn: bounding box trên ảnh đường phố, công cụ CVAT, xuất COCO 1.0

> Những câu đánh dấu **[chưa chốt, cần họp]** là chỗ tôi chưa chắc, đem ra Pha 2.

---

## 1. Mục tiêu

Nhãn dùng để huấn luyện mô hình **phát hiện người và phương tiện tham gia giao thông** cho camera giao thông / hỗ trợ lái xe ở đường phố châu Á.

- Mô hình phải tìm được **vật thật đang hoặc có thể tham gia giao thông** (người đi bộ, người lái, xe đang chạy hoặc đỗ bên đường).
- **Sai nguy hiểm nhất:** bỏ sót người hoặc xe máy ở gần (dẫn tới va chạm). Vì vậy vật gần, rõ thì **luôn** phải gán.
- **Sai gây nhiễu:** dạy mô hình coi tượng, đồ chơi, hình in, ma-nơ-canh, phản chiếu là người/xe → mô hình báo động giả. Những vật này **không gán** để chúng trở thành mẫu âm.
- Vùng mà người gán không thể gán từng vật một cách nhất quán (đám đông dày, vật quá nhỏ) dùng `ignore_region` để không tính lỗi ở đó.

## 2. Lớp và thuộc tính

### 2.1 Lớp

Phép thử: *"mô hình có phải đoán ra điều này không?"* — nếu có thì là lớp, nếu chỉ để lọc / QC thì là thuộc tính.

| Lớp | Định nghĩa | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Xe 4 bánh chở người, thân liền một khối, tối đa ~9 chỗ: sedan, hatchback, SUV, taxi, xe cổ, xe diễu hành chạy thật | img_015 (xe đỏ, xe bạc), img_023 (sedan đen), img_040 (taxi xanh, SUV đen), img_018 (xe trong bãi), img_019 (xe diễu hành vàng) | Pickup có thùng hở → `truck`; minivan thùng cao kiểu Hiace → `van`; xe mô hình đồ chơi (img_038) → không gán |
| `truck` | Xe chở hàng có **cabin tách khỏi thùng hàng** (thùng hở, thùng bạt hoặc thùng kín), gồm cả pickup, xe tải nhẹ, đầu kéo + rơ-moóc | img_001 (Suzuki thùng lửng), img_002 (xe chở ô tô), img_007 (Isuzu thùng kín, pickup), img_012, img_028, img_036 (Veryca thùng bạt), img_037 (pickup có nắp thùng) | Bia mộ hình pickup (img_027) → không gán; van thân liền → `van` |
| `van` | Xe 4 bánh **thân liền một khối, mui cao, dạng hộp**, chở người hoặc hàng (Hiace, xe 16 chỗ cỡ nhỏ) | img_005 (van bạc biển 51LD), img_016 (van trắng) | SUV/MPV mui thấp → `car`; minibus ≥ 20 chỗ có cửa khách riêng → `bus` |
| `motorcycle` | Xe 2 bánh có động cơ: xe máy, xe tay ga, mô tô, gồm cả mô tô có thùng bên (sidecar) | img_005, img_006, img_008, img_010, img_021, img_026, img_030, img_022 (sidecar) | Xe đạp → `bicycle`; tuk-tuk → `three_wheeler`; xe máy điêu khắc không chạy được → xem Ca khó img_011 |
| `person` | Người thật, đi bộ, đứng, ngồi hoặc đang lái / ngồi sau xe hở | img_014, img_034, img_029, người lái xe máy img_005 | Tượng (img_017), ma-nơ-canh (img_033), người trên biển quảng cáo (img_018, img_030), linh vật (img_019 con chuột) → không gán |
| `bicycle` *(đề xuất mới)* | Xe đạp 2 bánh không động cơ | img_006, img_029 (xe đạp người đỡ ống), img_003, img_039 | Xe máy → `motorcycle` |
| `three_wheeler` *(đề xuất mới)* | Xe 3 bánh chở người / hàng: tuk-tuk, auto-rickshaw, xích lô | img_004, img_016, img_035, img_040 (tuk-tuk phía sau), img_023 (xích lô) | Mô tô sidecar (img_022) → `motorcycle` |
| `bus` *(đề xuất mới)* | Xe khách ≥ 20 chỗ, có cửa khách bên hông: xe buýt, minibus (Hyundai County) | img_009, img_031, img_037 (xe buýt xanh), img_040 (buýt vàng phía sau) | Van 16 chỗ → `van` |
| `ignore_region` | **Vùng** (không phải vật) chứa nhiều vật thuộc lớp trên mà không thể gán từng cái nhất quán | Phần xa của img_021, hàng khán giả sau rào img_019 | Không dùng để "giấu" vật không thật (vật không thật thì không gán gì) |

**Lý do đề xuất 3 lớp mới:** 40 ảnh có rất nhiều xe đạp, tuk-tuk, xe buýt. Ép chúng vào lớp cũ (tuk-tuk → `motorcycle`, bus → `truck`) làm mỗi lớp có hai hình dạng rất khác nhau, mô hình khó học và hai người gán sẽ chọn khác nhau. **[chưa chốt, cần họp]** Nếu nhóm không muốn thêm lớp, phương án dự phòng của tôi:
- `bicycle` → không gán (không thuộc mục tiêu "phương tiện cơ giới"), người đạp xe vẫn gán `person`;
- `three_wheeler` → `motorcycle`;
- `bus` → `truck`.

**Vật không thuộc lớp nào** (ngựa, tượng, xe đẩy hàng rong, gánh hàng, cọc giao thông, ô dù): **không gán**. Chỉ khi một vùng có **≥ 5 vật** thuộc lớp trong taxonomy mà không tách được từng cái mới dùng `ignore_region`.

### 2.2 Thuộc tính

**`occluded_level`** — phần trăm diện tích vật bị **vật khác** che (ước lượng bằng mắt theo hình dạng đầy đủ của vật; phần nằm ngoài mép ảnh **không** tính vào đây mà tính vào `truncated`):

| Mức | Ngưỡng | Ví dụ |
|---|---|---|
| `0` | che < 10% | Người đi bộ img_034, xe tải img_036 |
| `1` | 10% ≤ che < 40% | Xe máy có người ngồi che một phần yên (img_026) |
| `2` | 40% ≤ che < 70% | Xe sau hàng rào song sắt img_018; van img_005 bị đám người che |
| `3` | che ≥ 70% nhưng **vẫn nhận ra lớp** | Xe máy đỗ hàng sau chỉ thấy đầu xe (img_008, img_030) |

Che > 90% hoặc không nhận ra lớp → **không gán**. Hàng rào song sắt, kính trong tính là che theo phần bị song sắt / kính mờ che thực tế (song sắt thưa ≈ 20–30%).

**`truncated`**:
- `yes`: box chạm mép ảnh (cách mép ≤ 2 px) **và** ước lượng ≥ 10% vật nằm ngoài khung ảnh.
- `no`: còn lại.

**`can_review`**: **bắt buộc** tick khi (a) không chắc lớp, (b) vật rơi vào ca ghi **[chưa chốt]** ở mục 4, (c) không chắc box (vật nhoè, tối). Không dùng `can_review` thay cho việc chọn lớp — vẫn phải chọn lớp mình cho là đúng nhất.

## 3. Quy tắc vẽ box

1. **Ôm sát phần nhìn thấy** của vật (không vẽ phần bị che theo phỏng đoán). Sai lệch cho phép ≤ 2 px mỗi cạnh với vật cao ≥ 50 px.
2. **Vật bị che:** box chỉ bao phần nhìn thấy; nếu vật bị chia thành hai phần rời (ví dụ cột đèn chắn giữa xe), một box bao cả hai phần.
3. **Vật bị cắt mép:** box kéo tới đúng mép ảnh, `truncated = yes` theo ngưỡng mục 2.2.
4. **Kích thước tối thiểu:** box cao < 20 px (`min_box_height_px`) → không gán riêng. Nhiều vật nhỏ như vậy cùng chỗ → một `ignore_region`.
5. **Người trên xe hở** (xe máy, xe đạp, tuk-tuk, xích lô, thùng pickup): **hai box riêng** — một `person` bao trọn người (cả chân thấy được), một box xe bao trọn xe. Hai người trên một xe → hai box `person`.
6. **Người trong xe kín** (car, van, bus, cabin truck) chỉ thấy qua kính: **không gán** `person`.
7. **Xe chở vật / chở xe khác:**
   - Box xe chở bao **thân xe + hàng chất cố định** trên xe (vì đó là kích thước chiếm đường): img_035, img_029 (ống dài: box xe máy **không** bao ống vì ống vươn ra rất xa và do người cầm, xem Ca khó).
   - **Phương tiện thật** được chở (ô tô trên xe chở ô tô img_002) → **thêm** box riêng theo lớp của nó, `occluded_level` theo khung sắt che.
   - Vật không thuộc lớp (tượng ngựa img_028, hàng hoá img_012) → không gán.
8. **Không gán (mẫu âm):** tượng, bia mộ, đồ chơi, mô hình, ma-nơ-canh, hình in / poster / tranh tường, phản chiếu trên kính / gương / mặt đường ướt, bóng đổ.
9. **Xe chủ (ego vehicle)** — xe của người chụp thấy trong khung (gương, nắp capo, khung cửa): **không gán**.
10. **Đám đông / xe dày đặc:** gán từng vật khi tách được box (chồng lấn giữa hai vật < 50% diện tích vật nhỏ hơn). Phần phía sau không tách được → **một** `ignore_region` bao vùng đó. Không vẽ `ignore_region` chồng lên vật đã gán riêng quá 30% diện tích vật đó.
11. **Vật nhoè (motion blur):** gán nếu vẫn nhận ra lớp; box bao **phần thân đậm** của vật, bỏ vệt mờ nhạt; tick `can_review`. Không nhận ra lớp → không gán; nhiều vật nhoè liền nhau → `ignore_region`.
12. **Ảnh tối / ngược sáng:** gán nếu nhận ra lớp; không chắc lớp → gán lớp gần đúng nhất + `can_review`.
13. **Mô tô sidecar:** **một** box `motorcycle` bao cả xe và thùng bên.
14. **Đầu kéo + rơ-moóc:** **một** box `truck` bao cả đầu kéo và rơ-moóc khi rơ-moóc đang nối.

## 4. Ca khó

| Ảnh | Tình huống | Quyết định | Lý do |
|---|---|---|---|
| img_001 | Xe Suzuki thùng lửng chở ống sắt; nhiều người ngồi quán, tranh hot dog trên biển | `truck` bao cả ống trên thùng; mỗi người thật một box `person`; tranh không gán | Thùng hở, cabin tách → truck; tranh in không phải người |
| img_002 | Xe đầu kéo Renault chở 8–9 ô tô | 1 box `truck` (đầu kéo + rơ-moóc + khung chở); mỗi ô tô nhìn rõ ≥ 30% thân một box `car` với `occluded_level` theo khung che; người đứng cạnh xe là `person` | Ô tô được chở vẫn là ô tô thật; mô hình cần nhận ra chúng |
| img_004 | Hai tuk-tuk, tài xế ngồi trong | 2 box `three_wheeler`; tài xế thấy rõ thân → `person` (xe hở); cọc giao thông không gán | Tuk-tuk không có buồng kín |
| img_005 | Đám người trên xe máy chồng lên nhau, van phía sau, xe bạc ở mép trái | Mỗi người tách được → `person`; xe máy chỉ thấy tay lái / gương ít hơn 10% → không gán; van `occluded_level 2`; ô tô mép trái `truncated yes` | Theo ngưỡng che |
| img_009 | Minibus Hyundai County | `bus` | Có cửa khách hông, ~25 chỗ. **[chưa chốt, cần họp]** nếu nhóm không thêm `bus` thì `van` |
| img_011 | Tượng / người hoá trang Predator khổng lồ; xe máy điêu khắc kim loại; đám người nhỏ phía xa | Predator: không gán (kích thước > người thật, tôi xem là tượng); xe máy điêu khắc có bánh và khung thật → `motorcycle` + `can_review`; người xa < 20 px → `ignore_region` | **[chưa chốt, cần họp]** xe máy nghệ thuật có tính là phương tiện không |
| img_012 | Pickup chở hàng; Mazda SUV; xe tải xa | Pickup → `truck` (box không gồm phần ngoài khung, `truncated yes`); SUV → `car`; xe tải xa → `truck` | Pickup = cabin tách thùng |
| img_014 | Hai người đi bộ rõ; xe máy và người nhoè mạnh phía sau | 2 `person` rõ; vật nhoè nhận ra lớp → gán + `can_review`; vệt quá nhoè → không gán | Quy tắc 11 |
| img_015 | Xe đỏ và xe bạc cắt mép; hậu cảnh xe chạy nhoè; phản chiếu trên đường ướt | Xe đỏ, xe bạc `car` `truncated yes`; xe hậu cảnh gán; phản chiếu không gán | Quy tắc 3, 8 |
| img_016 | Hai tuk-tuk, van trắng, xe Vespa, người phụ nữ cắt mép dưới-phải | `three_wheeler` x2, `van`, `motorcycle` + `person` lái; người phụ nữ `person` `truncated yes`; tuk-tuk xanh bị cô che `occluded_level 1–2` | Che + cắt mép cùng lúc vẫn theo hai thuộc tính riêng |
| img_017 | Tượng người cưỡi ngựa đội nón giao thông | Không gán gì | Tượng, không phải người thật |
| img_018 | Người trên biển quảng cáo; xe đỗ sau hàng rào song sắt | Người in không gán; mỗi xe `car` `occluded_level 1–2` theo song sắt và xe khác che | Hình in là mẫu âm |
| img_019 | Xe diễu hành Danger Mouse có biển số, người lái thật, linh vật chuột; đám đông sau rào | Xe → `car`; người lái thấy rõ (xe mui trần) → `person`; linh vật không gán; người phía trước tách được → `person`; khối khán giả phía sau → `ignore_region` | Xe chạy thật trên đường; linh vật là đồ vật |
| img_020 | Gương chiếu hậu xe chủ, có ô tô phản chiếu | Không gán gì | Xe chủ và phản chiếu đều không gán |
| img_021 | Kẹt xe máy cực dày | Hàng gần (box cao ≥ 20 px, tách được): `person` + `motorcycle` riêng; phần xa phía sau → một `ignore_region` | Quy tắc 10 |
| img_022 | Mô tô sidecar, ảnh đen trắng | 1 `motorcycle` bao cả sidecar; người lái `person` | Quy tắc 13 |
| img_023 | Xích lô có người đạp; sedan đen; xe máy xa | Xích lô → `three_wheeler`; người đạp → `person`; sedan `car` | **[chưa chốt, cần họp]** xích lô thuộc `three_wheeler` hay `bicycle` |
| img_024 | Bảo tàng xe; ô tô vàng đặt trên nóc ô tô đen | Các xe trưng bày → `car` (xe thật); xe vàng trên nóc → `car` + `can_review` | Nhìn như vỏ xe thật nhưng không chắc là mô hình. **[chưa chốt, cần họp]** |
| img_026 | 2 người trên 1 xe máy; tranh tường cảng biển | 2 `person` (box chồng nhau), 1 `motorcycle`; tranh không gán | Quy tắc 5, 8 |
| img_027 | Bia mộ hình xe pickup bằng đá | Không gán | Tượng, không phải xe |
| img_028 | Pickup cổ chở tượng ngựa; xe đỏ phía sau | Pickup → `truck` (box không bao ngựa); xe đỏ → `car` `occluded_level 1` | Ngựa là tượng, không thuộc lớp; không phải hàng chất cố định của xe |
| img_029 | Người đi xe máy vác ống dài, người đạp xe đỡ đầu kia | `motorcycle`, `bicycle`, 2 `person`; box xe **không** bao ống | Ống do người cầm, vươn ra xa gấp 3 thân xe |
| img_030 | Bãi xe máy đỗ dày; người trên poster; người giữ xe | Xe hàng trước tách được → `motorcycle`; hàng sau chồng > 50% → `ignore_region`; poster không gán; người giữ xe → `person` | Quy tắc 10 |
| img_031 | Phố đêm, đường ướt phản chiếu; ô tô, xe buýt, xe máy | Gán vật nhận ra; phản chiếu không gán; người tối không rõ → `can_review` | Quy tắc 12 |
| img_032 | Hàng người chờ xe buýt dày; xe Honda cắt mép trái | Người tách được → `person`; đoạn đứng sát chồng nhau → `ignore_region`; xe `car` `truncated yes` | Quy tắc 10 |
| img_033 | Ma-nơ-canh sau kính; phản chiếu xe tải trên kính | Không gán gì | Ma-nơ-canh và phản chiếu là mẫu âm |
| img_035 | Auto-rickshaw chở hàng cao gấp đôi thân xe | `three_wheeler`, box bao cả đống hàng; người lái `person` | Hàng cố định là kích thước chiếm đường (quy tắc 7) |
| img_037 | Pickup có nắp thùng kín (truck cap) | `truck` | Vẫn là pickup, cabin tách thùng; nắp thùng không biến nó thành van |
| img_038 | Tủ kính đầy xe mô hình đồ chơi | Không gán gì | Đồ chơi là mẫu âm |
| img_040 | Người đội mũ bảo hiểm cắt mép dưới; taxi cắt mép phải; tuk-tuk, SUV, buýt phía sau | `person` `truncated yes`; taxi `car` `truncated yes`; `three_wheeler`, `car`, `bus` | Quy tắc 3 |

### Câu hỏi đem ra họp nhóm (Pha 2)
1. Có thêm 3 lớp `bicycle`, `three_wheeler`, `bus` không? Nếu không, áp dụng phương án dự phòng ở mục 2.1.
2. Xích lô (img_023) là `three_wheeler` hay `bicycle`?
3. Xe máy điêu khắc (img_011) và xe vàng trên nóc (img_024) có tính là phương tiện thật không?
4. Box ôm **phần nhìn thấy** (như tôi đề xuất) hay ước lượng **toàn bộ vật** (amodal)? Ảnh hưởng lớn tới IoU ở img_005, img_008, img_030.
5. Ngưỡng tách đám đông: chồng lấn 50% có phù hợp không?

## 5. QC và phiên bản

- **Ngưỡng QC** (theo `taxonomy.json`, không đổi): IoU ghép box `match_iou = 0.5`; IAA đạt khi IoU trung bình ≥ 0.85 và đồng thuận lớp ≥ 95%; tỉ lệ ảnh lỗi ≤ 2% mới Release.
- **Ca mơ hồ khi gán:** tick `can_review`, ghi chú tên ảnh, đem ra calibration. Không tự sửa guideline giữa Pha 3.
- **Người chốt ca mơ hồ:** cả nhóm biểu quyết trong buổi họp; hoà thì người phụ trách guideline quyết và ghi lý do.
- Mọi thay đổi làm đổi nhãn → tăng phiên bản và thêm dòng vào bảng dưới.

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v0 | Bản khởi đầu của giảng viên | — |
| v0.1-2A202603191 | Thêm định nghĩa + ví dụ + phản ví dụ cho mọi lớp; đề xuất 3 lớp `bicycle`, `three_wheeler`, `bus` | Tuk-tuk img_004/016/035/040, xe đạp img_006/029, buýt img_009/031/037 |
| | Ngưỡng % cho `occluded_level`, `truncated`; điều kiện bắt buộc `can_review` | v0 "che ít / che nhiều / không rõ" không đo được (img_005, img_008, img_018) |
| | 14 quy tắc vẽ box: người trên xe, xe chở vật, vật không thật, xe chủ, đám đông, nhoè, sidecar, rơ-moóc | img_002, img_017, img_019, img_020, img_021, img_022, img_027, img_033, img_038 |
| | Bảng ca khó 30 ảnh + 5 câu hỏi cho họp | Toàn bộ 40 ảnh |
