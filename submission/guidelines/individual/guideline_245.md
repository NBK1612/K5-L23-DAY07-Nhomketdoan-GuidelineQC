# Guideline gán nhãn Bounding Box — v0.1 (Bản cá nhân) — Nguyễn Sỹ Kiên

- **Người thực hiện:** Nguyễn Sỹ Kiên (MSHV: 2A202603245)
- **Lớp:** K5-L2A
- **Nhóm:** G-05_t-004_Nhóm kết đoàn
- **Ngày lập:** 16/11/2026
- **Công cụ gán nhãn:** CVAT (Computer Vision Annotation Tool), xuất định dạng COCO 1.0
- **Trạng thái:** Dự thảo cá nhân (Pha 1) sau khi rà soát toàn bộ 40 ảnh `img_001.jpg` – `img_040.jpg` để chuẩn bị đưa vào phiên họp thống nhất Pha 2

> **Ghi chú cá nhân:** Các ca khó, đối tượng mơ hồ hoặc vùng bóng tối/phản chiếu chưa nhận diện chắc chắn $100\%$ được tôi gắn cờ **`can_review = true`** kèm câu hỏi cụ thể để tôi trực tiếp rà soát thủ công lại bằng mắt thường trước phiên họp nhóm.

---

## 1. Mục tiêu gán nhãn và Bối cảnh bài toán

### 1.1. Bối cảnh ứng dụng
Tập dữ liệu 40 ảnh được thu thập từ đường phố các đô thị châu Á (Việt Nam, Thái Lan, Ấn Độ, Đài Loan...) với đặc trưng giao thông hỗn hợp cao độ, mật độ phương tiện dày đặc, nhiều loại xe đặc thù (xe máy, xe ba bánh tuk-tuk, xích lô, xe bán tải, xe chở hàng cồng kềnh) cùng điều kiện ánh sáng đa dạng (ban ngày, hoàng hôn, đêm tối, mưa ướt, hầm chui).

Bộ dữ liệu phục vụ huấn luyện và đánh giá mô hình thị giác máy tính phát hiện vật thể 2D (Object Detection) thời gian thực trên camera giao thông thông minh và hệ thống hỗ trợ lái xe an toàn (ADAS Cấp độ 2+/3).

### 1.2. Phân tích rủi ro kỹ thuật (False Negatives vs False Positives vs Class Confusion)
1. **Rủi ro bỏ sót vật thể (False Negative - FN):**
   - *Mức độ nguy hiểm cao nhất:* Bỏ sót người đi bộ (người đi bộ băng qua đường, người đứng cạnh xe, người đạp xe, người đi xe máy ở cự ly gần và trung bình) có thể dẫn tới việc hệ thống phanh tự động khẩn cấp (AEB) không kích hoạt, gây tai nạn đe dọa tính mạng.
   - *Yêu cầu:* Mọi thực thể sống và phương tiện di chuyển được có chiều cao $\ge 20$ pixel phải được gán box, tuyệt đối không được bỏ sót vì lười vẽ.
2. **Rủi ro nhận diện ảo giác / Gán thừa (False Positive - FP):**
   - *Mức độ gây nhiễu nghiêm trọng:* Gán nhãn cho tượng đài người/ngựa đá (`img_017`), bia mộ tạc hình xe (`img_027`), hình người phẳng in trên biển quảng cáo billboard (`img_018`), ma-nơ-canh trong cửa hàng thời trang (`img_033`), bóng phản chiếu trên mặt đường ướt (`img_013`) hoặc đồ chơi mô hình trong tủ kính (`img_038`) sẽ dạy sai cho mô hình. Xe tự hành khi gặp các vật này trên đường sẽ kích hoạt phanh gấp vô cớ ("phanh ma" / phantom braking), gây nguy cơ xe phía sau đâm dồn toa.
   - *Quy tắc thép:* Tất cả các thực thể phi sinh học / phi phương tiện nêu trên phải được coi là **mẫu âm (negative samples)** và **KHÔNG ĐƯỢC GÁN BOX**, trừ trường hợp dùng `ignore_region` để cách ly vùng quá phức tạp.
3. **Rủi ro gán sai lớp (Class Confusion):**
   - Phân loại nhầm giữa xe tải lớn (`truck`) và xe con (`car`), hoặc giữa xe máy (`motorcycle`) và xe 3 bánh (`three_wheeler` / `tuk_tuk`) làm sai lệch dự báo động học (gia tốc, khoảng cách phanh, bán kính chuyển hướng) của bộ lập kế hoạch hành trình (Motion Planner).

---

## 2. Hệ thống Lớp đề xuất và Thuộc tính định lượng

### 2.1. Đề xuất Taxonomy cá nhân (8 Lớp đối tượng + 1 Vùng bỏ qua)

Khung phân loại ban đầu v0 của giảng viên chỉ có 5 lớp (`car`, `truck`, `van`, `motorcycle`, `person`) là **chưa đáp ứng được** thực tế 40 ảnh vì xuất hiện rất nhiều xe ba bánh (tuk-tuk, xích lô), xe buýt lớn và xe đạp. Do đó, tôi đề xuất hệ thống lớp chi tiết như sau:

