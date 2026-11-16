# Ghi chú cá nhân — Day 07 — 2A202603103 - LeNgocDung

- **Ngày:** 16/11/2026
- **Bài thực hành:** Viết lại guideline và gán Bounding Box trên CVAT — 40 ảnh đường phố châu Á
- **Trạng thái:** Bản ghi chú cá nhân chuẩn bị / cần cập nhật sau thực hành

> **Lưu ý về tính xác thực:** Tôi viết phần nhận định và đề xuất ban đầu dựa trên đề bài và các ca trong template được cung cấp. Những số liệu, bất đồng, kết quả calibration và việc dùng công cụ thực tế **chưa được cung cấp**, nên tôi ghi rõ là chưa xác nhận. Trước khi nộp, tôi sẽ thay nội dung này bằng ghi nhận của chính mình. Các ID ảnh dưới đây là ID nêu trong template, cần kiểm tra lại trong bộ ảnh được giao.

## 1. Pha 1: ba ca khó tôi tìm ra trong 40 ảnh

| Ảnh | v0 không trả lời được gì | Quyết định ban đầu của tôi |
|---|---|---|
| `img_004`, `img_016`, `img_035` | Tuk-tuk / auto-rickshaw ba bánh không khớp rõ với danh sách lớp v0 (`car`, `truck`, `van`, `motorcycle`, `person`). | Tôi **đề xuất** thêm lớp `three_wheeler`. Nếu không được phép đổi taxonomy, tôi không tự mặc định `motorcycle` mà đánh dấu `can_review`/ghi danh sách review để nhóm chốt cách ánh xạ. |
| `img_002` | Xe chuyên chở nhiều ô tô: xe vận chuyển có phải một box hay nhiều box? Các ô tô trên xe có gán riêng? Phần bị khung thép che xử lý thế nào? | Tôi **đề xuất** một box `truck` cho toàn bộ tổ hợp xe vận chuyển trong vùng thấy được và box `car` riêng cho từng ô tô nhận diện được. Mốc “thấy ≥ 30% thân xe” trong template chỉ là **đề xuất cần chốt**; chưa dùng làm quy định bắt buộc. |
| `img_017`, `img_027`, `img_033` | Tượng người cưỡi ngựa, tượng/bia mộ hình pickup, ma-nơ-canh có được coi là đối tượng thật hay không? | Tôi **đề xuất không gán** các đối tượng mô phỏng vật thể thật, giữ như mẫu âm; xác nhận với nhóm vì v0 chưa nói rõ. |

**Tôi rút ra ở pha 1:** Vấn đề không chỉ là vẽ box đẹp mà là định nghĩa đúng **đối tượng nào được gán, gán class nào và khi nào cần hỏi lại**. Nếu hai annotator hiểu khác nhau ở đây, dữ liệu sẽ không thống nhất dù cả hai đều thao tác CVAT thành thạo.

## 2. Pha 2: chỗ tôi bất đồng với nhóm

**Chưa có biên bản thảo luận nhóm.** Đây là ba điểm tôi sẽ đưa ra để so sánh quan điểm trong buổi thống nhất guideline:

| Chủ đề cần đối chiếu | Quan điểm ban đầu của tôi | Câu hỏi nhóm phải chốt | Kết quả thực tế |
|---|---|---|---|
| Tuk-tuk | Ưu tiên class riêng nếu được phê duyệt; không ép vào class khác khi thiếu quy tắc. | Có cho bổ sung `three_wheeler` không? Nếu không, ánh xạ vào class nào hay bỏ qua? | **Chưa cập nhật** |
| Xe chở ô tô | Phân biệt phương tiện chở và từng xe được chở; gán riêng nếu hợp lệ. | Quy tắc xe chuyên chở, rơ-moóc, mức độ nhìn thấy tối thiểu và box phần bị che là gì? | **Chưa cập nhật** |
| Tượng / ma-nơ-canh | Không gán như người hay xe thật. | Các bản sao, tượng, đồ chơi, hình in, phản chiếu có bị loại trừ hết không? | **Chưa cập nhật** |

**Tôi đồng ý / chưa đồng ý vì sao:** Chưa thể ghi nhận khi chưa có quyết định nhóm. Khi thảo luận xong, tôi sẽ ghi quyết định cuối, người xác nhận và lý do thay vì chỉ ghi “nhóm đã thống nhất”.

