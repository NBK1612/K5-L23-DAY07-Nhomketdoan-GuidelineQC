# Guideline gán nhãn — v0.2 cá nhân — 03103 - Lê Ngọc Dũng

- Phiên bản: v0.2 cá nhân — bản đề xuất có bảng rà soát 40 ảnh, để người viết kiểm tra trước Pha 2.
- Ngày: 16/11/2026.
- Người viết: 03103 - Lê Ngọc Dũng; Codex hỗ trợ soạn nội dung và xem 40 ảnh theo yêu cầu; học viên cần rà soát, xác nhận quyết định trước khi sử dụng.
- Người duyệt: Chưa duyệt.
- Công cụ: CVAT; nhãn bounding box; xuất COCO 1.0.
- Dữ liệu: 40 ảnh trong `data/images/`.
- Căn cứ: `guideline/TEMPLATE_guideline.md`, `guideline/guideline_v0_starter.md`, `GUIDE.md`, `RULES.md`, `docs/QC_CHEATSHEET.md`, `submission/taxonomy.json` hiện ở phiên bản `v0`.

> Đây là bản đề xuất cá nhân, chưa phải guideline thống nhất hoặc kết quả calibration. Ngưỡng QC ở mục 5 lấy từ đề bài. Ngưỡng che khuất và quy tắc diễn giải ở mục 2–3 là đề xuất cần người viết xác nhận và mang ra họp Pha 2. Bảng mục 4 do Codex hỗ trợ phân tích trực tiếp ảnh gốc theo yêu cầu, không phải xác nhận học viên đã tự quan sát hoặc nhóm đã chốt. Chưa tạo nhãn hay đo QC.

## 1. Mục tiêu

Tạo nhãn để huấn luyện và đánh giá mô hình phát hiện người và phương tiện trong ảnh đường phố. Mỗi đối tượng hợp lệ có một lớp, một box ôm sát phần nhìn thấy và các thuộc tính theo cùng quy tắc.

Ưu tiên tránh bỏ sót người, nhầm lớp phương tiện, gán tượng hoặc hình in thành đối tượng thật và vẽ box không thống nhất khi đối tượng bị che. Những lỗi này có thể khiến mô hình học sai và làm giảm độ tin cậy của dữ liệu dùng cho camera giao thông.

## 2. Lớp và thuộc tính

### 2.1. Lớp

Đề xuất bộ nhãn gồm tám lớp đối tượng và một lớp vùng bỏ qua. Trong đó sáu tên lớp cơ sở có trong taxonomy v0; `bicycle`, `three_wheeler`, `bus` là ba lớp đề xuất bổ sung, chỉ sử dụng khi nhóm thống nhất. Một đối tượng chỉ thuộc một lớp; `ignore_region` không phải một đối tượng giao thông.

| Lớp | Định nghĩa (một câu) | Ví dụ khái niệm | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô con chở người, gồm sedan, hatchback và SUV. | Sedan bốn cửa, SUV. | Pickup có thùng hàng riêng thuộc `truck`; xe đồ chơi không gán. |
| `truck` | Xe tải hoặc xe có khoang/thùng hàng riêng với cabin. | Xe tải thùng, pickup, xe chuyên chở ô tô. | SUV thuộc `car`; xe có khoang kín liền thân cần phân biệt với `van`. |
| `van` | Xe thùng kín cỡ nhỏ có khoang sau liền thân, dùng chở người hoặc hàng. | Xe van giao hàng, van chở người. | Xe tải cabin tách thùng thuộc `truck`; xe buýt chưa có lớp trong taxonomy hiện tại. |
| `motorcycle` | Phương tiện hai bánh có động cơ dạng xe máy hoặc mô tô. | Xe tay ga, mô tô hai bánh. | Xe đạp và tuk-tuk không tự ép vào lớp này. |
| `person` | Người thật có phần cơ thể nhìn thấy và đủ bằng chứng nhận diện là người. | Người đi bộ, người ngồi trên xe hở. | Tượng, ma-nơ-canh, người trên poster và ảnh phản chiếu không gán. |
| `bicycle` *(đề xuất mới)* | Xe đạp hai bánh vận hành bằng bàn đạp, không có bằng chứng là xe máy. | Xe đạp ở `img_006`, `img_029`. | Xích lô thuộc đề xuất `three_wheeler`; xe có động cơ chưa rõ loại cần review. |
| `three_wheeler` *(đề xuất mới)* | Phương tiện có kết cấu ba bánh chở người hoặc hàng, gồm tuk-tuk, auto-rickshaw và xích lô. | Tuk-tuk `img_004`, xích lô `img_023`, xe chở hàng `img_035`. | Mô tô gắn sidecar vẫn đề xuất thuộc `motorcycle`, một box gồm cả thùng bên. |
| `bus` *(đề xuất mới)* | Xe có thân và khoang hành khách chuyên dụng dạng xe buýt/xe khách, gồm minibus. | Minibus `img_009`; xe buýt `img_031`, `img_037`. | Van thân liền cỡ nhỏ thuộc `van`; không suy đoán số ghế từ ảnh để phân lớp. |
| `ignore_region` | Vùng có đối tượng mục tiêu quá nhỏ hoặc quá dày đặc để gán riêng đáng tin cậy. | Cụm đối tượng nhỏ hơn ngưỡng 20 px; phần đám đông không tách được. | Vùng nền trống; vùng chứa tượng hoặc đồ chơi đơn thuần; đối tượng đủ rõ để gán riêng. |