| STT | Tên Lớp | Định nghĩa chuẩn xác | Ví dụ trong 40 ảnh | Phản ví dụ / Ca dễ nhầm lẫn |
|:---:|---|---|---|---|
| 1 | `car` | Xe cơ giới 4 bánh thân liền kín, chuyên chở người ($\le 9$ chỗ): sedan, hatchback, SUV, crossover, coupe, taxi. | Xe taxi sedan (`img_016`, `img_040`), SUV đen (`img_040`), xe con màu đỏ/bạc (`img_015`), xe cổ AMC Pacer (`img_024`), xe chở trên giàn rơ-moóc (`img_002`). | Xe bán tải thùng hở (`truck`), xe van hộp cao (`van`), xe mô hình đồ chơi (`img_038`), bia mộ đá (`img_027`). |
| 2 | `truck` | Xe cơ giới chuyên chở hàng hoá có cabin lái tách biệt với khoang chở hàng (thùng lửng, thùng mui bạt, thùng kín, xe bán tải/pickup, xe đầu kéo + rơ-moóc). | Xe đầu kéo Renault kéo giàn (`img_002`), xe bán tải Ford/Isuzu (`img_001`, `img_012`, `img_028`, `img_037`), xe tải nhẹ thùng lửng Veryca (`img_036`), xe tải kho bãi (`img_007`). | Xe van thân liền chở hàng bưu kiện mini (`van`), ô tô con chở đồ trên nóc (`car`). |
| 3 | `bus` *(đề xuất)* | Xe cơ giới chở khách công cộng cỡ lớn từ 16 chỗ trở lên, thân dài, có cửa lên xuống riêng biệt và các hàng ghế hành khách. | Xe buýt lớn tại trạm dừng Edmonton (`img_032`), xe buýt công cộng Bangkok (`img_040`), minibus/xe buýt xanh (`img_009`). | Xe van 16 chỗ thân hẹp (`van`), xe tải thùng kín (`truck`). |
| 4 | `van` | Xe cơ giới 4 bánh thân liền khối (monocoque), mui cao dạng hình hộp chữ nhật, chở người hoặc hàng bưu phẩm (Hiace, Transit, minivan). | Xe van trắng chở khách/hàng tại Bangkok (`img_016`), van bưu chính bãi đỗ. | Xe SUV gầm cao 7 chỗ (`car`), xe buýt cỡ lớn (`bus`), xe tải có thùng rời (`truck`). |
| 5 | `motorcycle` | Phương tiện cơ giới 2 bánh (hoặc 3 bánh dạng xe mô tô có thùng phụ / sidecar), điều khiển bằng ghi-đông, người lái ngồi trên yên xe. | Xe số Honda Wave, xe tay ga Vespa, SH (`img_005`, `img_008`, `img_014`, `img_026`, `img_030`), mô tô sidecar (`img_022`), xe máy chở hàng (`img_029`). | Xe đạp không động cơ (`bicycle`), xe tuk-tuk buồng lái hở có khung che (`three_wheeler`). |
| 6 | `three_wheeler` *(đề xuất)* | Phương tiện 3 bánh cơ giới hoặc thô sơ đặc thù châu Á dùng chở khách/hàng (gồm xe tuk-tuk, auto-rickshaw, xe lam máy, xe xích lô đạp). | Xe tuk-tuk Pattaya (`img_004`, `img_016`), Auto Rickshaw Ấn Độ (`img_035`), xe xích lô Huế (`img_006`, `img_009`, `img_023`, `img_034`, `img_039`). | Xe mô tô 2 bánh (`motorcycle`), ô tô mini 4 bánh (`car`). *Lưu ý: Nếu nhóm muốn tách xích lô đạp thành `cyclo`, tôi sẵn sàng đồng thuận ở Pha 2.* |
| 7 | `bicycle` *(đề xuất)* | Phương tiện thô sơ 2 bánh vận hành chủ yếu bằng sức đạp của con người qua bàn đạp và xích líp. | Xe đạp bán mít của tiểu thương Hà Nội (`img_003`), xe đạp chở quả phật thủ phố cổ (`img_010`). | Xe máy / xe đạp điện có tay ga vặn (`motorcycle`), xe xích lô 3 bánh (`three_wheeler`). |
| 8 | `person` | Thực thể con người sống ở mọi tư thế (đi bộ, đứng, ngồi, lái xe, đạp xe, ngồi sau phương tiện, ngồi vỉa hè). | Người đi bộ qua đường (`img_014`, `img_025`), người lái và ngồi sau xe máy (`img_005`, `img_026`), tài xế tuk-tuk (`img_004`), người đạp xích lô và du khách (`img_006`, `img_009`). | Tượng đài đồng/đá (`img_017`), ảnh người in pano 2D (`img_018`), ma-nơ-canh (`img_033`), búp bê đồ chơi (`img_038`). |
| 9 | `ignore_region` | Vùng hình chữ nhật bao phủ các khu vực tập trung quá nhiều vật thể cực nhỏ ($< 20$ px), đám đông mờ mịt ở hậu cảnh xa xôi hoặc tủ kính chứa hàng trăm mô hình gây nhiễu. | Đám đông xe máy/người ở đường chân trời (`img_005`, `img_030`), toàn bộ tủ kính trưng bày đồ chơi (`img_038`), vệt phản chiếu đáy ảnh khó tách (`img_013`). | Vật thể riêng lẻ nhìn rõ nét và có chiều cao $\ge 20$ pixel. |

---

### 2.2. Thuộc tính định lượng (Attributes)

Mỗi box đối tượng (trừ `ignore_region`) đều phải được gán đủ 3 thuộc tính:

#### 1. `occluded_level` (Mức độ che khuất bề mặt) — Select: `0`, `1`, `2`, `3`
Ước lượng tỷ lệ diện tích nhìn thấy thực tế so với hình dạng toàn phần của vật thể:
- **`0` (Không bị che khuất):** Nhìn thấy $\ge 90\%$ bề mặt vật thể (ví dụ: xe đỗ thoáng, người đi bộ không bị vật cản).
- **`1` (Che khuất nhẹ):** Bị che từ $10\%$ đến dưới $35\%$ diện tích (ví dụ: xe bị bánh xe khác che một phần lốp, người lái xe máy bị ghi-đông che phần đùi).
- **`2` (Che khuất trung bình / nặng):** Bị che từ $35\%$ đến dưới $70\%$ diện tích (ví dụ: ô tô bị xe khác chắn ngang nửa thân, người ngồi trong xe hở chỉ thấy từ ngực trở lên, xe sau rào chắn song sắt thưa).
- **`3` (Che khuất rất nặng):** Bị che $\ge 70\%$ diện tích nhưng vẫn nhận diện được lớp chắc chắn nhờ các bộ phận đặc trưng còn lộ ra (ví dụ: xe máy đỗ hàng sau chỉ thấy chỏm đầu xe và gương).
*(Lưu ý: Nếu vật bị che $> 90\%$ mà không thể phân loại chắc chắn bằng mắt thường, tuyệt đối không đoán mò -> bỏ qua không gán).*

#### 2. `truncated` (Bị cắt bởi mép ảnh) — Select: `no`, `yes`
- **`no`:** Toàn bộ đường biên của vật thể nằm trọn vẹn bên trong 4 cạnh viền của ảnh.
- **`yes`:** Vật thể nằm sát mép và bị cạnh viền ảnh cắt mất một phần thân thể (dù chỉ là đầu xe, đuôi xe hay cánh tay người).

#### 3. `can_review` (Cờ cần thảo luận / Soát xét thủ công) — Checkbox: `false`, `true`
- **`false` (Mặc định):** Người gán tự tin $100\%$ về box và nhãn theo quy tắc.
- **`true`:** Bắt buộc tick khi gặp ca khó, ranh giới mờ, đối tượng kỳ dị (Predator, xe máy điêu khắc, xe đồ chơi diễu hành, xe cõng tượng, tải chở quá khổ, ảnh bóng đêm ướt mưa) hoặc khi bản thân người gán chưa thể khẳng định chắc chắn bằng mắt thường.

---

## 3. Quy tắc vẽ Bounding Box chi tiết

1. **Nguyên tắc bao khít phần nhìn thấy (Tight Visible Bounding Box):**
   - Box phải ôm sát toàn bộ các pixel nhìn thấy được của vật thể (thân xe, gương chiếu hậu, lốp xe chạm đất, cản trước/sau, tay lái, giá chở hàng). Không vẽ box theo kiểu tưởng tượng phần bị che khuất (amodal).
   - Độ sai lệch mép box cho phép không quá $2$ pixel đối với vật có chiều cao $\ge 50$ px.
2. **Quy tắc phân tách Người và Phương tiện:**
   - **Xe máy / Xe đạp / Xích lô / Tuk-tuk hở:**
     - Gán 01 box cho phương tiện bao trọn toàn bộ thân xe và bánh xe.
     - Gán từng box `person` riêng biệt cho người lái và từng hành khách ngồi trên xe. Vùng chồng lấn (overlap) giữa box người và box xe là hoàn toàn hợp lệ và bắt buộc.
   - **Ô tô con / Xe tải cabin kín / Xe buýt:**
     - Người ngồi bên trong buồng lái kín chỉ nhìn lờ mờ qua kính chắn gió thì **KHÔNG gán** box `person`, vì kính xe và khung vỏ là một khối bao bọc.
3. **Quy tắc hàng hóa chở trên xe:**
   - Hàng hóa chất trên thùng xe tải, giá đèo hàng xe máy hoặc giỏ xe đạp (`img_010`, `img_012`, `img_036`): **Được tính gộp vào box của phương tiện đó** vì khối hàng di chuyển đồng thời và chiếm dụng không gian an toàn trên mặt đường.
   - *Trường hợp ngoại lệ (`img_029`):* Xe máy chở bó ống thép / tre dài 5 mét vươn quá xa phía trước và phía sau. Box xe máy chỉ ôm khung xe và khối hàng sát thân xe, không kéo dài cả 5 mét gây loãng box; phần còn lại gắn cờ `can_review=true` để họp nhóm chốt ranh giới.
4. **Quy tắc phương tiện chuyên chở phương tiện khác (`img_002`):**
   - Xe đầu kéo vận chuyển ô tô: Gán 01 box `truck` lớn bao trọn toàn bộ xe đầu kéo và giàn khung rơ-moóc chuyên chở.
   - Từng chiếc ô tô con thật được chở trên giàn: Nếu nhìn thấy rõ biên dạng ($\ge 20$ px), gán từng box `car` riêng với `occluded_level=1` hoặc `2` (do thanh sắt giàn xe che khuất).
5. **Quy tắc loại trừ Thực thể giả (Entity Negation Rules):**
   - **Tượng đài / Điêu khắc (`img_017`, `img_027`):** KHÔNG gán `person` cho tượng người cưỡi ngựa đá/đồng; KHÔNG gán `car`/`truck` cho bia mộ hình xe bán tải.
   - **Biển quảng cáo / Billboard 2D (`img_018`):** KHÔNG gán `person` cho hình in chân dung trên pano tấm lớn.
   - **Ma-nơ-canh (`img_033`):** KHÔNG gán `person` cho tượng ma-nơ-canh trong cửa hàng quần áo.
   - **Đồ chơi mô hình thu nhỏ (`img_038`):** KHÔNG gán từng xe mô hình hay búp bê nhỏ li ti trong tủ kính; gán 01 box `ignore_region` bao trọn cả tủ kính.
   - **Ảnh phản chiếu gương / vũng nước (`img_013`, `img_020`):** KHÔNG gán nhãn cho ảnh ảo xe cộ phản chiếu qua gương chiếu hậu hay in bóng dưới vũng nước mưa.
6. **Quy tắc xe chủ (Ego Vehicle):**
   - Các bộ phận của chính xe gắn camera chụp hình (gương chiếu hậu, mép nắp capo, kính chắn gió, cần gạt nước mưa ở `img_015`, `img_020`): **KHÔNG GÁN NHÃN**.
