# Guideline gán nhãn phát hiện đối tượng đường phố — v0.1 (Bản cá nhân mở rộng)

- **Người thực hiện:** Nguyễn Xuân Anh (MSHV: 03101)
- **Lớp:** K5-L2A
- **Nhóm:** G-05_t-004_Nhóm kết đoàn
- **Ngày lập:** 16/11/2026
- **Trạng thái:** Bản dự thảo cá nhân mở rộng Taxonomy (Pha 1) để đề xuất trong phiên họp nhóm Pha 2

---

## 1. Mục tiêu

Tập dữ liệu phục vụ huấn luyện mô hình học sâu phát hiện vật thể (Object Detection) 2D thời gian thực trên camera hành trình và camera giao thông thông minh tại các đô thị châu Á (thuộc hệ thống ADAS / xe tự lái cấp độ 2+/3).

* **Bối cảnh giao thông châu Á:** Khác với chuẩn giao thông phương Tây (chủ yếu là ô tô con và xe tải lớn), đường phố châu Á (Việt Nam, Thái Lan, Ấn Độ...) có mật độ phương tiện hỗn hợp cực kỳ phức tạp: xe máy áp đảo, xe lôi 3 bánh, xích lô, xe tuk-tuk, xe bán hàng rong và người đi bộ đan xen dày đặc.
* **Quan điểm cá nhân về Taxonomy:** Bản khởi đầu v0 của giảng viên chỉ có 5 lớp là **quá thiếu hụt**, ép buộc các phương tiện có hành vi động học và hình thái hoàn toàn khác biệt (như xe buýt, tuk-tuk, xe đạp, xích lô) vào các lớp chung chung, dẫn đến suy giảm độ chính xác của hệ thống nhận thức. Do đó, bản cá nhân này **đề xuất mở rộng taxonomy lên 9 lớp đối tượng chuyên biệt** để phản ánh đúng thực tế.
* **Hậu quả nếu nhãn sai:**
  * *Bỏ sót (False Negative):* Bỏ sót người đi bộ, người đi xe đạp hoặc xe máy đang cắt ngang đầu xe sẽ khiến hệ thống phanh khẩn cấp (AEB) không kích hoạt, dẫn tới tai nạn nghiêm trọng.
  * *Báo động giả / Gán nhầm (False Positive):* Nhận diện nhầm ma-nơ-canh, tượng đài, tranh ảnh quảng cáo 2D hoặc hình phản chiếu qua gương thành thực thể sống sẽ gây hiện tượng "phanh ma" (phantom braking) làm xe sau đâm dồn toa.
  * *Gán sai lớp (Class Confusion):* Nhầm lẫn giữa `bus`/`truck` và `car`, hoặc giữa `motorcycle` và `tuk_tuk`/`bicycle` sẽ khiến mô hình dự đoán sai vận tốc cực đại, bán kính quay vòng và khoảng cách an toàn cần thiết.

---

## 2. Hệ thống Lớp mở rộng và Thuộc tính

### 2.1. Danh mục 9 Lớp đối tượng + 1 Vùng bỏ qua

Mỗi vật thể thực trên đường thuộc đúng **một lớp duy nhất**.