**Vật không thuộc lớp nào:** Không tự thêm lớp trong CVAT và không ép vào lớp gần giống. Ghi tên ảnh, vị trí và câu hỏi vào danh sách cần họp. Vật ngoài phạm vi không tự động trở thành `ignore_region`.

**Các lớp mới cần được nhóm phê duyệt trước Pha 3:** `bicycle`, `bus`, `three_wheeler`. Lý do đề xuất là có hình dạng và cấu trúc riêng được quan sát trong bộ ảnh; ép xe đạp hoặc tuk-tuk vào `motorcycle` và xe buýt vào `truck` sẽ làm định nghĩa không nhất quán. Nếu chưa chốt lớp mới, ghi ca ngoài taxonomy để họp; không tự ép lớp. Chỉ áp dụng sau khi cập nhật taxonomy và sinh lại nhãn CVAT. Không thêm `horse` chỉ vì thấy hình ngựa: `img_017` và `img_028` có hình ngựa dạng tượng.

**Ranh giới cần thống nhất:** Không dùng số ghế 16/20 làm tiêu chí bắt buộc vì ảnh thường không cho phép đếm. Với `van`/`bus`, dựa vào kiểu thân xe và khoang khách; không chắc thì review. Với `car`/`truck`, ưu tiên kết cấu cabin và thùng hàng riêng; pickup lắp nắp cao vẫn là `truck` (`img_037`).

### 2.2. Thuộc tính

Dùng đúng tên và tập giá trị của taxonomy hiện tại; không thay bằng `none/partial/heavy` hoặc `true/false` cho thuộc tính dạng lựa chọn.

**`occluded_level` — đề xuất ngưỡng che khuất:**

Ước lượng tỉ lệ diện tích hình chiếu của đối tượng bị vật khác che, dựa trên hình dạng có thể suy ra trong ảnh. Không tính phần nằm ngoài khung ảnh là che khuất; phần đó dùng `truncated`. Việc ước lượng mức che không có nghĩa được vẽ box ra phần khuất.

| Giá trị | Ngưỡng đề xuất | Ví dụ khái niệm |
|---|---|---|
| `0` | 0% bị che. | Người đứng riêng, không có vật chắn phía trước. |
| `1` | Trên 0% đến dưới 30%. | Một phần chân bị vật khác che. |
| `2` | Từ 30% đến dưới 70%. | Một phần lớn thân xe bị xe khác chắn. |
| `3` | Từ 70% trở lên nhưng vẫn đủ bằng chứng nhận diện lớp. | Chỉ còn một phần nhỏ đặc trưng của đối tượng nhìn thấy. |

Các ngưỡng trên là **đề xuất cá nhân**, không phải ngưỡng chính thức có sẵn trong taxonomy. Nếu ước lượng gần ranh giới hoặc không chắc mức, chọn mức phù hợp nhất với bằng chứng, bật `can_review` và ghi lý do. Không nhận diện được lớp thì không tạo box lớp cụ thể chỉ để bật cờ.

**`truncated`:**

- `yes`: Đối tượng bị ranh giới ảnh cắt mất một phần; box kết thúc tại mép ảnh tương ứng.
- `no`: Đối tượng nằm trọn trong ảnh. Chỉ chạm mép nhưng không có bằng chứng bị cắt thì không tự kết luận `yes`.
- Một đối tượng có thể đồng thời bị che và bị cắt mép; đánh giá hai thuộc tính riêng.

**`can_review` — checkbox:**

