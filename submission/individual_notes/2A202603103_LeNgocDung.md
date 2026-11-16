# Ghi chú cá nhân — Day 07 — 2A202603103 - Lê Ngọc Dũng

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
| Tuk-tuk | Ưu tiên class riêng nếu được phê duyệt; không ép vào class khác khi thiếu quy tắc. | Có cho bổ sung `three_wheeler` không? Nếu không, ánh xạ vào class nào hay bỏ qua? | Nhóm chốt `three_wheeler` gồm tuk-tuk, auto-rickshaw, xích lô (v1.0, quyết định #1, #2) |
| Xe chở ô tô | Phân biệt phương tiện chở và từng xe được chở; gán riêng nếu hợp lệ. | Quy tắc xe chuyên chở, rơ-moóc, mức độ nhìn thấy tối thiểu và box phần bị che là gì? | Một box `truck` cho đầu kéo + rơ-moóc, mỗi ô tô được chở một box `car` (R-08) |
| Tượng / ma-nơ-canh | Không gán như người hay xe thật. | Các bản sao, tượng, đồ chơi, hình in, phản chiếu có bị loại trừ hết không? | Tượng người, ma-nơ-canh, hình in, phản chiếu không gán (R-05, R-06); vật hình xe không chạy được, kể cả đồ chơi, gán lớp xe + `drivable=no` (R-14) |

**Tôi đồng ý / chưa đồng ý vì sao:** Đồng ý các kết quả trên; chi tiết từng điểm ở bảng "Kết quả nhóm chốt" bên dưới. Khi thảo luận xong, tôi sẽ ghi quyết định cuối, người xác nhận và lý do thay vì chỉ ghi “nhóm đã thống nhất”.

### Kết quả nhóm chốt (cập nhật sau họp Pha 2)

So bản cá nhân với kết quả nhóm chốt trong `guideline_v1.0_agreed.md` (mục 0). Cả nhóm đã thống nhất các điểm dưới trong họp Pha 2.

| # (mục 0 v1.0) | Vấn đề | Đề xuất ban đầu của tôi | Nhóm chốt (v1.0) | Tôi đồng ý? Vì sao |
|---|---|---|---|---|
| #4 | Ngưỡng `occluded_level` | 0 / <30 / 30–70 / ≥70 | <10 / 10–<40 / 40–<70 / ≥70 | Đồng ý; dùng mốc chung trùng nhiều bản nhất để cả nhóm đo giống nhau |
| #8 | Người trong cabin kín | Gán nếu thấy rõ qua kính | Không gán người sau kính; xe hở vẫn gán | Đồng ý; người sau kính khó vẽ box nhất quán, xe hở vẫn gán người |
| #10 | Predator img_011 | Không gán hình nhân vật | Không gán (tượng) | Đồng ý; Predator lớn hơn người thật, đứng yên như vật trưng bày nên coi là tượng |
| #11, #12 | Xe máy kim loại img_011, xe vàng img_024 | Ghi ca cần họp / review | Gán lớp xe + `drivable=no` | Đồng ý; dùng `drivable=no` ghi nhận xe không chạy được, tránh tranh luận thật/giả |
| #15 | Bia mộ img_027 | Không gán | `truck` + `drivable=no` (R-14) | Đồng ý; vật hình xe cỡ thật vẫn có ích nếu đánh `drivable=no` để lọc khi huấn luyện |
| #16 | Hàng chất trên xe (img_001, img_012, img_035) | Box chỉ bao thân xe | Box bao cả hàng chất cố định (R-09) | Đồng ý; đa số 4/5, hàng chất cố định là kích thước xe chiếm trên đường |

## 3. Pha 3: ảnh tôi đã gán

- Số ảnh được giao: **24** (`submission/qc/assignments/2A202603103.txt`); file nhãn `submission/labels/2A202603103_LeNgocDung.json`, `validate_labels.py` báo hợp lệ, đủ 24 ảnh.
- Số box đã vẽ: **260** (person 115, motorcycle 65, car 41, ignore_region 11, bus 9, bicycle 8, truck 6, three_wheeler 5) · Số lần tick `can_review`: **3**
- Ca lưỡng lự nhất khi gán: **img_013.jpg** (ảnh tôi tick `can_review` nhiều nhất: 2 box).

## 4. Pha 4–5: tôi lệch ở đâu

- Số ca trong `calibration_candidates.csv` có tên tôi: **663** (bỏ sót/thừa 172, lệch occluded_level 167, bất đồng ignore 122, IoU thấp 117, lệch truncated 38, sai lớp 26, lệch drivable 18, can_review 3).
- Đồng thuận của tôi với các bạn (gộp các cặp có tôi, 340 cặp box ghép): IoU TB 0.838, cùng lớp 92.4%, cùng `occluded_level` 50.9%.
- Ảnh tôi lệch nhiều nhất: img_006.jpg (85 ca), img_003.jpg (80 ca), img_032.jpg (77 ca).
- Nguyên nhân:
  - lệch `occluded_level` (167 ca) và bất đồng vùng ignore (122 ca): **guideline mơ hồ**, cả nhóm cùng lệch vì ước lượng % che và ranh giới vùng dày bằng mắt; v1.1 thêm R-15 để chốt cách ước lượng
  - lệch `drivable` (18 ca): chủ yếu do quy tắc đồ chơi/xe không chạy được (R-14) và lỗi `person` của một người khác
  - bỏ sót/thừa (172 ca) và IoU thấp (117 ca): mỗi người dừng vẽ ở ngưỡng vật nhỏ khác nhau và ôm box chưa đều (R-01, R-03)
- Quyết định calibration quan trọng nhất với tôi: R-15 (cách ước lượng `occluded_level`, phân vân chọn mức thấp hơn), vì đây là nhóm ca lệch nhiều nhất (427 ca toàn nhóm).

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
