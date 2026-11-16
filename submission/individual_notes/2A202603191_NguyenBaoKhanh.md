# Ghi chú cá nhân — 2A202603191 - Nguyễn Bảo Khánh

## 1. Pha 1: ba ca khó tôi tìm ra trong 40 ảnh
| Ảnh | v0 không trả lời được gì | Quyết định ban đầu của tôi |
|---|---|---|
| img_004, img_016, img_035 | Tuk-tuk / auto-rickshaw không thuộc lớp nào trong v0 (car, truck, van, motorcycle, person) | Đề xuất lớp mới `three_wheeler`; nếu nhóm không thêm thì gán `motorcycle` |
| img_002 | Xe đầu kéo chở 8–9 ô tô: gán mấy box, ô tô được chở có gán không | 1 box `truck` cho đầu kéo + rơ-moóc; mỗi ô tô thấy ≥ 30% thân thì một box `car`, `occluded_level` theo khung sắt che |
| img_017, img_027, img_033 | v0 không nói gì về tượng, bia mộ hình xe, ma-nơ-canh | Không gán, để thành mẫu âm |

## 2. Pha 2: chỗ tôi bất đồng với nhóm

So bản cá nhân `guideline_2A202603191_NguyenBaoKhanh.md` với kết quả nhóm chốt trong `guideline_v1.0_agreed.md` (mục 0):

| # (mục 0 v1.0) | Vấn đề | Đề xuất ban đầu của tôi | Nhóm chốt (v1.0) | Tôi đồng ý? Vì sao |
|---|---|---|---|---|
| #1 | Thêm lớp | Thêm `bicycle`, `three_wheeler`, `bus` | Thêm cả 3 | Đồng ý, trùng đề xuất; 4/5 bản cùng hướng |
| #2 | Xích lô (img_023) | `three_wheeler` | `three_wheeler` | Đồng ý, trùng đề xuất |
| #4 | Ngưỡng `occluded_level` | <10 / 10–40 / 40–70 / ≥70 | <10 / 10–<40 / 40–<70 / ≥70 | Đồng ý, nhóm giữ đúng các mốc của tôi |
| #5 | Vật nhỏ, đám đông | <20 px không vẽ; ≥ 5 vật chồng > 50% → một `ignore_region` | R-03 (cụm ≥ 3 vật < 20 px → ignore) + R-04 (≥ 5 vật chồng > 50% → ignore) | Đồng ý; R-03 bổ sung ca cụm vật nhỏ mà bản tôi chưa nói rõ |
| #6 | Tủ đồ chơi img_038 | Không gán | Không gán, không ignore | Đồng ý; nhóm hòa 2–2, tôi (leader) quyết vì đồ chơi thu nhỏ là mẫu âm |
| #10 | Predator img_011 | Không gán (tượng) | Không gán (tượng) | Đồng ý; hòa 2–2, tôi (leader) quyết vì kích thước lớn hơn người thật, đứng yên như vật trưng bày |
| #11, #15 | Xe máy kim loại img_011 | `motorcycle` + `can_review` | `motorcycle` + `drivable=no` + `can_review` | Đồng ý; tôi đề xuất thêm thuộc tính `drivable` để ghi nhận xe không chạy được thay vì tranh luận thật/giả |
| #12, #15 | Xe vàng img_024 | `car` + `can_review` | `car` + `drivable=no` | Đồng ý, cùng lý do với thuộc tính `drivable` |
| #15 | Bia mộ hình pickup img_027 | Không gán (tượng) | `truck` + `drivable=no` (R-14) | Đồng ý, đổi ý so với bản cá nhân: vật hình xe cỡ thật vẫn có ích cho mô hình nếu đánh dấu `drivable=no` để lọc; đồ chơi thu nhỏ vẫn không gán |
| — | Box phần nhìn thấy hay amodal | Phần nhìn thấy | Phần nhìn thấy (R-01) | Đồng ý, trùng đề xuất |

