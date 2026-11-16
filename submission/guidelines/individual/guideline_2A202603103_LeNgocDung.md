# Guideline gán nhãn — v1.0 — Cá nhân

- **Phiên bản:** v1.0 – Dự thảo Calibration
- **Ngày:** 16/11/2026
- **Người viết / duyệt:** Học viên chương trình Nhân tài AI / Chưa duyệt
- **Bài thực hành:** Day 07 – Bounding Box Annotation
- **Công cụ:** CVAT
- **Dữ liệu:** 40 ảnh đường phố châu Á

> **Trạng thái:** Bản đề xuất cá nhân. Danh sách lớp, ID, thuộc tính và ngưỡng QC cần đối chiếu đề bài chính thức, chốt trong Calibration trước khi áp dụng.

## 1. Mục tiêu

Tạo bộ dữ liệu Bounding Box phục vụ huấn luyện và đánh giá mô hình AI phát hiện đối tượng giao thông và con người trong hình ảnh.

Yêu cầu:

- Mỗi đối tượng được phân loại thống nhất.
- Bounding Box chính xác và không bỏ sót đối tượng hợp lệ.
- Các tình huống đặc biệt phải có quy tắc xử lý rõ ràng.
- Nhiều Annotator áp dụng cùng Guideline phải cho kết quả có độ đồng thuận cao.

**Rủi ro:** Gán nhầm tượng thành người thật, mô hình xe thành xe thật hoặc bỏ sót phương tiện đặc biệt có thể làm sai dữ liệu huấn luyện và giảm khả năng nhận diện của mô hình.

## 2. Lớp và thuộc tính

### Lớp

Bảng dưới là **Taxonomy đề xuất để thử nghiệm**. Danh sách class và ID cuối cùng phải đối chiếu với yêu cầu chính thức của bài tập.

| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `person` | Người thật xuất hiện trong ảnh | Người đi bộ, tài xế | Tượng người, ma-nơ-canh |
| `car` | Ô tô con có chức năng vận chuyển người | Sedan, SUV, xe tạo hình đặc biệt còn vận hành được | Mô hình xe, tượng xe |
| `truck` | Phương tiện vận chuyển hàng hóa | Xe tải, xe chuyên chở ô tô | Ô tô con |
| `bus` | Phương tiện chở nhiều hành khách | Xe buýt | Xe tải, xe du lịch |
| `motorcycle` | Xe mô tô hoặc xe máy theo định nghĩa dự án | Xe máy hai bánh | Tuk-tuk nếu Taxonomy không quy định |
| `bicycle` | Xe đạp | Xe đạp thông thường | Xe máy |
| `horse` | Ngựa thật | Ngựa trên đường | Tượng ngựa |

**Vật không thuộc lớp nào:** Không tự tạo class hoặc ép vào lớp gần giống. Đánh dấu `can_review` nếu chưa rõ và chuyển trưởng nhóm quyết định. Nếu CVAT chưa cấu hình thuộc tính `can_review`, ghi ca vào danh sách Review riêng thay vì tự ý thêm thuộc tính.

### Thuộc tính

Các thuộc tính sau chỉ áp dụng **nếu dự án cho phép cấu hình**.

**Occlusion (mức che khuất):**

- `none`: Không bị che.
- `partial`: Bị che một phần.
- `heavy`: Bị che phần lớn nhưng vẫn nhận diện được.

**Truncation (bị cắt mép ảnh):**

- `false`: Đối tượng không bị cắt bởi ranh giới ảnh.
- `true`: Một phần đối tượng nằm ngoài khung ảnh.

**Chưa chốt:** Ngưỡng phần trăm che khuất để phân biệt `partial` và `heavy`. Nhóm phải thống nhất ngưỡng cụ thể trong Calibration; không tự áp dụng ngưỡng chưa được xác nhận.

## 3. Quy tắc vẽ box