| STT | Lớp | Định nghĩa (1 câu) | Ví dụ trong 40 ảnh | Phản ví dụ / Dễ nhầm lẫn |
|:---:|---|---|---|---|
| 1 | `car` | Phương tiện cơ giới 4 bánh chở người cỡ nhỏ/trung, gồm sedan, hatchback, SUV, crossover, coupe và xe bán tải (pickup truck) gia đình. | Xe taxi sedan, Toyota Vios, xe bán tải Ford Ranger đỗ trước quán (`img_001`), xe bán tải chở rau (`img_012`), xe bán tải có nắp thùng (`img_037`). | Xe thùng kín chở hàng (`van`), xe tải thùng chuyên dụng (`truck`), xe buýt (`bus`), mô hình xe đồ chơi (`img_038`), tượng đá tạc hình xe (`img_027`). |
| 2 | `truck` | Xe cơ giới chuyên dụng chở hàng hóa nặng, có cabin lái tách biệt với thùng hàng lớn (thùng lửng, thùng mui bạt, thùng đông lạnh, xe ben, xe đầu kéo kéo rơ-moóc). | Xe đầu kéo chuyên dụng chở ô tô con Renault (`img_002`), xe tải giao hàng trong kho tối Pattaya (`img_007`), xe tải nhẹ thùng lửng CMC Veryca (`img_036`). | Xe bán tải gia đình có thùng hở (`car`), xe tải van nhỏ liền khối (`van`), xe buýt chở khách (`bus`). |
| 3 | `bus` | Xe cơ giới chở khách công cộng cỡ lớn từ 16 chỗ ngồi trở lên, thân dài, có cửa lên xuống cho hành khách và các hàng ghế ngang/dọc. | Xe buýt công cộng đón trả khách tại trạm dừng giờ cao điểm (`img_032`), các xe buýt lớn lưu thông trên đại lộ Bangkok (`img_040`). | Xe tải thùng kín (`truck`), xe van 9–16 chỗ chở khách mini (`van`). |
| 4 | `van` | Xe tải nhẹ hoặc xe khách mini có thiết kế thân vỏ liền khối (monocoque/unibody), khoang chở hàng kín hoặc chở khách 9–16 chỗ liền khoang với cabin. | Xe van chở bưu kiện, Ford Transit, Mercedes Sprinter, xe giao hàng mini thân kín. | Xe SUV 7 chỗ (`car`), xe buýt lớn (`bus`), xe tải nhẹ có thùng rời (`truck`). |
| 5 | `motorcycle` | Phương tiện cơ giới 2 hoặc 3 bánh chạy bằng động cơ đốt trong hoặc động cơ điện, người lái ngồi trên yên và điều khiển bằng ghi-đông, bao gồm cả xe mô tô có thuyền phụ (sidecar). | Xe máy số Honda Wave, xe tay ga SH, Vespa (`img_005`, `img_008`, `img_014`, `img_026`, `img_030`), mô tô độ phân khối lớn (`img_011`), xe máy chở hàng cồng kềnh (`img_029`), mô tô sidecar (`img_022`). | Xe tuk-tuk/auto-rickshaw 3 bánh có cabin bao bọc (`tuk_tuk`), xe đạp cơ học (`bicycle`), xe xích lô đạp chân (`cyclo`). |
| 6 | `tuk_tuk` | Phương tiện cơ giới 3 bánh đặc thù châu Á (tuk-tuk, auto-rickshaw, xe lam, xe lôi máy) có kết cấu buồng lái hở/kín, tay lái ghi-đông hoặc vô-lăng, có mui che và khoang chở khách/hàng phía sau. | Xe tuk-tuk xanh đỗ bên vỉa hè Pattaya (`img_004`), xe Auto Rickshaw chở quá tải tại Hyderabad (`img_035`). | Xe máy 2 bánh (`motorcycle`), xe xích lô đạp chân không động cơ (`cyclo`), xe ô tô con mini (`car`). |
| 7 | `bicycle` | Phương tiện thô sơ 2 bánh vận hành hoàn toàn hoặc chủ yếu bằng sức người đạp qua bàn đạp (pedal), bao gồm xe đạp chở hàng rong của tiểu thương. | Xe đạp chở mít của người phụ nữ bán rong Hà Nội (`img_003`), xe đạp chở quả phật thủ trên phố cổ (`img_010`). | Xe máy / xe gắn máy có động cơ (`motorcycle`), xe xích lô 3 bánh (`cyclo`). |
| 8 | `cyclo` | Phương tiện thô sơ 3 bánh vận hành bằng bàn đạp sức người, có ghế ngồi chở khách hoặc thùng chở hàng ở phía trước và người đạp ngồi lái phía sau (xích lô du lịch). | Đoàn xích lô du lịch chở khách tại Cố đô Huế (`img_006`, `img_009`, `img_023`, `img_034`, `img_039`). | Xe tuk-tuk cơ giới (`tuk_tuk`), xe đạp 2 bánh (`bicycle`), xe ba gác máy. |
| 9 | `person` | Con người sống ở mọi tư thế (đi bộ, đứng, ngồi, nằm, ngồi xổm) hoặc người đang điều khiển / ngồi trên bất kỳ phương tiện giao thông nào. | Người đi bộ qua đường (`img_014`, `img_025`), người lái xe máy (`img_005`, `img_026`), người đạp xích lô (`img_006`), người bán hàng rong (`img_003`), người cosplay Predator (`img_011`). | Tượng đài người cưỡi ngựa (`img_017`), hình ảnh người in trên billboard tranh cử (`img_018`), ma-nơ-canh cửa hàng thời trang (`img_033`), búp bê đồ chơi (`img_038`). |
| 10 | `ignore_region` | Vùng đệm chữ nhật bao trùm các khu vực tập trung quá nhiều vật thể nhỏ mờ, đám đông xa xôi, hoặc các đối tượng gây nhiễu để không tính lỗi khi đo đạc và huấn luyện mô hình. | Đám đông xe cộ/người mờ mịt ở chân trời (`img_005`, `img_030`), tủ kính trưng bày đồ chơi mô hình (`img_038`), vệt phản chiếu đáy ảnh. | Vật thể riêng lẻ nhìn rõ nét và có chiều cao $\ge 20$ pixel. |

