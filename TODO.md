# Việc cần làm — Nhóm G-05_t-004_Nhóm kết đoàn

Đánh dấu `[x]` khi xong. Chi tiết lệnh xem [GUIDE.md](GUIDE.md), yêu cầu nộp xem [SUBMISSION.md](SUBMISSION.md).

> Trên Windows chạy script bằng `PYTHONIOENCODING=utf-8 python scripts/...` để tránh lỗi font tiếng Việt.

## Pha 0 · Chuẩn bị
- [x] Fork, clone, đổi tiêu đề `README.md` (đủ 5 MSHV - Họ tên)
- [x] `TEAM.md`: tên nhóm, K=3, danh sách thành viên
- [ ] `TEAM.md`: URL repo, tài khoản GitHub từng người, bảng phân công
- [ ] Kiểm tra tên repo theo mẫu `K5-L23-DAY07-<TenNhom>-GuidelineQC` (hỏi giảng viên); repo **public**
- [ ] Mời 4 bạn làm collaborator; mỗi người đặt `git config user.name/email` đúng tài khoản GitHub

## Pha 1 · Guideline cá nhân (độc lập, không xem bài nhau)
- [x] 2A202603191 - Nguyễn Bảo Khánh: viết guideline
- [x] 2A202603191 - Nguyễn Bảo Khánh: commit + push
- [x] 2A202603103 - Lê Ngọc Dũng: `guideline_2A202603103_LeNgocDung.md`
- [ ] 2A202603201 - Bùi Quang Huy: `guideline_2A202603201_BuiQuangHuy.md`
- [ ] 2A202603245 - Nguyễn Sỹ Kiên: `guideline_2A202603245_NguyenSyKien.md`
- [ ] 2A202603101 - Nguyễn Xuân Anh: `guideline_2A202603101_NguyenXuanAnh.md`

Mỗi file nằm ở `submission/guidelines/individual/` và do chính người đó commit.

## Pha 2 · Họp thống nhất (cả nhóm)
- [ ] So 5 bản cá nhân, ghi các chỗ khác nhau vào mục 0 của `guideline_v1.0_agreed.md`
- [ ] Chốt các câu hỏi chính:
  - [ ] Có thêm lớp `bicycle` / `three_wheeler` / `bus` không?
  - [ ] Xích lô (img_023) thuộc lớp nào?
  - [ ] Xe máy điêu khắc (img_011), xe vàng trên nóc (img_024) có tính là xe thật?
  - [ ] Box ôm phần nhìn thấy hay cả vật (amodal)?
  - [ ] Ngưỡng tách đám đông / dùng `ignore_region`
- [ ] Viết đủ 5 phần `submission/guidelines/guideline_v1.0_agreed.md` (đã có khung)
- [ ] Sửa `submission/taxonomy.json`: `version` → `v1.0`, `classes` + `definition` khớp v1.0. **Không đổi `rules`**
- [ ] `python scripts/make_cvat_labels.py`

## Pha 3 · Chia ảnh và gán nhãn (độc lập, không dùng AI)
- [x] Một người chạy (2A202603191 đã chạy, mỗi người 24 ảnh) — [x] commit `submission/qc/`:
  `python scripts/split_assign.py --members 2A202603191,2A202603103,2A202603201,2A202603245,2A202603101 --copy-images`
- [ ] Mỗi người tạo Project trên CVAT bằng `cvat/labels_generated.json`, upload **đúng** ảnh trong `submission/qc/assignments/<MSHV>.txt`
- [ ] Gán theo v1.0; ca chưa chắc thì tick `can_review`
- [ ] Export COCO 1.0 → `submission/labels/<MSHV>_<HoTen>.json`
  - [ ] 2A202603191_NguyenBaoKhanh.json
  - [ ] 2A202603103_LeNgocDung.json
  - [ ] 2A202603201_BuiQuangHuy.json
  - [ ] 2A202603245_NguyenSyKien.json
  - [ ] 2A202603101_NguyenXuanAnh.json
- [ ] `python scripts/validate_labels.py --all` sạch, rồi mỗi người tự commit

## Pha 4 · Đo IAA
- [ ] `python scripts/qc_compare.py`
- [ ] `python scripts/viz_disagreement.py --images flagged` rồi xem ảnh trong `work/viz/`
- [ ] Đọc `iaa_summary.md`: so ngưỡng IoU 0.85 / đồng thuận lớp 95%, ghi kèm mẫu số
- [ ] Với mỗi chỗ lệch: cả nhóm cùng lệch (guideline mơ hồ) hay một người lệch (chưa nắm quy tắc)?

## Pha 5 · Calibration (cả nhóm)
- [ ] `python scripts/build_final.py` lần đầu để có khung `calibration_decisions.csv`
- [ ] Họp, mỗi quyết định **một dòng**; ảnh nào bị cờ cũng phải có ít nhất 1 dòng
- [ ] Viết `submission/guidelines/guideline_v1.1_calibrated.md` (đã có khung): copy v1.0 rồi thêm quy tắc mới
- [ ] `python scripts/prepare_calibration.py` → trên CVAT sửa các ảnh bị cờ → export ra `submission/qc/labels_calibrated_flagged.json`
- [ ] Chạy `python scripts/build_final.py` lần nữa để ra `labels_calibrated.json/.csv`

## Pha 6 · QC và nộp bài
- [ ] `python scripts/qc_sample.py --n 20 --seed 42`
- [ ] Điền `submission/qc/qc_review.csv`: mỗi lỗi một dòng, ảnh không có lỗi ghi `khong`. Người kiểm nên là người ít gán ảnh đó nhất
- [ ] `python scripts/qc_report.py`
- [ ] Điền đủ 7 mục `submission/qc/QC_REPORT.md` (đã có khung):
  - [ ] Tỉ lệ lỗi theo ảnh và theo vật, kèm mẫu số
  - [ ] Top 3 loại lỗi, mỗi loại có ảnh ví dụ
  - [ ] Quyết định theo cây Hold → Rework → Release
- [ ] Ghi chú cá nhân `submission/individual_notes/<MSHV>_<HoTen>.md` (mẫu: `docs/INDIVIDUAL_NOTE_TEMPLATE.md`)
  - [ ] 2A202603191 (đã có khung, nhớ điền mục 5 "Dùng AI" trung thực)
  - [ ] 2A202603103
  - [ ] 2A202603201
  - [ ] 2A202603245
  - [ ] 2A202603101
- [ ] `TEAM.md`: điền "Kết quả tóm tắt" và hash commit chốt bài
- [ ] `python scripts/check_submission.py` không còn `LỖI`
- [ ] Kiểm tra: cả 5 người đều có commit; không commit thư mục `work/`
- [ ] Push, nộp link repo lên LMS trước hạn