- Bật khi còn nghi ngờ về lớp, mép box, mức che, cắt mép hoặc cách áp dụng một quy tắc.
- Ghi kèm tên ảnh, vị trí đối tượng và câu hỏi trong ghi chú CVAT hoặc sổ ca cần xem lại.
- Không bật khi quyết định đã rõ theo guideline đang áp dụng.
- Đây là thuộc tính của box, không phải lớp. Với vật chưa có lớp hợp lệ và chưa tạo box, ghi ca riêng để nhóm xử lý.

Với `ignore_region`, đề xuất giữ mặc định `occluded_level = 0`, `truncated = no` vì đây là vùng quy ước; bật `can_review` nếu chưa chắc ranh giới vùng. Nhóm cần xác nhận quy ước này trước khi gán chính thức.

## 3. Quy tắc vẽ box

Các quy tắc dưới là đề xuất cá nhân để chốt ở Pha 2. Khi bắt đầu Pha 3, áp dụng bản v1.0 của nhóm đã thống nhất.

1. **Box ôm phần nhìn thấy:** Dùng hình chữ nhật nhỏ nhất bao các phần nhìn thấy thuộc cùng đối tượng. Không tự kéo cạnh box tới vị trí suy đoán của phần bị che. Box có chiều rộng, chiều cao dương và nằm trong ảnh.
2. **Đối tượng bị che:** Nếu có nhiều phần rời nhau nhưng đủ bằng chứng thuộc cùng một đối tượng, dùng một box bao các phần đó; vùng bị vật chắn nằm giữa có thể nằm trong box. Không chia một người hoặc một xe thành nhiều box.
3. **Đối tượng bị cắt mép:** Chỉ bao phần trong ảnh, kéo cạnh tới đúng ranh giới ảnh và đặt `truncated` theo mục 2.2. Không vẽ ra ngoài khung.
4. **Kích thước tối thiểu:** Tính chiều cao box theo pixel của ảnh gốc. Cao dưới 20 px thì không gán từng đối tượng; dùng `ignore_region` ôm vùng đối tượng nhỏ. Cao đúng 20 px được xem xét gán riêng nếu nhận diện được lớp. Không dùng kích thước hiển thị sau khi phóng to để vượt ngưỡng.
5. **Người trên xe hở:** Gán riêng người và phương tiện nếu cả hai đủ điều kiện; một xe có nhiều người thì mỗi người một box. Box xe bao các phần của xe, không mở rộng chỉ để bao người ngồi trên xe.
6. **Người trong cabin kín:** Đề xuất chỉ gán `person` nếu phần cơ thể nhìn qua kính đủ rõ để xác định người và đạt ngưỡng chiều cao. Không gán người từ bóng mờ hoặc suy đoán có tài xế. Cần họp chốt phạm vi này để mọi người làm giống nhau.
7. **Xe chở hàng hoặc chở xe khác:** Box xe chở bao các bộ phận của chính xe, không mở rộng theo hàng hóa. Nếu hàng là phương tiện thật thuộc lớp hợp lệ, đủ rõ và đạt kích thước tối thiểu, gán thêm box riêng cho phương tiện đó. Hàng thông thường không có box riêng.
8. **Các phần gắn với xe:** Đề xuất tính gương, bánh và bộ phận cố định vào box khi nhìn thấy rõ. Đầu kéo nối rơ-moóc được xem là một `truck`; sidecar gắn với mô tô được đề xuất là một `motorcycle`. Hai cách xử lý này cần nhóm xác nhận.
9. **Tượng, đồ chơi, hình in, ma-nơ-canh và phản chiếu:** Không gán như người hoặc phương tiện thật. Không gán bóng đổ. Với phương tiện trang trí hoặc trưng bày chưa rõ là xe thật hay mô hình, ghi ca cần họp, không kết luận chỉ từ vẻ ngoài giống xe.
10. **Xe chủ (ego vehicle):** Đề xuất không gán phần xe mang camera như nắp capo, khung cửa hoặc gương xuất hiện sát máy ảnh. Không loại một xe khác chỉ vì xe đó ở gần camera.
11. **Đám đông hoặc xe dày đặc:** Gán từng đối tượng nếu xác định được lớp và phân biệt được phần nhìn thấy của từng vật. Dùng `ignore_region` cho phần còn lại không thể tách đáng tin cậy. Không bỏ qua cả vùng chỉ vì các box có chồng lấn; giới hạn vùng bỏ qua để tránh bao đối tượng đã gán rõ.
12. **Vật nhoè, ảnh tối:** Chỉ gán khi có đủ bằng chứng nhận diện lớp. Box theo đường biên phần đối tượng nhìn thấy, không chạy theo vệt nhòe mờ; bật `can_review` khi mép không chắc. Không chọn một lớp gần giống khi thiếu bằng chứng; ghi ca cần xem lại.
13. **Kiểm trước khi xuất:** Rà đúng lớp, đủ đối tượng, không box trùng, đúng thuộc tính và đúng ảnh được giao; xuất COCO 1.0. Công cụ kiểm cấu trúc không thay thế việc tự nhìn lại box.