1. **Phần nhìn thấy:** Box hình chữ nhật bao sát phần nhìn thấy của đối tượng, hạn chế vùng nền thừa.
2. **Đối tượng bị che khuất:** Chỉ vẽ quanh phần nhìn thấy theo quy tắc đang đề xuất; không tự mở rộng box vào vùng bị che khuất. Cần xác nhận cách xử lý với trưởng nhóm.
3. **Đối tượng bị cắt mép ảnh:** Box giới hạn trong khung hình; đánh dấu `truncated` nếu dự án có thuộc tính này.
4. **Tượng, hình in, đồ chơi, phản chiếu:** Đề xuất không gán như vật thể thật; ca chưa rõ đưa vào Calibration.
5. **Xe chủ (`ego vehicle`):** Nếu máy ảnh đặt trên phương tiện và chỉ thấy một phần phương tiện, cần quyết định riêng của trưởng nhóm trước khi gán.
6. **Đối tượng quá nhỏ:** Không tự đặt ngưỡng pixel. Chỉ gán khi đủ bằng chứng nhận diện class; ca chưa rõ chuyển Review.
7. **Đám đông:** Mỗi người thật nhận diện được là một đối tượng riêng. Không dùng một box chung bao cả đám đông, trừ khi Taxonomy quy định khác.
8. **Một vật có nhiều phần:** Xe tải chở ô tô: xe tải và từng ô tô nhìn thấy được xem xét gán box riêng nếu thuộc class hợp lệ. Không gộp tất cả thành một phương tiện duy nhất.

## 4. Ca khó

| Ảnh | Tình huống | Quyết định | Lý do |
|---|---|---|---|
| `img_001`* | Tuk-tuk | `can_review` | Xe ba bánh, cần xác nhận Taxonomy |
| `img_002`* | Xe tải chở nhiều ô tô | Đề xuất gán riêng các đối tượng hợp lệ | Tránh bỏ sót xe đang được vận chuyển |
| `img_003`* | Tượng người cưỡi ngựa | Đề xuất không gán | Không phải người và ngựa thật |
| `img_004`* | Xe tạo hình nhân vật | Đề xuất gán `car` nếu là ô tô thật | Phân loại theo định nghĩa đối tượng |
| `img_005`* | Tượng xe pickup | Đề xuất không gán | Không phải phương tiện thật |
| `img_006`* | Ma-nơ-canh | Đề xuất không gán `person` | Không phải con người thật |

\* ID ảnh chỉ là mã minh họa; cần thay bằng tên file thực tế của bộ 40 ảnh.

**Quy tắc xử lý câu hỏi chưa chốt:** Đánh dấu `can_review` (hoặc ghi vào danh sách Review nếu chưa cấu hình trong CVAT), nêu rõ câu hỏi và trình trưởng nhóm. Không tự đoán. Các quyết định đề xuất ở trên chưa trở thành quy định chính thức khi chưa được Calibration xác nhận.

## 5. QC và phiên bản

### Nguyên tắc QC

Kiểm tra theo các tiêu chí:

1. Đúng class.
2. Không bỏ sót đối tượng.
3. Không gán đối tượng ngoài phạm vi.
4. Box ôm sát đối tượng theo Guideline.
5. Thuộc tính đúng quy tắc.
6. Các ca mơ hồ được giải quyết và ghi nhận.

### Đo độ đồng thuận

Các Annotator gán **độc lập** trên cùng một tập ảnh trước khi trao đổi đáp án. So sánh:

- Số lượng đối tượng được phát hiện.
- Class của các đối tượng tương ứng.
- IoU của các Bounding Box tương ứng.
- Các tình huống gây bất đồng.

**Ngưỡng đề xuất để Calibration thảo luận:** IoU ≥ 0,5 cho ghép cặp box; class agreement ≥ 95% trên các cặp đã ghép. Đây **không phải ngưỡng chính thức** của bài tập và chưa đủ để Release nếu chưa kiểm tra bỏ sót và gán thừa. Cần thống nhất thêm cách ghép cặp, số ảnh đánh giá và ngưỡng lỗi trước khi nghiệm thu.

### Quyết định Release / Hold / Rework

- **Release:** Dữ liệu đạt tất cả tiêu chí QC đã được nhóm và giảng viên thống nhất; không còn ca Review chưa được xử lý.
- **Hold:** Còn bất đồng chưa giải quyết hoặc chưa đủ bằng chứng nghiệm thu.
- **Rework:** Có lỗi đã xác định, cần sửa và kiểm tra lại trước khi xét Release.

**Người quyết ca mơ hồ:** Trưởng nhóm/Lead sau Calibration; ca vượt phạm vi Guideline cần xin ý kiến giảng viên hoặc người phụ trách dự án.

### Lịch sử phiên bản

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v1.0 | Xây dựng Guideline cá nhân theo 5 phần | Bài thực hành Day 07 |
| v1.1 (dự kiến) | Cập nhật sau Calibration | Các ca khó được nhóm thống nhất |
| v1.2 (dự kiến) | Cập nhật sau QC | Điều chỉnh từ lỗi thực tế |

---

**Nguyên tắc cá nhân:** Một Guideline tốt không chỉ giúp một người gán nhãn đúng, mà phải giúp nhiều người độc lập thực hiện và tạo ra kết quả thống nhất, có thể kiểm tra và nghiệm thu.
