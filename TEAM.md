# Thông tin nhóm

- Tên nhóm: Nhóm Kết Đoàn (G-05_t-004)
- Thành viên (MSHV - Họ và tên): 2A202603191 - Nguyễn Bảo Khánh, 2A202603103 - Lê Ngọc Dũng, 2A202603201 - Bùi Quang Huy, 2A202603245 - Nguyễn Sỹ Kiên, 2A202603101 - Nguyễn Xuân Anh
- Người điều phối (leader): 2A202603191 - Nguyễn Bảo Khánh
- Người review bản cuối: 2A202603191 - Nguyễn Bảo Khánh
- Commit chốt bài (điền ở kênh nộp sau khi commit):

## Phân công (mỗi người làm gì ngoài phần gán nhãn)

| MSHV - Họ và tên | Phụ trách thêm |
|---|---|
| 2A202603191 - Nguyễn Bảo Khánh | Leader: điều phối, chia ảnh, nhận và tập hợp nhãn; review bản cuối (QC Pha 6, QC_REPORT.md); đại diện nhóm nộp bài |
| 2A202603101 - Nguyễn Xuân Anh | Đề xuất taxonomy, phân tích ca khó (Pha 1–2); gom image_issues.csv (Pha 3); kiểm tra lại bản sửa calibration (Pha 5); chạy check_submission tới 0 lỗi (Pha 6) |
| 2A202603103 - Lê Ngọc Dũng | Chạy so sánh IAA (Pha 4); ghi calibration_decisions.csv (Pha 5) |
| 2A202603201 - Bùi Quang Huy | Soạn guideline v1.0 và taxonomy (Pha 2); guideline v1.1, guideline_change.json (Pha 5) |
| 2A202603245 - Nguyễn Sỹ Kiên | Sửa nhãn calibration trên CVAT, tạo nhãn cuối bằng build_final (Pha 5) |

## Kết quả tóm tắt (điền cuối bài)

- Lượt gán hoàn tất / được giao: 120/120
- IAA trước calibration (kèm mẫu số): IoU TB 0.845, cùng lớp 95.2% trên 938 cặp box ghép — chưa đạt (IoU < 0.85)
- Số ảnh phải calibration: 37 / 40 (1781 ca)
- Vòng QC và số ảnh đã duyệt / cả lô: vòng 2, 40/40 (vòng 1: Hold do còn box can_review)
- Tỉ lệ lỗi: 0.0 % theo ảnh (0/40), 0.0 % theo đối tượng (0/591)
- **Quyết định** (Release / Hold / Rework) và lý do một dòng: Release — QC vòng 2 duyệt 40/40 ảnh, 0 lỗi ≤ 2%, không lỗi lặp; nhãn cuối là đồng thuận đa số + R-17