## 4. Ca khó

Bảng dưới được Codex hỗ trợ rà soát trực tiếp đủ 40 ảnh trong `data/images/` theo yêu cầu của học viên. Đây là các **quyết định đề xuất để học viên kiểm tra và đem ra Pha 2**, không phải nhãn đã gán, kết quả calibration hay bằng chứng học viên đã tự làm độc lập. Các tên lớp mới chỉ có hiệu lực khi nhóm phê duyệt. Với ảnh có nhiều vật, mô tả tập trung vào điểm dễ gây bất đồng, không thay thế việc kiểm toàn bộ ảnh khi gán.

| Ảnh | Tình huống quan sát | Quyết định đề xuất | Lý do |
|---|---|---|---|
| `img_001.jpg` | Pickup trắng trước quán, người đứng/ngồi, hàng trong thùng và biển quảng cáo. | Pickup → `truck`; gán từng người thật đủ rõ; box xe không bao hàng rời; hình in không gán. | Cabin và thùng hàng riêng; người thật khác hình trên biển; xe và người sát mép cần kiểm tra `truncated`. |
| `img_002.jpg` | Xe chuyên chở ô tô với nhiều phương tiện xếp trên hai tầng. | Một `truck` cho đầu kéo và rơ-moóc đang nối; từng phương tiện chở trên đó gán theo lớp riêng khi nhận diện được; người bên phải gán riêng. | Xe được vận chuyển vẫn là vật thể riêng; khung giá gây che khuất; không tự coi mọi xe trên giá đều là `car` nếu có thùng pickup. |
| `img_003.jpg` | Người bán hàng rong gánh trái cây, người và xe đạp/xe máy đan xen. | Người thật → `person`; xe đạp → đề xuất `bicycle`, xe máy → `motorcycle`; không mở box người để bao toàn bộ đòn gánh và giỏ. | Đòn gánh/hàng không phải bộ phận cơ thể; cần tách người với phương tiện và đánh giá phần bị che. |
| `img_004.jpg` | Hai tuk-tuk đỗ cạnh nhau, có người ngồi trong khoang hở. | Mỗi xe → đề xuất `three_wheeler`; người nhìn rõ → `person` riêng; cọc tiêu không gán. | Tuk-tuk có cấu trúc ba bánh, không phải mô tô hai bánh; box người và xe tách biệt. |
| `img_005.jpg` | Nhóm người đội mũ trên xe máy che nhau, van bạc ở phía sau. | Người thật đủ rõ → `person`; van → `van`; xe máy chỉ gán khi đủ bằng chứng xác định đối tượng; đặt mức che theo từng vật. | Mũ bảo hiểm không chứng minh có một xe riêng cho mỗi người; không suy ra xe từ số người. |
| `img_006.jpg` | Người đi xe máy và xe đạp trên cùng làn, nhiều người ở xa. | Tách `person` và `motorcycle`; người đạp xe và xe đạp dùng hai box, xe đạp thuộc lớp mới đề xuất; vùng vật nhỏ dưới 20 px dùng `ignore_region`. | Không gộp người và xe; phân biệt xe đạp bằng hình dạng nhìn thấy, không bằng vị trí trên đường. |
| `img_007.jpg` | Pickup trắng và xe tải thùng trong khu vực có mái che, nền tối. | Cả pickup và xe tải → `truck`; không gán pickup thành `car`; chỉ gán người nếu thực sự nhận diện được. | Kết cấu thùng hàng riêng quyết định lớp; ánh sáng tối không phải lý do bỏ phương tiện rõ. |
| `img_008.jpg` | Xe máy đỗ san sát, nhiều xe bị người hoặc xe khác che và cắt mép. | Gán riêng các `motorcycle` tách được và người thật; vùng còn lại quá nhỏ/không tách được dùng `ignore_region`. | Chồng lấn box không tự động làm mất khả năng gán riêng; tránh phủ ignore lên xe đã rõ. |
| `img_009.jpg` | Minibus nhìn ngang giữa phố; một số xe máy sát mép ảnh. | Minibus → đề xuất `bus`; xe máy và người rõ gán riêng; kiểm tra phần xe ở mép. | Phân biệt xe khách với van bằng cấu trúc thân/khoang hành khách, không suy đoán chính xác số ghế. |
| `img_010.jpg` | Người mua bán quanh xe máy và xe hàng ở chợ. | Gán `person`, `motorcycle` rõ; không gán xe đẩy bán hàng vào `bicycle` hoặc `motorcycle` chỉ vì có bánh; hàng hóa không gán. | Phạm vi lớp không bao mọi vật có bánh; xe bị che cần box theo các phần nhìn thấy. |
| `img_011.jpg` | Khu trưng bày có hình nhân vật lớn và các vật dạng mô tô ghép kim loại. | Không gán hình nhân vật thành `person`; vật dạng mô tô trưng bày ghi ca cần họp, chưa kết luận là xe thật từ hình dạng; người và xe thật rõ ở nền vẫn xem xét riêng. | Ảnh không đủ xác minh khả năng vận hành hay vật trưng bày là xe thật; không dùng suy đoán đó làm tiêu chí chắc chắn. |
| `img_012.jpg` | Pickup chở thùng hàng ở tiền cảnh, SUV và xe tải ở phía trước. | Pickup → `truck`, SUV → `car`; box pickup không bao hàng rời; phần bị cắt ở mép đặt `truncated = yes`. | Thùng hàng mở không biến pickup thành car; hàng hóa ngoài phạm vi lớp. |
| `img_013.jpg` | Phố đêm có người trên vỉa hè, ô tô xa, phản sáng trên mặt đường. | Gán người và xe đủ nhận diện/đạt ngưỡng; không gán phản sáng thành phương tiện; vùng tối không rõ ghi review. | Ánh sáng và bóng phản chiếu không phải vật thể; phải kiểm kích thước trên ảnh gốc. |
| `img_014.jpg` | Hai người đi bộ rõ, phương tiện và người phía sau bị nhòe chuyển động. | Gán từng `person` rõ; phương tiện nhòe chỉ gán khi xác định được lớp; box không bao vệt mờ kéo dài; bật `can_review` khi mép khó xác định. | Tách phần đối tượng có thể xác định khỏi vệt nhòe; không đoán lớp từ một dải màu. |
| `img_015.jpg` | Ô tô đỏ/bạc ở hai mép, xe phía bên kia đường và phản chiếu trên mặt đường ướt. | Xe tiền cảnh → `car`, `truncated = yes`; xe nền đủ rõ vẫn gán; không gán ảnh phản chiếu bên dưới. | Cắt mép khác che khuất; phản chiếu không phải đối tượng thứ hai. |
| `img_016.jpg` | Tuk-tuk, van, người đi Vespa và người đứng sát mép phải. | Tuk-tuk → đề xuất `three_wheeler`, van → `van`, Vespa → `motorcycle`; người gán riêng; thuộc tính cắt mép theo từng box. | Cùng ảnh có nhiều lớp và nhiều kiểu bị che; không gộp người vào box xe. |
| `img_017.jpg` | Tượng người cưỡi ngựa, đầu tượng có nón giao thông. | Không gán tượng thành `person`; không tạo `horse`; không dùng `ignore_region` cho toàn tượng. | Đây là vật mô phỏng, không phải người hoặc động vật thật; nón đặt trên tượng không đổi bản chất. |
| `img_018.jpg` | Người trên biển quảng cáo, các xe sau hàng rào. | Không gán người in trên biển; xe thật đủ rõ gán theo lớp và đánh giá mức bị hàng rào che; không coi cả bãi là ignore. | Phân biệt hình in với vật thật; song rào chỉ che một phần hình chiếu, không phải toàn bộ khung bao hàng rào. |
| `img_019.jpg` | Xe vàng tạo hình hoạt hình, người lái nhìn thấy, linh vật chuột và khán giả sau rào. | Đề xuất xe thật dạng ô tô → `car`, người lái → `person`; không suy ra thêm một `person` từ linh vật khi không có bằng chứng cơ thể người; khán giả gán riêng khi tách được. | Trang trí không tự làm xe thành đồ chơi; không suy đoán bên trong linh vật; vùng đám đông khó tách cần review/ignore có giới hạn. |
| `img_020.jpg` | Gương chiếu hậu xe mang camera phản chiếu xe và đường. | Không gán phần xe chủ, gương hoặc phương tiện xuất hiện qua phản chiếu. | Tránh tạo vật thể giao thông từ ảnh trong gương; ảnh âm vẫn được giữ trong bộ dữ liệu. |
| `img_021.jpg` | Dòng xe máy và người rất dày ở giao lộ. | Gán xe/người riêng ở vùng còn tách được; dùng `ignore_region` cho vùng xa nhỏ hoặc không thể phân tách; không dùng một box chung cho cả đoàn. | Không đủ cơ sở đếm vật từ các mũ/đầu chồng nhau; vùng ignore phải tránh che mất các vật rõ. |
| `img_022.jpg` | Ảnh đen trắng có mô tô gắn sidecar đang đỗ trên đường. | Đề xuất một `motorcycle` gồm xe và sidecar; không thêm `person` vì không thấy người lái. | Sidecar là bộ phận gắn với mô tô; không suy ra có người chỉ vì xuất hiện xe. |
| `img_023.jpg` | Xích lô nhìn từ phía sau với người đạp, ô tô đen và xe máy phía xa. | Xích lô → đề xuất `three_wheeler`, người đạp → `person`, ô tô → `car`; không đưa xích lô vào `bicycle` theo định nghĩa hai bánh. | Chọn ranh giới lớp theo cấu trúc xe; người đạp là đối tượng riêng. |
| `img_024.jpg` | Xe trưng bày trong nhà, có xe vàng nhỏ đặt trên nóc ô tô đen. | Ô tô trưng bày có hình dạng/kích thước xe thật → đề xuất `car`; xe vàng nhỏ trên nóc ghi review riêng, chưa quyết là ô tô thật hay mô hình. | Trưng bày không tự đồng nghĩa đồ chơi; ảnh chưa đủ chứng minh bản chất xe vàng, cần nhóm chốt phạm vi. |
| `img_025.jpg` | Lối đi ven biển, người và phương tiện thưa dần về xa. | Gán người/xe đủ rõ, đủ 20 px; vùng có đối tượng dưới ngưỡng dùng ignore gọn; không bao ghế, cây hoặc cả lối đi vào vùng bỏ qua. | Ngưỡng áp dụng theo pixel gốc; không gán bóng hoặc đồ vật thành người. |
| `img_026.jpg` | Hai người ngồi trên một xe máy, phía sau là tranh tường. | Hai box `person` và một `motorcycle` nếu từng đối tượng đủ điều kiện; tranh tường không gán. | Một phương tiện có nhiều người; người ngồi sau không phải phần xe; tranh không phải cảnh thật. |
| `img_027.jpg` | Vật hình pickup đặt trên bệ trong nghĩa trang. | Không gán vật hình xe trên bệ thành `truck`; người hoặc xe thật khác ở nền nếu nhận diện được vẫn xét theo quy tắc chung. | Bối cảnh và bệ cho thấy vật tưởng niệm dạng xe; không suy ra cả ảnh không có đối tượng hợp lệ. |
| `img_028.jpg` | Pickup cổ chở tượng ngựa, ô tô đỏ phía sau. | Pickup → `truck`, xe đỏ → `car`; không gán ngựa thành động vật; box truck chỉ bao xe, không bao tượng ngựa. | Phân biệt phương tiện chở với hàng trên thùng; thống nhất quy tắc không mở box theo hàng. |
| `img_029.jpg` | Người trên xe máy và người đi xe đạp cùng giữ các ống dài. | Gán người, `motorcycle` và đề xuất `bicycle` riêng; không kéo box xe/người để bao toàn bộ ống. | Ống là vật được vận chuyển/cầm giữ, không phải cấu trúc của xe hay cơ thể. |
| `img_030.jpg` | Bãi xe máy đông, người đứng trong bãi và người trên poster. | Gán xe tách được và người thật; người trên poster không gán; phần xe khó tách dùng ignore có giới hạn. | Cần phân biệt người in, người thật và cụm xe; không để ignore che lỗi ở hàng xe rõ. |
| `img_031.jpg` | Giao thông ban đêm có ô tô, xe máy, xe buýt và phản chiếu trên đường. | Gán các phương tiện thật đủ rõ; buýt → đề xuất `bus`; phản chiếu và hình hoạt hình trên biển không gán. | Độ sáng không xác định lớp; mỗi phương tiện chỉ có một box, không thêm box cho bóng phản chiếu. |
| `img_032.jpg` | Hàng người chờ bên đường, ô tô bị cắt ở mép trái. | Gán từng `person` còn phân tách được; chỉ ignore phần không thể tách; ô tô → `car`, `truncated = yes`. | Đứng gần nhau chưa đủ lý do bỏ qua; thuộc tính cắt mép áp dụng riêng cho ô tô. |
| `img_033.jpg` | Ma-nơ-canh trong cửa kính và cảnh phản chiếu trên kính. | Không gán ma-nơ-canh là `person`; không gán vật thể chỉ xuất hiện trong phản chiếu. | Hình người và cảnh trên bề mặt kính không phải đối tượng thật ở vị trí đang nhìn thấy. |
| `img_034.jpg` | Người đi bộ giữa giao lộ, ô tô phía sau và xe máy cắt mép phải. | Người đi bộ → `person`, ô tô → `car`; xe máy và người nhìn rõ ở mép gán riêng với thuộc tính cắt phù hợp. | Ngược sáng không loại người đủ rõ; vật chồng nhau vẫn là đối tượng riêng. |
| `img_035.jpg` | Xe ba bánh chở kiện hàng cao, xe máy và người lái ở tiền cảnh. | Xe ba bánh → đề xuất `three_wheeler`; box chỉ bao xe, không bao các kiện hàng; xe máy và người lái gán riêng. | Giữ cùng quy tắc hàng hóa như pickup; kiện hàng không biến thành bộ phận xe vì được buộc cố định. |
| `img_036.jpg` | Xe tải nhỏ màu xanh có thùng phía sau. | Gán `truck`, không gán `van` chỉ vì kích thước xe nhỏ; xe máy nền xét từng vật rõ. | Cabin tách với thùng là dấu hiệu phân biệt, không phải kích thước tổng thể. |
| `img_037.jpg` | Pickup trắng lắp nắp thùng cao, xe buýt xanh phía trước và người đi bộ. | Pickup → `truck`; xe buýt → đề xuất `bus`; người → `person`; không đổi pickup sang `van` vì có nắp thùng. | Phân loại theo cấu trúc gốc cabin/thùng hàng; phụ kiện không đổi lớp. |
| `img_038.jpg` | Tủ kính chứa xe mô hình và đồ chơi. | Không gán các mô hình thành `car`, `truck`, `bus` hoặc người; không dùng ignore để bao tủ. | Vật mô phỏng không phải đối tượng thật thuộc phạm vi; đây là ca âm hữu ích. |
| `img_039.jpg` | Đường phố với người đi bộ, người đi xe đạp và xe máy ở nhiều cỡ. | Gán đối tượng đủ rõ/đủ 20 px; tách người với xe đạp hoặc xe máy; kiểm tra vùng xa nhỏ trước khi dùng ignore. | Không gộp người lái và phương tiện; biển quảng cáo không tạo thêm người. |
| `img_040.jpg` | Người đội mũ ở tiền cảnh, taxi xanh cắt mép, tuk-tuk, SUV và xe buýt phía sau. | Người → `person`, taxi/SUV → `car`, tuk-tuk → đề xuất `three_wheeler`, buýt → đề xuất `bus`; kiểm cả che và cắt ở từng đối tượng. | Không suy ra xe máy riêng dưới người tiền cảnh khi chỉ có chi tiết quá ít; xe chủ nếu xác định được thì loại theo quy tắc ego. |