## 3. Pha 3: ảnh tôi đã gán
- Số ảnh được giao: **24** (`submission/qc/assignments/2A202603191.txt`: img_001, 003, 004, 006, 008, 010, 012, 013, 015, 016, 019, 020, 021, 022, 025, 026, 029, 030, 031, 033, 035, 036, 038, 039)
- Số box đã vẽ: **190** (person 66, car 53, motorcycle 35, ignore_region 13, bicycle 11, three_wheeler 6, truck 3, bus 2, van 1) · Số lần tick `can_review`: **0**
- File nhãn: `submission/labels/2A202603191_NguyenBaoKhanh.json`, `validate_labels.py` báo hợp lệ, đủ 24 ảnh.
- Ca lưỡng lự nhất khi gán: **img_038** (tủ kính xe mô hình). Theo v1.0 lúc đầu, đồ chơi thu nhỏ không gán; tôi cho rằng mô hình nên học nhận ra hình xe nhưng biết xe đó không chạy được, nên gán 25 xe mô hình là `car` + `drivable=no` và đề xuất nhóm mở rộng R-14 cho cả đồ chơi. Nhóm đã cập nhật quy tắc này trong v1.0.

## 4. Pha 4–5: tôi lệch ở đâu

- Số ca trong `calibration_candidates.csv` có tên tôi: **688** (bất đồng ignore 194, bỏ sót/thừa 179, lệch occluded_level 125, IoU thấp 92, lệch truncated 54, lệch drivable 32, sai lớp 12).
- Đồng thuận của tôi với các bạn (gộp các cặp có tôi, 327 cặp box ghép): IoU TB 0.861, cùng lớp 96.3%, cùng `occluded_level` 61.8%.
- Ảnh tôi lệch nhiều nhất: img_021.jpg (78 ca), img_003.jpg (75 ca), img_006.jpg (66 ca).
- Nguyên nhân:
  - lệch `occluded_level` (125 ca) và bất đồng vùng ignore (194 ca): **guideline mơ hồ**, cả nhóm cùng lệch vì ước lượng % che và ranh giới vùng dày bằng mắt; v1.1 thêm R-15 để chốt cách ước lượng
  - lệch `drivable` (32 ca): chủ yếu do quy tắc đồ chơi/xe không chạy được (R-14) và lỗi `person` của một người khác
  - bỏ sót/thừa (179 ca) và IoU thấp (92 ca): mỗi người dừng vẽ ở ngưỡng vật nhỏ khác nhau và ôm box chưa đều (R-01, R-03)
- Phân tích riêng của tôi:
  - IoU theo cặp có tôi: với 2A202603245 0.891, 2A202603103 0.857, 2A202603201 0.853 (đạt ngưỡng 0.85), với 2A202603101 0.844. Box tôi vẽ ôm vật khá sát; IoU chung 0.845 bị kéo xuống chủ yếu ở các cặp không có tôi (ví dụ 2A202603101|2A202603103 là 0.815).
  - Điểm yếu chính là **bỏ sót**: 154 box người khác vẽ mà tôi không vẽ, chỉ 25 box ngược lại. Tập trung ở img_021 (28), img_006 (22), img_031 (18), img_035 (14): vật nhỏ, ở xa, trong bóng tối hoặc người đu bám xe.
  - Tôi khoanh `ignore_region` khác các bạn ở ảnh đông: img_003 (52 ca), img_008 (40), img_021 (35). Số vùng ignore của tôi (13) tương đương các bạn (10–13), nhưng vị trí và kích thước vùng không trùng: chỗ tôi khoanh ignore thì có bạn vẽ box riêng, và ngược lại.
  - Nguyên nhân: mỗi người tự quyết ranh giới "vùng dày" theo R-03/R-04 khác nhau, vì guideline chưa có ví dụ ranh giới cụ thể; tôi cũng dừng vẽ vật nhỏ, xa sớm hơn các bạn. Lần sau tôi sẽ vẽ từng vật còn tách được trước, chỉ gộp ignore phần thật sự không tách được.