---

### 2.2. Thuộc tính định lượng (Attributes)

Áp dụng cho tất cả 9 lớp vật thể:

#### 1. `occluded_level` (Mức độ che khuất) — Select [0, 1, 2, 3]
* **`0` (Không che):** Nhìn thấy $\ge 95\%$ bề mặt vật thể.
* **`1` (Che nhẹ):** Bị che khuất từ $5\%$ đến $30\%$ diện tích (ví dụ: bánh xe bị vỉa hè che, chân người bị bánh xe che).
* **`2` (Che trung bình / nặng):** Bị che từ $> 30\%$ đến $70\%$ diện tích (ví dụ: người lái xe máy chỉ thấy từ thắt lưng trở lên, ô tô bị xe khác chắn nửa thân).
* **`3` (Che rất nặng):** Bị che $> 70\%$ diện tích, chỉ nhìn thấy một phần nhỏ đặc trưng nhưng vẫn nhận dạng chắc chắn nhờ ngữ cảnh.

#### 2. `truncated` (Bị cắt bởi mép ảnh) — Select [no, yes]
* **`no`:** Toàn bộ biên dạng vật thể nằm trọn bên trong 4 cạnh của khung hình.
* **`yes`:** Vật thể chạm hoặc bị cắt ngang bởi ít nhất một mép ảnh (trên, dưới, trái, phải).

#### 3. `can_review` (Cờ cần thảo luận lại) — Checkbox [false, true]
* **`false`:** Người gán tự tin $100\%$ vào quyết định gán nhãn theo guideline.
* **`true`:** Đánh dấu khi gặp trường hợp lưỡng lự, dị biệt, ranh giới chưa rõ để đưa ra thảo luận tại buổi họp nhóm / calibration.

---

## 3. Quy tắc vẽ Bounding Box chi tiết

1. **Nguyên tắc bao khít phần nhìn thấy (Tight Visible Box):**
   * Box phải ôm sát toàn bộ các pixel nhìn thấy được của vật thể (thân xe, bánh xe, gương chiếu hậu, đèn pha, cản trước/sau; đầu, thân, tay chân của người).
   * Tuyệt đối không vẽ box quá rộng (chứa thừa khoảng trống mặt đường) hoặc quá hẹp (cắt mất gương, bánh xe).
   * Không bao gồm ăng-ten siêu mỏng hoặc cờ nhỏ cắm trên xe nếu làm tăng diện tích box một cách bất thường.