### Câu hỏi mang ra họp Pha 2

- Có duyệt ba lớp đề xuất `bicycle`, `bus`, `three_wheeler` không; ranh giới từng lớp là gì?
- Nhóm dùng các ngưỡng che khuất nào cho bốn mức `0–3`?
- Người nhìn qua kính cabin có thuộc phạm vi `person` không?
- Box xe có bao hàng hóa không; xử lý rơ-moóc và sidecar thế nào?
- Dấu hiệu nào đủ để phân biệt xe thật trưng bày với mô hình?
- Ranh giới `ignore_region` và cách xử lý thuộc tính của vùng được quy định ra sao?

Sau khi họp, ghi quyết định vào bản nhóm v1.0. Bản cá nhân Pha 1 được giữ làm bằng chứng quan điểm ban đầu, không sửa theo kết luận của người khác.

## 5. QC và phiên bản

### 5.1. Ngưỡng chính thức của bài

Các giá trị dưới lấy từ `submission/taxonomy.json`; không thay đổi mục `rules` để làm kết quả đạt ngưỡng.

| Nội dung | Ngưỡng / cách áp dụng |
|---|---|
| Kích thước tối thiểu | `min_box_height_px = 20`. |
| Ghép cặp box để so sánh | `match_iou = 0.5`; đạt ngưỡng ghép chưa có nghĩa box đã đạt chất lượng. |
| IoU từng cặp box lớn | `iou_thr_large = 0.85`, mốc kích thước `large_px = 50`. |
| IoU từng cặp box nhỏ | `iou_thr_small = 0.7`. |
| IAA | IoU trung bình trên các cặp ghép được ≥ 0.85 và tỉ lệ cùng lớp trên các cặp ghép được ≥ 95%. |
| Tỉ lệ ảnh lỗi | Số ảnh có ít nhất một lỗi / số ảnh đã kiểm ≤ 2% mới được xét Release. |