7. **Ngưỡng kích thước tối thiểu và Vùng bỏ qua (`ignore_region`):**
   - Vật thể có chiều cao $< 20$ pixel: Không gán box riêng lẻ.
   - Khi có cụm đám đông xe cộ hoặc người dày đặc ở phía xa chân trời (`img_005`, `img_030`) mà mắt thường không thể phân tách ranh giới rõ ràng: Vẽ 01 box `ignore_region` bao trùm toàn bộ khu vực đó để tránh phạt sai mô hình khi đánh giá.

---

## 4. Bảng phân tích chi tiết toàn bộ 40 ảnh thực tế và Các ca khó cần soát xét thủ công

> Dưới đây là bảng phân tích toàn diện 40/40 ảnh từ `img_001.jpg` đến `img_040.jpg`. Các ca có hiện tượng dị biệt, ranh giới phức tạp hoặc tôi chưa thể khẳng định chắc chắn đều được đánh dấu cờ **`can_review = true`** kèm câu hỏi cụ thể để tôi trực tiếp mở ảnh gốc soát lại bằng mắt thường.

| Mã ảnh | Hiện tượng & Chi tiết thị giác trong ảnh | Lớp đề xuất | Quyết định gán nhãn & Thuộc tính | Ca khó / Điểm cần soát xét thủ công (`can_review = true`) |
|---|---|:---:|---|---|
| `img_001.jpg` | Quán ăn ven đường lúc chạng vạng ở Pattaya; xe bán tải cũ đỗ trước quán; khách ngồi dưới dây đèn; phụ nữ xăm mình đứng cạnh xe. | `truck`, `person` | - Xe bán tải: Gán `truck`, `occluded_level=1`, `truncated=no`.<br>- Phụ nữ đứng: Gán `person`, `occluded_level=0`.<br>- Khách ngồi tại bàn: Gán từng `person` thấy rõ thân ($\ge 20$ px). | Tranh vẽ hot dog hoạt hình trên biển hiệu không gán. Cần soi kỹ các khách ngồi khuất sau cột quán. |
| `img_002.jpg` | Xe đầu kéo chuyên dụng Renault 380 chở 6-8 ô tô con trên giàn rơ-moóc 2 tầng; người đứng cạnh xe. | `truck`, `car`, `person` | - Gán 01 box `truck` bao toàn bộ đầu kéo và giàn chở.<br>- Gán từng box `car` riêng cho mỗi ô tô con trên giàn (`occluded_level=1/2`).<br>- Gán `person` cho người đứng bên lề. | **`can_review = true`:** Các ô tô trên tầng 2 bị khung sắt giàn xe che nhiều, cần kiểm tra thủ công xem có đủ độ rõ để gán từng xe không. |
| `img_003.jpg` | Người phụ nữ bán mít dạo tại Hà Nội đẩy xe đạp chở thúng mít lớn. | `person`, `bicycle` | - Gán `person` cho người bán hàng.<br>- Gán `bicycle` bao bọc cả khung xe đạp và thúng mít gắn trên giá đèo hàng. | Nếu chưa có lớp `bicycle` ở Pha 2 thì phải thảo luận xếp vào đâu (hay không gán xe đạp). |
| `img_004.jpg` | Hai xe tuk-tuk màu xanh dương đỗ cạnh nhau trên vỉa hè ban đêm ở Pattaya; cọc tiêu rào chắn. | `three_wheeler`, `person` | - Gán 02 box `three_wheeler` cho 2 xe tuk-tuk.<br>- Tài xế ngồi trong buồng lái hở: Gán `person` (`occluded_level=2`). | Cọc tiêu giao thông hình trụ không thuộc taxonomy -> không gán. |
| `img_005.jpg` | Ngã tư TP.HCM giờ cao điểm, hàng trăm xe máy chen chúc; xe van bạc và ô tô con mép trái. | `motorcycle`, `person`, `van`, `car`, `ignore_region` | - Tiền cảnh: Gán từng cặp `motorcycle` và `person` ($\ge 20$ px).<br>- Xe van bạc phía sau: `van`, `occluded_level=2`.<br>- Ô tô bạc mép trái: `car`, `truncated=yes`.<br>- Hậu cảnh xa: Vẽ `ignore_region`. | Mật độ xe máy cực lớn, cần soi kỹ thủ công để không bỏ sót người lái ở hàng xe thứ 2-3. |
| `img_006.jpg` | Đoàn xe xích lô du lịch chở khách kẹt xe tại Cố đô Huế. | `three_wheeler`, `person` | - Gán `three_wheeler` cho từng xe xích lô.<br>- Gán `person` riêng cho người đạp và từng du khách ngồi trên ghế. | Các xích lô nối đuôi che khuất nhau nhiều, cần kiểm tra kỹ toạ độ box xích lô phía sau. |
| `img_007.jpg` | Kho bãi ban đêm thiếu sáng tại Pattaya; các xe tải giao hàng đỗ trong kho tối. | `truck` | - Gán `truck` cho các xe tải nhìn rõ mép thân vỏ, `occluded_level=0/1`. | **`can_review = true`:** Ánh sáng kho rất tối, cần tăng độ sáng màn hình để kiểm tra xem có người bốc xếp hoặc xe bán tải ẩn trong góc tối không. |
| `img_008.jpg` | Chợ đêm Pattaya, nhiều xe máy đỗ và người đi xe máy mặc áo phản quang di chuyển giữa đám đông. | `motorcycle`, `person` | - Gán cặp `motorcycle` và `person` cho các xe đang chạy và xe đỗ rõ.<br>- Người đi bộ mua sắm: Gán `person` nếu $\ge 20$ px. | **`can_review = true`:** Các xe máy đỗ sát nhau trước cửa hàng bị che khuất nặng (`occluded_level=3`), cần soát xem xe nào quá mờ thì bỏ qua. |
| `img_009.jpg` | Xe xích lô du lịch tại Huế góc chụp cận; phía sau có xe buýt / minibus xanh. | `three_wheeler`, `person`, `bus` | - Xích lô cận cảnh: Gán 01 `three_wheeler`, 01 `person` lái xe, 01 `person` hành khách.<br>- Xe buýt xanh phía sau: Gán `bus`, `occluded_level=2`. | Xác nhận xe buýt phía sau là `bus` hay `van` (nhìn thấy cửa khách lớn -> ưu tiên `bus`). |
| `img_010.jpg` | Người bán quả phật thủ bằng xe đạp trên phố cổ Hà Nội; giỏ quả phật thủ đầy ắp. | `person`, `bicycle` | - Gán `person` cho người bán hàng đứng cạnh xe.<br>- Gán `bicycle` bao bọc cả xe đạp và giỏ quả phật thủ trên yên sau. | Quả phật thủ chất cao là hàng hoá gắn liền với xe, tính chung vào box `bicycle`. |
| `img_011.jpg` | Người hoá trang (cosplay) quái thú Predator đứng cạnh dàn xe mô tô độ điêu khắc kim loại kỳ dị tại lễ hội. | `person`, `motorcycle`, `ignore_region` | - Predator: Gán `person` (người mặc trang phục cosplay), tick `can_review=true`.<br>- Xe mô tô độ: Gán `motorcycle` cho từng xe, tick `can_review=true`.<br>- Đám đông nhỏ phía xa: Gom vào `ignore_region`. | **`can_review = true` (Rất khó):** Cần soát lại thủ công bằng mắt: Predator là người cosplay hay tượng composite? Dàn xe máy có biển số / động cơ thật chạy được hay chỉ là mô hình kim loại tĩnh? |
| `img_012.jpg` | Xe bán tải đỗ trên phố hẹp, thùng sau chất đầy bao tải và thùng xốp rau củ quả nhô cao; SUV phía sau. | `truck`, `car` | - Xe bán tải: Gán `truck`, box bao trọn cả đầu xe và toàn bộ khối thùng xốp hàng hoá trên thùng.<br>- SUV phía sau: Gán `car`. | Khối thùng xốp nhô cao trên thùng xe bán tải bắt buộc phải nằm trọn trong box xe tải. |
| `img_013.jpg` | Đường phố ban đêm sau mưa tại Pattaya; vỉa hè bóng loáng nước mưa phản chiếu dây đèn rực rỡ; dãy bàn ghế ngoài trời. | `person` (nếu có thật) | - **KHÔNG GÁN** bất kỳ bóng phản chiếu nào trên mặt đường ướt.<br>- Kiểm tra các vệt sáng đèn pha xem có xe thật hay chỉ là đèn đường hắt bóng. | **`can_review = true` (Rất khó):** Mặt đường loang loáng nước dễ gây ảo giác có xe đỗ. Cần soi kỹ góc tối dưới tán cọ xem có người ngồi quán hay không. |
| `img_014.jpg` | Hai người đi bộ băng qua đường giữa dòng xe máy chuyển động nhanh tại Sài Gòn (nhiều vệt nhoè chuyển động). | `person`, `motorcycle` | - Hai người đi bộ tiền cảnh: Gán 02 box `person` rõ nét.<br>- Xe máy và người lái xung quanh: Gán box ôm phần thân đậm nét, bỏ vệt mờ nhoè tia sáng. | Vật thể bị motion blur mạnh chỉ gán phần khối đặc, tick `can_review=true` nếu biên dạng nhoè quá $5$ px. |
| `img_015.jpg` | Nhìn qua kính chắn gió ô tô mờ mưa, cần gạt nước đang gạt; xe ô tô đỏ và xe bạc bị cắt mép; vệt đèn mờ. | `car` | - Ô tô đỏ mép phải: Gán `car`, `truncated=yes`.<br>- Ô tô bạc mép trái: Gán `car`, `truncated=yes`.<br>- Xe chạy mờ phía trước: Gán `car`, `occluded_level=2`. | **`can_review = true`:** Cần gạt nước và kính chắn gió của xe chụp (ego vehicle) tuyệt đối KHÔNG gán. Các xe mờ qua màn mưa cần soi xem có đạt $\ge 20$ px không. |
| `img_016.jpg` | Giao thông Bangkok: xe tuk-tuk xanh, xe van trắng, xe máy Vespa, người phụ nữ bị cắt mép góc dưới phải. | `three_wheeler`, `van`, `motorcycle`, `person` | - Xe tuk-tuk: Gán 02 `three_wheeler`.<br>- Xe van trắng: Gán `van`.<br>- Xe Vespa + người lái: Gán 01 `motorcycle` + 01 `person`.<br>- Người phụ nữ góc phải: Gán `person`, `truncated=yes`, che một phần tuk-tuk. | Cần bóc tách cẩn thận giữa người phụ nữ tiền cảnh và xe tuk-tuk bị che phía sau (`occluded_level=2`). |
| `img_017.jpg` | Tượng đài Công tước Wellington tại Glasgow cưỡi ngựa đá, đầu tượng đội nón chóp giao thông (traffic cone); chim hải âu đậu trên đỉnh. | **KHÔNG GÁN** | - **BỎ QUA HOÀN TOÀN.** Không gán `person` cho tượng người; không gán lớp động vật cho ngựa đá. | Tượng đài bằng đồng/đá tĩnh vô tri trên bệ cao, chim hải âu và nón giao thông không thuộc taxonomy. Tuyệt đối không gán nhãn! |
| `img_018.jpg` | Biển quảng cáo pano chính trị gia ngoài trời tại Praha; bãi đỗ xe ô tô phía sau hàng rào song sắt kim loại. | `car` | - **KHÔNG GÁN** hình mặt người trên biển quảng cáo 2D.<br>- Các ô tô trong bãi đỗ sau rào sắt: Gán từng box `car`, `occluded_level=1` hoặc `2` (do song sắt che). | Hình in phẳng 2D là mẫu âm. Cần kiểm tra kỹ các ô tô đỗ sau rào chắn song sắt xem xe nào quá nhỏ $< 20$ px thì bỏ qua. |
| `img_019.jpg` | Xe diễu hành đồ chơi Danger Mouse màu vàng hình chuột trong lễ diễu hành Hamleys; đám đông khán giả sau rào chắn. | `car`, `person`, `ignore_region` | - Xe diễu hành màu vàng: Gán `car`, tick `can_review=true`.<br>- Người lái / diễn viên mascot: Gán `person`.<br>- Đám đông khán giả sau hàng rào: Gán 01 box `ignore_region`. | **`can_review = true` (Khó):** Xe diễu hành có hình dáng chuột hoạt hình giả tưởng nhưng có bánh xe và động cơ chạy thật trên đường phố. Cần đưa ra họp nhóm xem gán `car` hay không gán. |
| `img_020.jpg` | Cảnh cầu vồng và xe cộ phản chiếu qua gương chiếu hậu cánh cửa ô tô; giọt nước đọng trên gương. | **KHÔNG GÁN** | - **BỎ QUA HOÀN TOÀN.** Không gán xe cộ hay cầu vồng ảo nhìn thấy trong lòng mặt gương. | Gương chiếu hậu của xe chủ (ego-vehicle) và ảnh phản chiếu quang học 2D không phải vật thể vật lý trước đầu xe. |
| `img_021.jpg` | Giao thông đường phố Việt Nam góc nhìn bao quát rộng; rất nhiều xe máy, xe tải nhẹ, người đi lại. | `motorcycle`, `person`, `truck`, `car`, `ignore_region` | - Các xe và người ở cự ly gần và trung bình ($\ge 20$ px): Gán chi tiết từng box.<br>- Đoạn đường xa tít mù ở cuối tầm mắt: Khoanh `ignore_region`. | Cần rà soát kỹ lưỡng các xe máy di chuyển ở làn giữa đường. |
| `img_022.jpg` | Xe mô tô cổ có gắn thùng phụ bên hông (sidecar) chở 2 người dưới trời nắng. | `motorcycle`, `person` | - Gán 01 box `motorcycle` bao trùm cả xe mô tô và thùng phụ sidecar.<br>- Gán 01 `person` cho người lái lái xe.<br>- Gán 01 `person` cho người ngồi trong thùng phụ. | Thùng phụ gắn cố định vào xe mô tô tạo thành một khối phương tiện duy nhất. |
| `img_023.jpg` | Nhìn từ phía sau xe xích lô du lịch tại Huế; phía trước có xe ô tô con màu đen sedan đang lưu thông. | `three_wheeler`, `person`, `car` | - Xe xích lô phía trước: Gán `three_wheeler`.<br>- Người đạp xích lô: Gán `person` (`occluded_level=1`).<br>- Ô tô sedan đen: Gán `car`, `occluded_level=0`. | Phần mui bạt của chính chiếc xích lô người chụp đang ngồi (ở đáy ảnh) không gán (ego vehicle). |
| `img_024.jpg` | Chụp cận cảnh nội thất ghế và hông xe ô tô cổ 1977 AMC Pacer wagon tại Rambler Ranch; phía xa trên mái nhà có xe trưng bày. | `car` | - Xe Pacer cận cảnh: Gán `car`, `truncated=yes`, `occluded_level=0`. | **`can_review = true` (Rất khó):** Cần soi kỹ chiếc xe màu vàng gắn trên nóc toà nhà phía xa: Đó là xe thật được đưa lên nóc làm biển hiệu hay là mô hình điêu khắc? Nếu là xe thật thì có tính là xe tham gia giao thông không? |
| `img_025.jpg` | Phố đi bộ ven biển Pattaya lúc chiều tà; người đi bộ dạo mát, tiểu thương đẩy xe hàng rong, toà nhà phía xa. | `person` | - Gán `person` cho từng người đi bộ nhìn rõ thân ($\ge 20$ px).<br>- Xe đẩy hàng rong: Không thuộc taxonomy -> không gán xe, chỉ gán người đẩy là `person`. | Các bóng người đi bộ ở xa gần bờ biển nếu $< 20$ px thì bỏ qua, không gán lắt nhắt. |
| `img_026.jpg` | Đôi nam nữ đèo nhau trên xe máy trong hầm Thủ Thiêm; ánh đèn vàng nhân tạo, vách hầm có vệt mờ. | `motorcycle`, `person` | - Gán 01 box `motorcycle` ôm trọn thân xe máy.<br>- Gán 01 box `person` cho nam thanh niên lái xe (`occluded_level=1`).<br>- Gán 01 box `person` cho cô gái ngồi sau (`occluded_level=2`). | Ánh sáng vàng đơn sắc trong hầm; cần gán khít 2 box người chồng lấn lên nhau và lên xe máy. |
| `img_027.jpg` | Bia mộ bằng đá hoa cương xám nguyên khối tạc hình xe bán tải tại nghĩa trang Oak Hill, California. | **KHÔNG GÁN** | - **BỎ QUA HOÀN TOÀN.** Tuyệt đối không gán `truck` hay `car`. | Đây là công trình điêu khắc đá tĩnh trong hoa viên nghĩa trang, không có bánh lốp hay động cơ thật. Mẫu âm chuẩn mực! |
| `img_028.jpg` | Xe bán tải Hard Times Cafe đỗ bên lề đường King Street; trên thùng xe có gắn cố định tượng một con ngựa khổng lồ. | `truck`, `person` | - Gán 01 box `truck` bao trùm xe bán tải.<br>- Người đi bộ trên vỉa hè phía sau: Gán `person` nếu $\ge 20$ px. | **`can_review = true`:** Con ngựa composite gắn trên thùng xe: Có kéo viền box `truck` lên bao cả đầu ngựa không (coi như hàng cồng kềnh), hay chỉ ôm sát mép thành thùng xe bán tải? Cần họp nhóm chốt! |
| `img_029.jpg` | Xe máy chở bó ống nhựa/thép cực dài trên đường phố; người lái bị che khuất gần hết; xe đạp bên cạnh. | `motorcycle`, `person`, `bicycle` | - Gán 01 box `motorcycle` ôm thân xe.<br>- Gán `person` cho người điều khiển xe máy (`occluded_level=2/3`).<br>- Người đi xe đạp phía xa: Gán `bicycle` + `person`. | **`can_review = true` (Rất khó):** Bó ống dài chìa ra trước sau vài mét. Tôi đề xuất box `motorcycle` KHÔNG bao toàn bộ 5m chiều dài ống để tránh làm loãng đặc trưng xe, nhưng cần soát lại bằng mắt xem có nguy hiểm va chạm không. |
| `img_030.jpg` | Biển người và xe máy dừng chờ đèn đỏ tại ngã tư Sài Gòn; mật độ dày đặc hàng trăm xe. | `motorcycle`, `person`, `ignore_region` | - Khoảng 10-15 xe máy và người lái ở hàng đầu tiên: Gán từng box chi tiết.<br>- Toàn bộ cụm xe cộ ken đặc từ hàng thứ 3 trở về sau: Gom vào 01 box `ignore_region`. | Ranh giới giữa vùng gán chi tiết và `ignore_region` phải cách nhau rõ ràng, không vẽ đè box chi tiết quá 30% diện tích. |
| `img_031.jpg` | Phố đêm Khang Định; ánh đèn neon các cửa hiệu; xe máy xếp san sát trên vỉa hè; ô tô di chuyển trên đường. | `motorcycle`, `car`, `person` | - Ô tô chạy trên đường: Gán `car`.<br>- Người đi lại trước cửa hàng: Gán `person`. | **`can_review = true`:** Hàng loạt xe máy đỗ trong bóng tối trước các mái hiên đóng cửa: Rất khó đếm chính xác số lượng xe máy. Cần soi kỹ thủ công xem xe nào nhìn rõ đầu/đuôi thì gán, chỗ quá tối mịt thì bỏ qua. |
| `img_032.jpg` | Trạm dừng xe buýt công cộng Edmonton lúc cao điểm; xe buýt lớn mở cửa; đám đông hành khách đứng chờ trên vỉa hè. | `bus`, `person` | - Xe buýt công cộng: Gán 01 box `bus` lớn, `occluded_level=1`.<br>- Đám đông hành khách đứng chờ trên vỉa hè: Gán từng box `person` ($\ge 20$ px). | Hành khách đứng chen nhau che khuất một phần thân dưới của nhau (`occluded_level=1/2`), cần vẽ cẩn thận từng người. |
| `img_033.jpg` | Tượng ma-nơ-canh mặc váy áo thời trang đứng tạo dáng trong tủ kính cửa hàng phố Buchanan, Glasgow. | **KHÔNG GÁN** | - **BỎ QUA HOÀN TOÀN.** Tuyệt đối không gán `person`. | Búp bê ma-nơ-canh vô tri trưng bày quần áo, không phải con người thật. Dạy mô hình bỏ qua để tránh phanh ma. |
| `img_034.jpg` | Đoàn xích lô du lịch rẽ qua ngã ba đường tại Huế; xe máy và người đi bộ xung quanh. | `three_wheeler`, `person`, `motorcycle` | - Gán `three_wheeler` cho từng xe xích lô.<br>- Gán `person` cho người đạp xích lô và du khách ngồi trên xe.<br>- Xe máy đi bên cạnh: Gán `motorcycle` + `person`. | Kiểm tra sự tách bạch giữa box người đạp xe phía sau và mui bạt xích lô. |
| `img_035.jpg` | Xe Auto Rickshaw tại Hyderabad chở quá tải hơn chục học sinh đu bám ở phía sau và hai bên hông. | `three_wheeler`, `person` | - Gán 01 box `three_wheeler` bao quanh toàn bộ khung xe auto-rickshaw.<br>- Gán từng box `person` cho tài xế và từng học sinh nhìn thấy thân thể (`occluded_level=2/3`). | **`can_review = true`:** Quá nhiều trẻ em đu bám chồng chéo lên nhau ở cửa sau. Cần zoom lớn soi thủ công đếm đủ số lượng đầu/thân người để gán box `person`. |
| `img_036.jpg` | Xe tải nhẹ đầu bằng CMC Veryca thùng lửng đỗ trên phố Khai Phong; cabin tách biệt thùng chở hàng. | `truck` | - Gán 01 box `truck` bao trùm toàn bộ xe tải Veryca, `occluded_level=0`, `truncated=no`. | Mẫu xe tải nhẹ kinh điển của đô thị châu Á (cabin tách thùng lửng) -> xếp chắc chắn vào `truck`. |
| `img_037.jpg` | Xe bán tải công vụ GO Transit lắp nắp chụp thùng cao Master 75 Truck Cap đỗ cạnh lề đường; xe buýt xanh phía xa. | `truck` (hoặc `car`), `bus` | - Xe bán tải lắp nắp thùng cao: Gán `truck`, `occluded_level=0`.<br>- Xe buýt GO Transit xanh phía xa: Gán `bus`. | **`can_review = true`:** Xe bán tải nhưng có nắp đậy thùng hàng cao ngang nóc cabin nhìn giống SUV/van. Cá nhân tôi phân loại là `truck` (bản chất khung gầm pickup), cần đưa ra họp nhóm chốt thống nhất. |
| `img_038.jpg` | Tủ kính cửa hàng đồ chơi ở Munich trưng bày hàng trăm mô hình xe hơi thu nhỏ, búp bê, gấu bông. | `ignore_region` | - Gán 01 box `ignore_region` bao trùm toàn bộ diện tích tủ kính trưng bày đồ chơi.<br>- **KHÔNG GÁN** từng mô hình xe hay búp bê. | Đồ chơi mô hình thu nhỏ li ti, không được gán `car` hay `person`. Dùng `ignore_region` để bảo vệ mô hình không bị phạt điểm. |
| `img_039.jpg` | Đoàn xích lô du lịch nối đuôi nhau di chuyển trên đường rợp bóng cây tại Huế. | `three_wheeler`, `person` | - Gán `three_wheeler` cho từng chiếc xích lô.<br>- Gán `person` riêng cho tài xế đạp xe và du khách. | Các xe xích lô nối đuôi thẳng hàng, xe sau che khuất một phần đuôi xe trước (`occluded_level=1`). |
| `img_040.jpg` | Giao thông đại lộ trung tâm Bangkok: xe buýt công cộng, taxi sedan màu hồng/xanh, xe tuk-tuk, xe máy chen chúc dưới cầu đường sắt trên cao. | `bus`, `car`, `three_wheeler`, `motorcycle`, `person` | - Các xe buýt lớn: Gán `bus`.<br>- Xe taxi sedan: Gán `car`.<br>- Xe tuk-tuk len lỏi: Gán `three_wheeler`.<br>- Xe máy và người lái: Gán `motorcycle` và `person`. | Bối cảnh giao thông hỗn hợp đa phương tiện phức tạp nhất. Cần soi kỹ các xe ở làn giữa bị che khuất một phần thân. |

