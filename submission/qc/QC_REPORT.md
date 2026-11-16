# Báo cáo QC — Nhóm Kết Đoàn

## 1. Mục đích, dữ liệu và phiên bản

- Mục đích và giới hạn sử dụng: Thực hành đặc tả và QC nhãn bounding box 2D (9 lớp người và phương tiện) trên 40 ảnh đường phố. Tập ảnh tuyển chọn để luyện ca biên, chưa đánh giá độ đại diện cho dữ liệu vận hành; không dùng trực tiếp để huấn luyện mô hình sản phẩm.
- Nguồn và phép sử dụng dữ liệu: 40 ảnh Wikimedia Commons theo giấy phép mở, ghi công trong `data/ATTRIBUTION.csv` (giữ nguyên); `batch_manifest.json` lưu nguồn và SHA-256 từng ảnh.
- Manifest và checksum nhãn cuối: `submission/qc/batch_manifest.json`; `labels_calibrated.json` SHA-256 `985af76fcd0e5633b173ed8629b0b18500750fec8753315e1ac2b08cf0218377` (40 ảnh, 601 annotation, trong đó 591 box đối tượng và 10 `ignore_region`).
- Guideline và taxonomy khi gán / khi bàn giao: Gán theo `guideline_v1.0_agreed.md` + `taxonomy.json` v1.0 (9 lớp, thuộc tính `occluded_level`, `truncated`, `drivable`, `can_review`). Bàn giao theo `guideline_v1.1_calibrated.md` (thêm R-15, R-16, R-17; `guideline_change.json` kind `semantic`); taxonomy giữ v1.0, không đổi lớp hay thuộc tính.

## 2. Phân công và đồng thuận trước calibration

- Lượt hoàn thành / được giao: 120/120 (5 người, mỗi ảnh 3 người, mỗi người 24 ảnh theo `assignment.csv`).
- Số cặp box ghép, IoU TB, đồng thuận lớp và mẫu số: 938 cặp box ghép (IoU ≥ 0.5, ghép 1-1); IoU TB 0.845 (ngưỡng 0.85, **chưa đạt**); cùng lớp 95.2% (ngưỡng 95%, đạt); cùng `occluded_level` 54.5%, `truncated` 82.0%, `drivable` 74.5%. Nguồn: `iaa_summary.md`.
- Box chưa ghép, loại do ignore, bất đồng vùng ignore: 367/293 box chưa ghép (tổng theo phía A/B của các cặp); 14 box bỏ qua do nằm trong ignore; 347 ca bất đồng vùng `ignore_region`. Tổng 1781 ca bất thường trên 37/40 ảnh.
- Ca hai người có thể cùng sai sau khi đối chiếu tham chiếu: Nhóm chưa nhận ca tham chiếu của coach nên chưa đối chiếu được. Rủi ro cùng sai lớn nhất là `occluded_level` (chỉ 54.5% đồng thuận, cả nhóm cùng lệch do ước lượng % bằng mắt) và vùng `ignore_region` ở ảnh đông (img_003, img_008, img_021).

## 3. Calibration và kiểm lại

- Ca đã xử lý, rule ID, bản sửa và người xác nhận: 1781/1781 ca có một dòng trong `calibration_decisions.csv`, gộp theo loại: `occluded_level` 427 ca (R-15, phạm vi `lo`), `truncated` 84 (R-16), `drivable` của `person` 88 (R-17, lỗi của một người gán), `drivable` của xe 39 (R-14), bỏ sót/thừa 415 (R-01, R-03, R-05), IoU thấp 318 (R-01), ignore 347 (R-03, R-04), sai lớp 45 (R-10), `can_review` 18. Người chốt 2A202603191. Bản sửa: nhãn cuối lấy theo đồng thuận đa số 3 người (`consensus_draft.json`), áp tự động R-17 và bỏ cờ `can_review`; **không sửa tay trên CVAT**. Kiểm lại: 2A202603191 qua QC vòng 1 và 2.
- Tác động v1.1 lên nhãn và ảnh: R-15 đổi cách chọn `occluded_level` nên phạm vi là cả lô 40 ảnh (`guideline_change.json` semantic). R-17 đổi 62 box `person` về `drivable=yes`. Không đổi lớp, không cần mapping taxonomy.
- Tham chiếu bằng chứng khi Rework: Vòng 1 (`reviews/round-01/`) ra **Hold** vì nhãn cuối còn 16 box `can_review`; nhóm bỏ cờ theo xác nhận của người duyệt, tạo lại nhãn cuối (`labels_calibrated_c6088f379e6320ec.json`) và mở vòng 2 (`reviews/round-02/`). Vòng 1 giữ nguyên.