Script `common.py` dùng trung bình chiều cao của hai box để chọn ngưỡng từng cặp: từ 50 px trở lên dùng 0.85, dưới 50 px dùng 0.7. Ngưỡng IAA trung bình vẫn là 0.85, không hạ xuống 0.7 cho cả lô.

Luôn báo cáo tử số và mẫu số; đếm thêm box không ghép được để nhận ra bỏ sót hoặc gán thừa. IAA chỉ mô tả mức đồng thuận, không chứng minh mọi người đều gán đúng.

### 5.2. Quy trình kiểm và xử lý ca mơ hồ

- Pha 1: Học viên tự kiểm tra 40 ảnh và các đề xuất ở mục 4, xác nhận hoặc điều chỉnh bằng lập luận của mình; khai báo phạm vi hỗ trợ AI.
- Pha 2: Nhóm chốt v1.0 và taxonomy; chỉ sau đó mới dùng để gán chính thức.
- Pha 3: Gán độc lập; nhóm năm người cần ba người gán mỗi ảnh. Không đổi guideline giữa pha. Ca mơ hồ ghi `can_review` hoặc ghi sổ nếu chưa có box hợp lệ.
- Pha 4–5: So sánh IAA, xem bất đồng, họp calibration; người chốt do nhóm phân công. Mỗi quyết định thực tế được ghi một dòng trong `calibration_decisions.csv`; quy tắc mới ghi vào v1.1.
- Pha 6: Theo quy trình lab, kiểm mọi ảnh từng bị cờ cộng 20 ảnh ngẫu nhiên; báo cáo số ảnh duy nhất thực tế được kiểm. Đây là cách lấy mẫu nới lỏng cho lab 40 ảnh, không phải tuyên bố đã kiểm toàn bộ lô.

