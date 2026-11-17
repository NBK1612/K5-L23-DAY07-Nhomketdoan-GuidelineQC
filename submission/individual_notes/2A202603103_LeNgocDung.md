# Ghi chú cá nhân — Day 07 — 2A202603103 - Lê Ngọc Dũng

- **Ngày:** 16/11/2026
- **Bài thực hành:** Viết lại guideline và gán Bounding Box trên CVAT — 40 ảnh đường phố châu Á
- **Trạng thái:** Đã có file nhãn cá nhân và đã kiểm tra cấu trúc; nội dung IAA/calibration chờ đối chiếu nguồn.

## 1. Pha 1: ba ca khó tôi tìm ra trong 40 ảnh

| Ảnh | v0 không trả lời được gì | Quyết định ban đầu của tôi |
|---|---|---|
| `img_004`, `img_016`, `img_035` | Tuk-tuk / auto-rickshaw ba bánh không khớp rõ với danh sách lớp v0 (`car`, `truck`, `van`, `motorcycle`, `person`). | Tôi **đề xuất** thêm lớp `three_wheeler`. Nếu không được phép đổi taxonomy, tôi không tự mặc định `motorcycle` mà đánh dấu `can_review`/ghi danh sách review để nhóm chốt cách ánh xạ. |
| `img_002` | Xe chuyên chở nhiều ô tô: xe vận chuyển có phải một box hay nhiều box? Các ô tô trên xe có gán riêng? Phần bị khung thép che xử lý thế nào? | Tôi **đề xuất** một box `truck` cho toàn bộ tổ hợp xe vận chuyển trong vùng thấy được và box `car` riêng cho từng ô tô nhận diện được. Mốc “thấy ≥ 30% thân xe” trong template chỉ là **đề xuất cần chốt**; chưa dùng làm quy định bắt buộc. |
| `img_017`, `img_027`, `img_033` | Tượng người cưỡi ngựa, tượng/bia mộ hình pickup, ma-nơ-canh có được coi là đối tượng thật hay không? | Tôi **đề xuất không gán** các đối tượng mô phỏng vật thể thật, giữ như mẫu âm; xác nhận với nhóm vì v0 chưa nói rõ. |

**Tôi rút ra ở pha 1:** Vấn đề không chỉ là vẽ box đẹp mà là định nghĩa đúng **đối tượng nào được gán, gán class nào và khi nào cần hỏi lại**. Nếu hai annotator hiểu khác nhau ở đây, dữ liệu sẽ không thống nhất dù cả hai đều thao tác CVAT thành thạo.

## 2. Pha 2: chỗ tôi bất đồng với nhóm

| Vấn đề | Quan điểm ban đầu của tôi | Nhóm chốt | Tôi đồng ý vì |
|---|---|---|---|
| Mức che khuất | `0`: không che; `1`: trên 0–<30%; `2`: 30–<70%; `3`: ≥70%. | <10% / 10–<40% / 40–<70% / ≥70%. | Có mốc chung để mọi người áp dụng thống nhất. |
| Người trong cabin kín | Gán nếu nhìn thấy đủ rõ qua kính. | Không gán người chỉ thấy qua kính xe kín; khoang hở theo R-11. | Box người sau kính khó xác định nhất quán. |
| Predator `img_011` | Không gán hình nhân vật. | Không gán, coi là tượng. | Tuân theo quyết định riêng của nhóm cho ca này. |
| Xe điêu khắc `img_011`, xe vàng `img_024` | Để ca cần họp/review. | Gán lớp xe và `drivable=no`; xe điêu khắc `img_011` còn cần `can_review`. | Ghi nhận vật hình xe theo phạm vi nhóm đã chọn, phân biệt bằng thuộc tính. |
| Bia mộ hình pickup `img_027` | Không gán. | `truck`, `drivable=no`, theo R-14. | Cần áp dụng nhất quán quy tắc vật hình xe cỡ thật của nhóm. |
| Hàng chất cố định trên xe | Box chỉ bao thân xe. | Bao cả hàng cố định theo R-09; không bao ống do người cầm ở `img_029`. | Ranh giới box phản ánh kích thước xe và khối hàng chiếm trên đường. |

Khi gán Pha 3, tôi cần theo bản nhóm đã khóa, không tiếp tục dùng các đề xuất cá nhân đã bị thay thế.


## 3. Pha 3: ảnh tôi đã gán
Số liệu đối chiếu trực tiếp từ `submission/labels/2A202603103_LeNgocDung.json`:

- **24 ảnh**, đúng danh sách được phân công.
- **260 annotation dạng box:** 249 box đối tượng và 11 vùng `ignore_region`.
- **3 box đang bật `can_review`:** hai box `bus` ở `img_013` — ID COCO 117, 118; một box `person` sát mép trái ở `img_029` — ID 186.
- Đây là số cờ còn bật trong bản xuất hiện tại, không phải tổng số lần tôi đã bấm review.

| Ảnh | Minh chứng trong file nhãn |
|---|---|
| `img_004` | 2 box `three_wheeler` và 2 box `person`, tách người với xe. |
| `img_027` | 1 box `truck`, `drivable=no`, khác quan điểm ban đầu không gán. |
| `img_035` | Có 13 box; box `three_wheeler` bao cả khối hàng, cao khoảng 383 px. |
| `img_013` | Hai box `bus` cao khoảng 26 px đang bật review; cần kiểm lại lớp trong cảnh tối. |

**Các điểm còn cần rà:** `img_029` chưa có box lớp `bicycle` dù ảnh có xe đạp; cần xác định bỏ sót hay chọn nhầm lớp. `img_011` có ba box `motorcycle`, hai box `drivable=no`, một box `drivable=yes`, cả ba chưa bật review; cần đối chiếu từng box với quyết định nhóm.

Ngày 17/11/2026, chạy:

```powershell
python scripts/validate_labels.py submission/labels/2A202603103_LeNgocDung.json
```

Kết quả: **hợp lệ về cấu trúc, 24 ảnh, không có cảnh báo**. Kết quả này chưa xác nhận mọi box đúng vật, ôm sát hoặc không bỏ sót. ID COCO có thể khác ID trên CVAT.


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
- **Tự kiểm tra:** Tôi đọc kỹ lại báo cáo và bổ sung, xoá, chỉnh sửa những nội dung AI viết chưa đúng ý và chưa rõ ràng. Tổng quan lại lần nữa và hoàn thiện

## 6. Bài học

Điều tôi sẽ làm khác khi viết guideline lần sau: **viết quy tắc quyết định cho các ca biên ngay từ đầu, kèm ví dụ dương/âm, rồi cho ít nhất hai người gán độc lập một tập ảnh mẫu trước khi triển khai toàn bộ.**

Tôi muốn mỗi quy tắc trả lời được bốn câu hỏi: **Có gán không? Gán class nào? Vẽ box tới đâu? Trường hợp nào phải đưa lên review?**

Nếu nhóm còn diễn giải một quy tắc theo hai cách, guideline đó chưa đủ rõ để Release. Quyết định cuối phải dựa trên bằng chứng đo đồng thuận và QC, không chỉ dựa trên cảm giác rằng các thành viên “đã hiểu giống nhau”.