## 4. Kết quả duyệt

- Vòng QC: 2
- Người review và ngày: 2A202603191, 2026-11-16
- Đã duyệt / cả lô: 40/40 (duyệt toàn bộ lô)
- Ảnh lỗi / ảnh đã duyệt: 0/40 = 0.0% (ngưỡng Release ≤ 2%)
- Annotation đối tượng hiện có bị lỗi / annotation đối tượng đã duyệt: 0/591 = 0.0%
- Vật bỏ sót, ảnh và vùng: 0 (người duyệt không ghi nhận vật bỏ sót)
- Ca khó / phần còn lại, lỗi lặp và ví dụ: ca khó 37/37 ảnh, 0 ảnh lỗi; phần còn lại 3/3 ảnh, 0 ảnh lỗi; không có lỗi lặp (phiếu `reviews/round-02/qc_review.csv`).
- Một lỗi máy chặn được và một lỗi cần người đọc ảnh: Máy chặn được: file nhãn đặt sai tên `...json.json` (`validate_labels.py` báo không tìm thấy file) và box cao < 20 px ở img_039 (cảnh báo vật nhỏ). Cần người đọc ảnh: bia mộ hình pickup img_027 được gán `truck` với `drivable=yes`; giá trị hợp lệ nên script cho qua, chỉ phát hiện khi đối chiếu R-14 với ảnh.

## 5. Quyết định bàn giao

- Quyết định: Release
- Lý do, người nhận và phạm vi sử dụng: Theo cây Hold → Rework → Release: guideline v1.1 không còn ca chưa chốt, dữ liệu không có ảnh hỏng (không Hold); QC vòng 2 duyệt 40/40 ảnh, 0/40 ảnh lỗi ≤ 2%, không có lỗi lặp (không Rework). Người nhận: coach/giảng viên lớp; phạm vi: bài thực hành Day 07, không dùng cho sản phẩm. Lưu ý: IAA trước calibration chưa đạt (IoU TB 0.845) và nhãn cuối là đồng thuận đa số chưa sửa tay, nên độ tin cậy dựa vào kết quả duyệt bằng mắt của một người.
- Nếu còn việc: Khi có ca tham chiếu của coach, 2A202603103 đối chiếu để tìm ca cùng sai; nếu phát hiện lỗi thì mở vòng QC mới theo `calibration_decisions.csv` (cột `phieu_sua` đã ghi việc cần làm cho từng ca).
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: Ca C001 — `calibration_candidates.csv`: img_001, bỏ sót/thừa một box `person` [321,124,102,190] (chỉ 2A202603103 vẽ, 2A202603191 không vẽ) → `calibration_decisions.csv` dòng C001: R-01, R-03, R-05, v1.1, người chốt 2A202603191, phạm vi `anh` → R-01/R-03/R-05 và dòng img_001 có trong `guideline_v1.1_calibrated.md` → `labels_calibrated.json` img_001 có 8 box, box `person` id 3 trùng vị trí ca C001 (IoU 0.74), `occluded_level=1`, `drivable=yes` → `reviews/round-02/qc_review.csv` img_001 = `khong` (2A202603191), ảnh duyệt `work/review/round-02/img_001.jpg.png`. Kết quả: đường dẫn nối liền, không đứt đoạn. Kiểm ngược này do công cụ AI (Claude Code) thực hiện trên file theo yêu cầu của leader; chưa có người ngoài nhóm kiểm bằng mắt.

## 6. Ghi chú kỹ thuật (sửa script)

- `scripts/make_cvat_labels.py`: thêm `"type": "rectangle"` cho mỗi nhãn và `values: ["false"]` cho thuộc tính checkbox, vì CVAT 2.74.1 báo lỗi `unknown label type "undefined"` và `attribute values must be a non-empty array`.
- `scripts/common.py`, `validate_labels.py`, `split_assign.py`, `check_submission.py`: nhận mã thành viên là MSHV (ví dụ 2A202603191) và tên file `<MSHV>_<HoTen>` theo yêu cầu của giáo viên, thay cho mã ba chữ số; vẫn nhận mã ba chữ số. Unittest 20/20 OK với taxonomy gốc.
- Lỗi môi trường: trên Windows phải đặt `PYTHONUTF8=1` để đọc file UTF-8; không sửa code cho việc này.
