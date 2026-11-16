# Việc cần làm — Nhóm Kết Đoàn

Đánh dấu `[x]` khi xong. Chi tiết lệnh xem [GUIDE.md](GUIDE.md), gói nộp xem [SUBMISSION.md](SUBMISSION.md).

> **Quy ước mới của repo gốc:** chỉ dùng **mã thành viên 3 chữ số** trong tên file, Task CVAT, README, TEAM.md.
> Không ghi họ tên, MSHV, email, tài khoản. Mã của nhóm: **101, 103, 191, 201, 245**.
>
> Trên Windows chạy script bằng `PYTHONIOENCODING=utf-8 python scripts/...` để tránh lỗi font tiếng Việt.
> Lưu file `.md` dạng **UTF-8** (không dùng UTF-16), nếu không script đọc lỗi.

## Pha 0 · Chuẩn bị
- [x] `TEAM.md`: tên nhóm, mã thành viên, người điều phối (191)
- [ ] `TEAM.md`: người review bản cuối, phân công của 201 và 245
- [ ] Repo nộp chuyển **riêng tư** (Settings → Danger Zone → Change visibility → Private), cấp quyền cho coach
- [ ] Tên repo/gói: `K5-L23-DAY07-<TenNhom>-GuidelineQC` (hỏi coach)
- [ ] `python -m pip install -r requirements.txt` (bản mới có thêm thư viện)

## Pha 1 · Guideline cá nhân (độc lập)
File: `submission/guidelines/individual/guideline_<mã>.md`
- [x] 191 — `guideline_191.md`
- [x] 101 — `guideline_101.md`
- [ ] 103 — `guideline_103.md`
- [ ] 201 — `guideline_201.md`
- [ ] 245 — `guideline_245.md`

## Pha 2 · Họp thống nhất (cả nhóm)
- [ ] So 5 bản cá nhân, ghi các chỗ khác nhau vào mục 0 của `guideline_v1.0_agreed.md`
- [ ] Chốt các câu hỏi chính:
  - [ ] Có thêm lớp mới không (`bicycle`, `three_wheeler`/`tuk_tuk`, `cyclo`, `bus`)?
  - [ ] Xích lô (img_023) thuộc lớp nào?
  - [ ] Xe máy điêu khắc (img_011), xe vàng trên nóc (img_024) có tính là xe thật?
  - [ ] Người đi xe máy: mấy box, mỗi box bao phần nào?
  - [ ] Box ôm phần nhìn thấy hay cả vật (amodal)?
  - [ ] Ngưỡng tách đám đông / dùng `ignore_region`
- [ ] Viết `submission/guidelines/guideline_v1.0_agreed.md`, gắn mã `R-01`, `R-02`… cho từng quy tắc
- [ ] Sửa `submission/taxonomy.json`: `version` → `v1.0`, `classes` khớp v1.0. **Giữ nguyên `rules`**
- [ ] `python scripts/make_cvat_labels.py` (kiểm schema + sinh `cvat/labels_generated.json`)
- [ ] **Khoá** v1.0 và taxonomy cho lượt gán

## Pha 3 · Gán nhãn (độc lập, người thật vẽ box)
- [x] Chia ảnh (mỗi người 24 ảnh, mỗi ảnh 3 người):
  `python scripts/split_assign.py --members 191,103,201,245,101 --copy-images`
  (4 bạn kia `git pull` rồi chạy lại đúng lệnh này để có `work/<mã>/images/`)
- [ ] Mỗi người tạo Project/Task CVAT tên theo mã, nhãn từ `cvat/labels_generated.json`, upload `work/<mã>/images/`
- [ ] Gán theo v1.0; chưa chắc thì tick `can_review`; ảnh chưa vẽ được thì ghi lại để đưa vào `submission/qc/image_issues.csv` (cột `image,annotator,region,note`)
- [ ] Export COCO 1.0 → `submission/labels/<mã>.json`, rồi `python scripts/validate_labels.py submission/labels/<mã>.json`
  - [ ] 191.json
  - [ ] 103.json
  - [ ] 201.json
  - [ ] 245.json
  - [ ] 101.json

## Pha 4 · Đo IAA
- [ ] `python scripts/validate_labels.py --all`
- [ ] `python scripts/qc_compare.py`
- [ ] `python scripts/viz_disagreement.py --images flagged` rồi xem `work/viz/`
- [ ] Đọc `iaa_summary.md`: mẫu số, box chưa ghép, ca cả hai cùng lệch / một người lệch / **cùng sai**

## Pha 5 · Calibration (cả nhóm)
- [ ] `python scripts/build_final.py` để sinh `calibration_decisions.csv`
- [ ] Mỗi ca một dòng: `quyet_dinh`, `quy_tac_ghi_vao_guideline` (R-xx), `phien_ban_guideline`, `nguoi_chot` (mã), `phieu_sua`, `kiem_lai`, `pham_vi` (`anh`/`lo`)
- [ ] Viết `guideline_v1.1_calibrated.md` + `submission/guideline_change.json` (`kind`: `editorial` hoặc `semantic`)
- [ ] Nếu đổi taxonomy: `submission/taxonomy_v1.1.json` rồi `python scripts/make_cvat_labels.py --final`
- [ ] `python scripts/prepare_calibration.py` → sửa trên CVAT → lưu COCO đúng đường dẫn lệnh in ra
- [ ] `python scripts/build_final.py` lần nữa → `labels_calibrated.json/.csv`, `batch_manifest.json`

## Pha 6 · QC và bàn giao
- [ ] `python scripts/qc_sample.py --round 1` (mặc định duyệt **cả lô**)
- [ ] Mở `work/review/round-01/`, điền `submission/qc/reviews/round-01/qc_review.csv` (người kiểm khác người sửa)
- [ ] `python scripts/qc_report.py --round 1`
- [ ] Nếu **Rework**: sửa, làm lại Pha 5 cho phạm vi cần sửa, chạy `--round 2` (giữ vòng 1)
- [ ] Điền `submission/qc/QC_REPORT.md` (khung đã có, giữ nguyên tên các trường)
- [ ] Ghi chú cá nhân `submission/individual_notes/<mã>.md`
  - [ ] 191 (đã có khung, điền mục "Dùng AI" trung thực)
  - [ ] 101 (đã có)
  - [ ] 103
  - [ ] 201
  - [ ] 245
- [ ] `TEAM.md`: điền "Kết quả tóm tắt"
- [ ] `python scripts/check_submission.py --round 1` không còn `LỖI`
- [ ] Nhờ một người ngoài nhóm lần theo một ca từ báo cáo tới ảnh, quy tắc và nhãn
- [ ] Nộp theo kênh coach (repo riêng tư hoặc gói nén), ghi commit chốt bài