2. **Quy tắc phân tách Con người và Phương tiện (Person vs Vehicle Interaction):**
   * **Xe máy (`motorcycle`):** Gán 01 box `motorcycle` cho khung xe máy (từ lốp trước đến lốp sau). Gán riêng từng box `person` cho người lái và từng hành khách ngồi sau. Box người lái và box xe máy chồng lấn lên nhau (overlap) là điều bắt buộc.
   * **Xe đạp (`bicycle`):** Gán 01 box `bicycle` cho khung xe đạp; gán 01 box `person` cho người đạp xe.
   * **Xích lô (`cyclo`):** Gán 01 box `cyclo` bao trọn toàn bộ kết cấu xe xích lô (bánh trước, ghế khách, khung sườn, bàn đạp, bánh sau); gán các box `person` riêng cho người đạp và từng du khách ngồi trên ghế.
   * **Xe Tuk-tuk (`tuk_tuk`):** Gán 01 box `tuk_tuk` bao trọn toàn bộ xe. Người lái và hành khách ngồi bên trong cabin hở: nếu nhìn rõ từ ngực/đầu trở lên thì gán các box `person` (`occluded_level=2` hoặc `3`).
3. **Quy tắc hàng hóa chở trên xe:**
   * Hàng hóa chất trên giá chở hàng, thùng xe tải, hoặc chằng sau xe máy (`img_012`, `img_029`): **Tính chung vào box của phương tiện đó**, vì khối hàng di chuyển cùng phương tiện và là một phần kích thước vật lý mà hệ thống tự lái phải né tránh.
4. **Quy tắc phương tiện chở phương tiện khác (`img_002`):**
   * Đối với xe đầu kéo vận chuyển ô tô: Gán 01 box `truck` lớn bao trọn toàn bộ xe đầu kéo và giàn rơ-moóc chuyên chở.
   * Đối với từng chiếc ô tô con được chở trên giàn xe: Gán từng box `car` riêng lẻ nếu nhìn thấy rõ kết cấu và chiều cao $\ge 20$ px; đánh dấu `can_review=true` để thảo luận nhóm xem có coi chúng là tải trọng hay đối tượng phát hiện độc lập.
5. **Quy tắc loại trừ Thực thể giả (Entity Negation Rules):**
   * **Tượng đài / Điêu khắc (`img_017`, `img_027`):** KHÔNG gán nhãn `person` cho tượng người cưỡi ngựa bằng đồng/đá; KHÔNG gán nhãn `car`/`truck` cho bia mộ đá tạc hình xe bán tải.
   * **Tranh ảnh, Pano, Billboard 2D (`img_018`):** KHÔNG gán nhãn `person` cho chân dung in phẳng trên biển quảng cáo.
   * **Ma-nơ-canh cửa hiệu (`img_033`):** KHÔNG gán nhãn `person`.
   * **Đồ chơi / Mô hình thu nhỏ (`img_038`):** KHÔNG gán box đơn lẻ cho xe mô hình hay búp bê trong tủ kính; khoanh 01 vùng `ignore_region` bao trùm toàn bộ tủ kính.
   * **Hình ảnh phản chiếu gương / bóng nước (`img_013`, `img_020`):** KHÔNG gán nhãn cho ảnh xe ảo nhìn qua gương chiếu hậu hoặc bóng xe dưới vũng nước.
6. **Ngưỡng kích thước tối thiểu và Vùng bỏ qua:**
   * Bất kỳ vật thể nào có chiều cao $< 20$ pixel: **Không gán box đơn lẻ**.
   * Nếu có một đám đông người/xe tập trung ở hậu cảnh xa xôi mờ ảo (`img_005`, `img_030`), vẽ 01 box `ignore_region` bao bọc lấy toàn bộ khu vực đó.

---

## 4. Bảng phân tích Ca khó trong 40 ảnh thực tế (Kèm đề xuất Taxonomy mới)