---

## 5. Quy trình Kiểm soát chất lượng (QC) và Lịch sử phiên bản

### 5.1. Các ngưỡng kỹ thuật theo quy định bài lab (Không thay đổi)
- **Kích thước tối thiểu:** Đối tượng có chiều cao box $< 20$ pixel (`min_box_height_px = 20`) không gán box riêng lẻ, chỉ gom vào `ignore_region` nếu là cụm đông.
- **Ngưỡng IoU ghép cặp:**
  - Box có kích thước lớn (chiều cao $\ge 50$ px): IoU yêu cầu $\ge 0.85$.
  - Box nhỏ ($20 \le \text{height} < 50$ px): IoU yêu cầu $\ge 0.70$.
  - Ngưỡng IoU tối thiểu để coi là ghép đôi thành công: `match_iou = 0.50`.
- **Chỉ số đồng thuận liên gán nhãn (IAA):**
  - IoU trung bình trên toàn bộ các cặp box ghép đôi: $\ge 0.85$.
  - Độ đồng thuận về phân loại lớp (Class Agreement): $\ge 95\%$.
- **Ngưỡng nghiệm thu xuất xưởng (Release Criteria):**
  - Tỷ lệ ảnh có lỗi $\le 2.0\%$ (`error_rate_img_max = 0.02`).
  - Không còn bất kỳ lỗi nhận diện lặp lại mang tính hệ thống.