## 3. Pha 3: ảnh tôi đã gán

- **Số ảnh được giao:** Chưa cập nhật.
- **Số ảnh đã hoàn thành:** Chưa cập nhật.
- **Số box đã vẽ:** Chưa cập nhật.
- **Số lần đánh dấu `can_review`:** Chưa cập nhật (nếu CVAT không có thuộc tính này, ghi số ca đưa vào danh sách review riêng).

**Ca tôi dự kiến phải kiểm tra kỹ nhất:** `img_002` — xe tải chở nhiều ô tô. Tôi cần phân biệt box cho xe chở, box từng xe con, phần bị khuất bởi khung vận chuyển và nguy cơ bỏ sót xe nằm ở tầng trên. Đây mới là **dự kiến**, không phải ca tôi đã thực sự lưỡng lự khi gán.

**Sau khi gán xong, tôi sẽ điền:** `[ID ảnh thực tế] – [Tôi lưỡng lự ở đâu] – [Tôi đã gán thế nào] – [Tôi có đánh dấu review không]`.

## 4. Pha 4–5: tôi lệch ở đâu

**Tệp đối chiếu:** `calibration_candidates.csv` — **chưa được cung cấp**, nên tôi chưa xác định được ca nào có tên mình và nguyên nhân sai khác thực tế.

Tôi sẽ phân loại bất đồng theo hai nguyên nhân:

1. **Guideline mơ hồ / thiếu quy tắc:** Ví dụ không có class dành cho tuk-tuk, chưa quy định tượng và ma-nơ-canh, hoặc chưa chốt cách vẽ box khi bị che.
2. **Tôi áp dụng sai quy tắc đã có:** Ví dụ guideline đã quy định rõ nhưng tôi chọn nhầm class, bỏ sót box hoặc không đánh dấu review.

**Quyết định calibration tôi cho là quan trọng nhất (đề xuất trước buổi họp):** Thống nhất cách xử lý tuk-tuk và những đối tượng trông giống vật thật nhưng không phải vật thật. Hai tình huống này làm thay đổi trực tiếp tập đối tượng được gán và kết quả đo đồng thuận.

**Cần cập nhật từ dữ liệu thực:** `[ID ảnh] – [Quy tắc gốc] – [Tôi đã làm] – [Người khác đã làm] – [Nguyên nhân lệch] – [Quyết định calibration]`.

## 5. Dùng AI

**Cách tôi sử dụng AI để chuẩn bị bản ghi chú này:**

- **Công cụ:** ChatGPT.
- **Mục đích:** Đọc cấu trúc template, hệ thống hóa ba ca khó, diễn đạt quyết định ban đầu dưới dạng kiểm tra được và lập danh sách các bằng chứng cần ghi sau khi thực hành.
- **Kiểm tra kết quả:** Tôi đối chiếu các ca và ID ảnh với template; giữ các quyết định chưa được nhóm xác nhận ở trạng thái **đề xuất**; không điền số lượng box, mức đồng thuận hay quyết định Release/Hold/Rework khi chưa có dữ liệu.
- **Ranh giới sử dụng:** AI hỗ trợ viết và rà soát tài liệu; không thay thế thao tác gán nhãn thực tế, quyết định nhóm hay kiểm tra từng ảnh.

**Lưu ý khi nộp:** Chỉnh mục này theo đúng công cụ và cách kiểm tra mà chính tôi thực sự đã dùng.

## 6. Bài học

Điều tôi sẽ làm khác khi viết guideline lần sau: **viết quy tắc quyết định cho các ca biên ngay từ đầu, kèm ví dụ dương/âm, rồi cho ít nhất hai người gán độc lập một tập ảnh mẫu trước khi triển khai toàn bộ.**

Tôi muốn mỗi quy tắc trả lời được bốn câu hỏi: **Có gán không? Gán class nào? Vẽ box tới đâu? Trường hợp nào phải đưa lên review?**

Nếu nhóm còn diễn giải một quy tắc theo hai cách, guideline đó chưa đủ rõ để Release. Quyết định cuối phải dựa trên bằng chứng đo đồng thuận và QC, không chỉ dựa trên cảm giác rằng các thành viên “đã hiểu giống nhau”.