| Ảnh | Hiện tượng trong ảnh | Lớp đề xuất | Quyết định chi tiết & Thuộc tính | Lý do kỹ thuật & Lợi ích của Taxonomy mở rộng |
|---|---|:---:|---|---|
| `img_001.jpg` | Xe bán tải cũ đỗ trước quán ăn bên đường. | `car` | Gán `car`, `occluded_level=0`, `truncated=no`. | Xe bán tải đô thị phục vụ di chuyển cá nhân, chuẩn ADAS xếp vào `car`. |
| `img_002.jpg` | Xe đầu kéo Renault chở 6 ô tô con trên giàn vận chuyển. | `truck` + `car` | Gán `truck` cho xe vận chuyển. Các ô tô con trên giàn: Gán từng box `car` riêng, tick `can_review=true`. | Xe chở hàng là `truck`. Các ô tô con trên giàn mang đầy đủ đặc trưng thị giác của xe con, cần gán để thử nghiệm độ phân rã của mô hình. |
| `img_003.jpg` | Người phụ nữ gánh hàng/đẩy xe đạp chở mít tại Hà Nội. | `person` + `bicycle` | Gán `person` cho người bán; gán `bicycle` cho xe đạp chở mít. | Taxonomy có lớp `bicycle` giúp nhận diện chính xác phương tiện thô sơ phổ biến này thay vì bỏ sót. |
| `img_004.jpg` | Hai xe tuk-tuk xanh đỗ bên vỉa hè Pattaya. | `tuk_tuk` | Gán `tuk_tuk` cho từng xe, `occluded_level=0`. | Có lớp `tuk_tuk` giải quyết dứt điểm tranh cãi xe này là `car` hay `motorcycle`. |
| `img_005.jpg` | Dòng xe máy ken đặc giờ cao điểm tại TP.HCM. | `motorcycle` + `person` + `ignore_region` | Gán cặp box `person` và `motorcycle` cho các xe ở cự ly gần/trung bình ($\ge 20$ px). Vùng xa mờ ảo vẽ `ignore_region`. | Xử lý đám đông chuẩn mực: tiền cảnh gán chi tiết, hậu cảnh cách ly bằng ignore region. |
| `img_006.jpg` | Đoàn xích lô du lịch chở khách tại Huế. | `cyclo` + `person` | Gán `cyclo` cho khung xe xích lô; gán `person` cho người đạp xe và khách ngồi trên ghế. | Phân loại chính xác phương tiện 3 bánh chở khách không động cơ (`cyclo`) thay vì bỏ lửng. |
| `img_007.jpg` | Xe tải giao hàng trong kho thiếu sáng tại Pattaya. | `truck` | Gán `truck` cho các xe tải nhìn rõ mép thân xe, `occluded_level=0/1`. | Nhận diện xe tải vận tải hàng hóa trong điều kiện ánh sáng yếu. |
| `img_008.jpg` | Người đi xe máy mặc áo phản quang trong chợ đêm. | `motorcycle` + `person` | Gán box `motorcycle` và box `person` cho từng người lái. | Kiểm tra khả năng nhận diện người điều khiển xe mặc trang phục bảo hộ/phản quang ban đêm. |
| `img_009.jpg` | Xích lô du lịch chở khách tại Huế (góc chụp cận). | `cyclo` + `person` | Gán 01 `cyclo`, 01 `person` (người đạp, `occluded_level=1`), 01 `person` (du khách, `occluded_level=1`). | Bóc tách rõ ràng giữa phương tiện và người vận hành/hành khách. |
| `img_010.jpg` | Người bán quả phật thủ bằng xe đạp tại phố cổ Hà Nội. | `person` + `bicycle` | Gán `person` cho người bán; gán `bicycle` bao bọc cả xe đạp và giỏ quả phật thủ. | Hàng hóa gắn liền với xe đạp được tính chung vào kích thước của xe đạp. |
| `img_011.jpg` | Cosplay Predator đứng cạnh dàn xe mô tô độ quái dị. | `person` + `motorcycle` | Gán `person` cho người mặc đồ Predator; gán `motorcycle` cho từng chiếc mô tô độ hầm hố. | Người hóa trang vẫn là con người sống (`person`). Xe mô tô dù hình thù kỳ dị vẫn là `motorcycle`. |
| `img_012.jpg` | Xe bán tải chở đầy thùng xốp rau củ quả ở thùng sau. | `car` | Gán 01 box `car` bao bọc cả đầu xe và toàn bộ khối thùng xốp trên thùng hàng. | Kích thước an toàn giao thông phải tính toàn bộ khối hàng nhô lên. |
| `img_013.jpg` | Đường ướt sau mưa ban đêm, vũng nước phản chiếu đèn. | Không gán bóng | Chỉ gán người và xe thật trên đường. Tuyệt đối không gán bóng phản chiếu trên mặt nước. | Chống lỗi nhận diện ảo giác (False Positive). |
| `img_014.jpg` | Người đi bộ băng cắt qua đường giữa dòng xe máy Sài Gòn. | `person` + `motorcycle` | Gán `person` cho người đi bộ (`occluded_level=1`), gán xe máy và người lái xung quanh. | Tình huống người đi bộ cắt ngang đường (VRU) cực kỳ quan trọng cho hệ thống phanh khẩn cấp AEB. |
| `img_015.jpg` | Kính chắn gió mờ mưa, cần gạt nước gạt nước mưa. | `car` | Gán `car` cho xe con mờ phía trước nếu nhìn rõ đèn đuôi ($\ge 20$ px), `occluded_level=1`. | Đánh giá độ chịu nhiễu thời tiết của cảm biến quang học. |
| `img_016.jpg` | Giao thông Bangkok: taxi sedan, xe buýt, tuk-tuk đan xen. | `car` + `bus` + `tuk_tuk` | Gán taxi là `car`, xe buýt là `bus`, tuk-tuk là `tuk_tuk`. | Minh chứng rõ nét nhất cho việc cần taxonomy mở rộng: 3 loại xe khác biệt hoàn toàn về kích cỡ. |
| `img_017.jpg` | Tượng công tước Wellington đội nón giao thông cưỡi ngựa đá. | **KHÔNG GÁN** | Bỏ qua hoàn toàn, không gán `person`. | Tượng điêu khắc tĩnh vô tri trên bệ cao, không tham gia giao thông. |
| `img_018.jpg` | Biển quảng cáo pano ngoài trời in hình mặt chính trị gia. | **KHÔNG GÁN** | Bỏ qua hoàn toàn, không gán `person`. | Hình ảnh in 2D phẳng; nếu gán sẽ gây lỗi phanh gấp vô căn cứ cho xe tự hành. |
| `img_019.jpg` | Xe mô hình Danger Mouse trong lễ diễu hành đồ chơi Hamleys. | `car` (hoặc `can_review`) | Gán `car`, tick `can_review=true` để thảo luận nhóm. | Xe có bánh, có tài xế điều khiển chạy trên đường nhưng ngoại hình giả tưởng. |
| `img_020.jpg` | Cảnh cầu vồng và xe cộ phản chiếu qua gương chiếu hậu ô tô. | **KHÔNG GÁN** | Bỏ qua hoàn toàn các xe ảo nhìn thấy trong lòng gương chiếu hậu. | Ảnh ảo quang học 2D phản chiếu không nằm ở toạ độ vật lý thực phía trước xe. |
| `img_021.jpg` | Giao thông đường phố Việt Nam góc nhìn rộng. | Đa lớp | Gán các xe và người đạt $\ge 20$ px theo đúng lớp. | Bối cảnh giao thông đô thị hỗn hợp điển hình. |
| `img_022.jpg` | Xe mô tô cổ có gắn thuyền phụ (sidecar). | `motorcycle` + `person` | Gán 01 box `motorcycle` bao gồm cả xe mô tô và thuyền phụ; gán các box `person` cho người lái và người ngồi trong thuyền. | Thuyền phụ gắn cứng tạo thành một phương tiện 3 bánh cơ giới thống nhất. |
| `img_023.jpg` | Xích lô du lịch di chuyển phía trước trên đường phố Huế. | `cyclo` + `person` | Gán `cyclo` cho xe, `person` cho người đạp và du khách. | Thống nhất chuẩn gán cho dòng xe xích lô du lịch. |
| `img_024.jpg` | Xe ô tô cổ AMC Pacer wagon chụp cận nội thất ghế. | `car` | Gán `car`, `truncated=yes`, `occluded_level=0`. | Xe bị cắt mép do chụp cận cảnh thân xe. |
| `img_025.jpg` | Người đi dạo và tiểu thương trên phố đi bộ ven biển. | `person` | Gán `person` cho từng người đi dạo rõ nét ($\ge 20$ px). | Quản lý người đi bộ trong không gian công cộng hỗn hợp. |
| `img_026.jpg` | Đôi nam nữ đèo nhau trên xe máy trong hầm Thủ Thiêm. | `motorcycle` + `person` | Gán 01 `motorcycle`, 01 `person` (người lái, `occluded_level=1`), 01 `person` (người ngồi sau, `occluded_level=2`). | Đảm bảo tính toán đúng số lượng hành khách trên phương tiện. |
| `img_027.jpg` | Bia mộ đá hoa cương tạc nguyên khối hình xe bán tải. | **KHÔNG GÁN** | Tuyệt đối không gán `car` hay `truck`. | Tượng đài bằng đá tĩnh trong nghĩa trang, không phải xe cơ giới. |
| `img_028.jpg` | Xe bán tải gắn tượng ngựa trang trí trên thùng hàng. | `car` | Gán `car` ôm thân xe; phần tượng ngựa gắn cố định tick `can_review=true` để chốt viền box. | Tình huống dị biệt cần đưa ra cuộc họp nhóm. |
| `img_029.jpg` | Xe máy chở bao tải hàng khổng lồ che kín người lái phía sau. | `motorcycle` + `person` | Gán `motorcycle` bao trùm cả khung xe và toàn bộ khối bao tải hàng; gán `person` cho người lái (`occluded_level=2`). | Khối hàng cồng kềnh là mối nguy va quệt trực tiếp trên làn đường. |
| `img_030.jpg` | Đám đông hàng trăm xe máy dừng chờ đèn đỏ tại Sài Gòn. | `motorcycle` + `person` + `ignore_region` | Các xe phía trước gán chi tiết; khu vực xa dày đặc mờ mịt gộp vào `ignore_region`. | Giải quyết bài toán tải gán nhãn và tránh phạt oan detector. |
| `img_031.jpg` | Phố đêm Khang Định với các cửa hàng và xe cộ. | `car` + `motorcycle` | Gán theo hình thái xe nhìn rõ dưới ánh đèn đường. | Đánh giá độ nhạy cảm biến trong điều kiện ánh sáng nhân tạo. |
| `img_032.jpg` | Xe buýt đón trả khách tại trạm dừng giờ cao điểm. | `bus` + `person` | Gán `bus` cho xe buýt công cộng lớn; gán `person` cho đám đông hành khách đứng chờ trên vỉa hè. | Minh chứng tuyệt vời cho sự cần thiết của lớp `bus` riêng biệt. |
| `img_033.jpg` | Ma-nơ-canh mặc thời trang đứng tạo dáng trong tủ kính. | **KHÔNG GÁN** | Tuyệt đối không gán `person`. | Búp bê ma-nơ-canh tĩnh trong cửa hàng, không phải con người. |
| `img_034.jpg` | Xích lô qua đường tại Huế. | `cyclo` + `person` | Gán `cyclo` và các box `person` tương ứng. | Giao cắt ngã ba đường của phương tiện thô sơ. |
| `img_035.jpg` | Xe Auto Rickshaw tại Ấn Độ chở quá tải người đu bám. | `tuk_tuk` + `person` | Gán 01 `tuk_tuk` cho thân xe; gán từng box `person` cho từng hành khách nhìn rõ phần thân. | Xe 3 bánh chở khách quá tải đặc trưng của Nam Á. |
| `img_036.jpg` | Xe tải nhẹ CMC Veryca thùng lửng trên phố Khai Phong. | `truck` | Gán `truck`, `occluded_level=0`. | Xe tải nhẹ có cabin tách biệt thùng hàng riêng. |
| `img_037.jpg` | Xe bán tải lắp nắp chụp thùng cao Master 75 Truck Cap. | `car` | Gán `car`, `occluded_level=0`. | Dù lắp nắp chụp thùng cao, bản chất xe vẫn là xe bán tải con. |
| `img_038.jpg` | Tủ kính trưng bày bộ sưu tập đồ chơi xe hơi, búp bê. | `ignore_region` | Gán 01 vùng `ignore_region` bao trùm toàn bộ tủ kính trưng bày đồ chơi. | Cách ly đồ chơi mô hình nhỏ li ti khỏi bộ dữ liệu huấn luyện. |
| `img_039.jpg` | Đoàn xích lô du lịch nối đuôi nhau trên đường. | `cyclo` + `person` | Gán từng chiếc `cyclo` và từng `person` tương ứng. | Chuỗi phương tiện xích lô nối đuôi trong giao thông du lịch. |
| `img_040.jpg` | Đại lộ Bangkok với xe buýt, ô tô và xe máy lưu thông dày đặc. | `bus` + `car` + `motorcycle` | Gán `bus` cho các xe buýt công cộng, `car` cho ô tô con và `motorcycle` cho xe máy. | Giao thông hỗn hợp đa tầng đặc trưng của đại đô thị Đông Nam Á. |