Kiểm đủ năm loại lỗi: `bo_sot`, `thua`, `sai_lop`, `sai_vi_tri_kich_thuoc`, `sai_thuoc_tinh`. Ảnh đã kiểm sạch ghi `khong`. Báo cáo cả tỉ lệ lỗi theo ảnh và theo đối tượng, kèm mẫu số.

### 5.3. Quyết định chất lượng

Áp dụng đúng thứ tự của đề:

1. **Hold:** Guideline còn khoảng trống hoặc dữ liệu có vấn đề; làm rõ hoặc xử lý dữ liệu trước.
2. **Rework:** Trên 2% ảnh có lỗi, lỗi lặp cùng kiểu hoặc IoU/IAA dưới ngưỡng; sửa rồi kiểm lại.
3. **Release:** Không còn điều kiện Hold/Rework, các ngưỡng đạt và có báo cáo QC xác nhận.

Hiện chưa có số liệu QC được xác minh cho bản này; không đưa ra quyết định Release/Hold/Rework cho lô nhãn.

### 5.4. Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v0 | Guideline khởi đầu do giảng viên cung cấp. | Làm cơ sở nhận diện điểm mơ hồ. |
| v0.1 cá nhân — bản nháp | Biên tập đủ năm phần; giữ sáu lớp cơ sở; dùng đúng thuộc tính và ngưỡng QC; bổ sung đề xuất quy tắc. | Bản cũ thiếu `van`, `ignore_region`, dùng mức thuộc tính chưa khớp và còn mã ảnh minh họa. |
| v0.2 cá nhân — hoàn thiện bảng ảnh | Rà soát 40 ảnh gốc với hỗ trợ AI; điền 40 mã ảnh thật và quyết định đề xuất; bổ sung ba lớp đề xuất, làm rõ bus/van và sidecar. | Sửa các mã ảnh minh họa; bổ sung ca che/cắt, đám đông, vật không thật, xe trưng bày và xe chở hàng. |

**Trước khi sử dụng:** Học viên cần tự rà soát quyết định và ghi công cụ AI, phạm vi hỗ trợ (gồm việc xem ảnh, đề xuất xử lý) cùng cách kiểm tra thực tế trong ghi chú cá nhân. Các quy tắc còn cần họp được ghi rõ; không tuyên bố đã thống nhất nhóm, gán nhãn hoặc QC chỉ từ tài liệu này.