- Quyết định calibration quan trọng nhất với tôi: R-15 (cách ước lượng `occluded_level`, phân vân chọn mức thấp hơn), vì đây là nhóm ca lệch nhiều nhất (427 ca toàn nhóm).

## 5. Dùng AI
- **Công cụ:** Claude Code (trợ lý lập trình chạy trong VS Code).
- **Dùng cho việc gì:**
  - Đọc và tóm tắt yêu cầu đề (README, GUIDE, RUBRIC, SUBMISSION), lập danh sách việc cần làm (`TODO.md`).
  - Lập danh sách **tình huống** có trong 40 ảnh (tuk-tuk, xe chở xe, tượng, phản chiếu…) dưới dạng câu hỏi, không chốt quyết định; file để trong `work/`, không nộp.
  - Tạo khung file (`guideline_v1.0_agreed.md`, `guideline_v1.1_calibrated.md`, `QC_REPORT.md`, ghi chú này).
  - Chạy `split_assign.py` chia ảnh, gộp bản cập nhật của repo gốc, đổi tên file sang `<MSHV>_<HoTen>`, sửa script cho CVAT và cho định dạng MSHV, commit/push.
  - Tổng hợp 5 bản cá nhân thành bản nháp `guideline_v1.0_agreed.md`; soạn `guideline_v1.1_calibrated.md`, `guideline_change.json` và 1781 dòng `calibration_decisions.csv` (gộp theo loại lỗi, theo quyết định tôi chốt với vai trò leader).
  - Chạy `qc_compare.py`, `build_final.py`, `qc_sample.py`, `qc_report.py`; áp tự động R-17 (`person` → `drivable=yes`) và bỏ cờ `can_review` trên nhãn đồng thuận; soạn `QC_REPORT.md` và ghi chú cá nhân theo số liệu thật.
  - Ghi phiếu `qc_review.csv` vòng 1 và 2 theo kết quả duyệt tôi xác nhận (40/40 ảnh không lỗi).
  - Đối chiếu file nhãn của nhóm với guideline để tìm lỗi thuộc tính (img_027, img_038, `person` có `drivable=no`).
- **Không dùng AI để:** vẽ box, quyết định gán nhãn cho từng ảnh hay duyệt ảnh QC thay tôi.
- **Cách tôi kiểm tra lại:**
  - Danh sách tình huống AI đưa ra chỉ dùng để gợi ý; tôi tự mở lại từng ảnh được nhắc tới để xác nhận tình huống có thật, rồi tự quyết định và viết vào `guideline_191.md`.
  - Bảng chia ảnh: kiểm `assignment.csv` có đủ 40 ảnh, mỗi ảnh đúng 3 mã, mỗi người 24 ảnh; khi chạy lại với mã mới, bảng chia vẫn giữ nguyên.
  - Khi đổi sang mã thành viên: rà lại các file trong `submission/` để chắc không còn họ tên, MSHV.
  - Chạy `check_submission.py` sau mỗi lần thay đổi để xem còn thiếu file nào.

## 6. Bài học
- Một điều tôi sẽ làm khác khi viết guideline lần sau: chạy thử guideline trên 3–4 ảnh khó (img_003, img_006, img_021) cho cả nhóm gán thử trước khi khóa v1.0. IAA cho thấy chỉ 54.5% cặp box cùng `occluded_level` và 347 ca bất đồng vùng ignore: các ngưỡng % và ranh giới vùng dày cần ví dụ minh họa cụ thể ngay trong v1.0, không đợi tới calibration.
- Tôi cũng sẽ không đổi quy tắc giữa Pha 3 (như quy tắc đồ chơi img_038): nên tick `can_review` và đưa ra calibration để giữ IAA có ý nghĩa.