---

## 5. Quy trình QC và Lịch sử phiên bản

### 5.1. Ngưỡng kiểm soát chất lượng (QC Targets)
* **Kích thước tối thiểu:** Box cao $< 20$ pixel phải gom vào `ignore_region`.
* **IoU ghép cặp:** Box cao $\ge 50$ px yêu cầu IoU $\ge 0.85$; box nhỏ $20-50$ px yêu cầu IoU $\ge 0.70$.
* **Độ đồng thuận liên gán nhãn (IAA):** IoU trung bình $\ge 0.85$, đồng thuận phân loại lớp $\ge 95\%$.
* **Ngưỡng chấp nhận bàn giao:** Tỷ lệ ảnh lỗi $\le 2\%$ và không có lỗi hệ thống lặp lại.

### 5.2. Lịch sử thay đổi

| Phiên bản | Người thực hiện | Nội dung thay đổi so với v0 | Cơ sở và lý do |
|---|---|---|---|
| **v0.0** | Giảng viên | Bản sơ khai ban đầu chỉ có 5 lớp cơ bản, không có quy tắc chi tiết cho giao thông phức tạp. | Bản tài liệu gốc của bài lab. |
| **v0.1** | Nguyễn Xuân Anh | 1. **Mở rộng Taxonomy từ 5 lên 9 lớp đối tượng:** bổ sung `bus`, `tuk_tuk`, `bicycle`, `cyclo` để bao phủ triệt để thực tế 40 ảnh giao thông châu Á.<br>2. Định lượng hoá 4 mức `occluded_level` theo ngưỡng diện tích che khuất.<br>3. Ban hành quy tắc phân tách nghiêm ngặt giữa người và phương tiện (người lái vs xe).<br>4. Quy tắc loại trừ đối tượng phi thực thể: biển quảng cáo 2D, tượng đài, ma-nơ-canh, đồ chơi, ảnh phản chiếu gương.<br>5. Bảng phân tích chi tiết 40/40 ảnh trong tập dữ liệu với các lớp đề xuất mới. | Khảo sát thực tế toàn diện 40 ảnh `img_001` - `img_040`, giải quyết triệt để các ca khó đặc trưng đường phố châu Á để chuẩn bị cho buổi họp nhóm Pha 2. |
