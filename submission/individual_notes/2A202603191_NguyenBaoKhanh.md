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
- Chưa có `calibration_candidates.csv` (chưa chạy `qc_compare.py`). Cập nhật sau Pha 4.
- Quyết định calibration quan trọng nhất: cập nhật sau họp Pha 5.

## 5. Dùng AI
- **Công cụ:** Claude Code (trợ lý lập trình chạy trong VS Code).
- **Dùng cho việc gì:**
  - Đọc và tóm tắt yêu cầu đề (README, GUIDE, RUBRIC, SUBMISSION), lập danh sách việc cần làm (`TODO.md`).
  - Lập danh sách **tình huống** có trong 40 ảnh (tuk-tuk, xe chở xe, tượng, phản chiếu…) dưới dạng câu hỏi, không chốt quyết định; file để trong `work/`, không nộp.
  - Tạo khung file (`guideline_v1.0_agreed.md`, `guideline_v1.1_calibrated.md`, `QC_REPORT.md`, ghi chú này).
  - Chạy `split_assign.py` chia ảnh, gộp bản cập nhật của repo gốc, đổi tên file sang mã thành viên, commit/push.
- **Không dùng AI để:** vẽ box hay quyết định gán nhãn cho ảnh.
- **Cách tôi kiểm tra lại:**
  - Danh sách tình huống AI đưa ra chỉ dùng để gợi ý; tôi tự mở lại từng ảnh được nhắc tới để xác nhận tình huống có thật, rồi tự quyết định và viết vào `guideline_191.md`.
  - Bảng chia ảnh: kiểm `assignment.csv` có đủ 40 ảnh, mỗi ảnh đúng 3 mã, mỗi người 24 ảnh; khi chạy lại với mã mới, bảng chia vẫn giữ nguyên.
  - Khi đổi sang mã thành viên: rà lại các file trong `submission/` để chắc không còn họ tên, MSHV.
  - Chạy `check_submission.py` sau mỗi lần thay đổi để xem còn thiếu file nào.

## 6. Bài học
- Một điều tôi sẽ làm khác khi viết guideline lần sau: (điền cuối bài)