### 5.2. Lịch sử thay đổi và Tiến trình hoàn thiện

| Phiên bản | Tác giả | Tóm tắt các nội dung thay đổi chính | Căn cứ & Mục tiêu kỹ thuật |
|:---:|---|---|---|
| **v0.0** | Giảng viên | Bản khung sơ khai gồm 5 lớp cơ bản (`car`, `truck`, `van`, `motorcycle`, `person`), thiếu quy tắc xử lý thực thể giả và xe đặc thù châu Á. | Bản gốc ban đầu của repo thực hành. |
| **v0.1-2A202603245** | Nguyễn Sỹ Kiên | 1. **Đề xuất mở rộng taxonomy lên 8 lớp + 1 vùng bỏ qua:** bổ sung `three_wheeler` (bao gồm tuk-tuk và xích lô), `bus`, `bicycle` để phản ánh đúng thực tế 40 ảnh giao thông châu Á.<br>2. Định lượng hoá 4 mức `occluded_level` ($0, 1, 2, 3$) dựa trên tỷ lệ phần trăm diện tích che khuất bề mặt.<br>3. Ban hành quy tắc phân tách chặt chẽ giữa Người và Phương tiện (buồng lái hở vs buồng lái kín).<br>4. Thiết lập quy tắc loại trừ triệt để thực thể giả: tượng đài đá, ma-nơ-canh, biển pano 2D, mô hình đồ chơi, hình ảnh phản chiếu gương chiếu hậu.<br>5. Bảng phân tích chi tiết toàn diện 40/40 ảnh trong bộ dữ liệu, chỉ rõ các ca khó dị biệt (Predator, xe máy điêu khắc, đường ướt phản chiếu đêm, xe chở hàng quá khổ, xe cõng tượng ngựa...) và gắn cờ `can_review=true` để tự rà soát thủ công trước khi họp Pha 2. | Khảo sát thực tế toàn bộ 40 ảnh `img_001` - `img_040`, chuẩn bị cơ sở dữ liệu và lý lẽ kỹ thuật vững chắc cho phiên họp thống nhất Pha 2 của nhóm. |

