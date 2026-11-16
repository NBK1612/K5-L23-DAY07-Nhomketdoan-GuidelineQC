# Báo cáo QC — Nhóm G-05_t-004_Nhóm kết đoàn

> Mọi tỉ lệ đều **kèm mẫu số**.

## 1. Lô

| Mục | Giá trị |
|---|---|
| Tên lô | day07-street-40 |
| Số ảnh | 40 |
| Thành viên (MSHV) | 2A202603191, 2A202603103, 2A202603201, 2A202603245, 2A202603101 |
| Số lần gán mỗi ảnh (K) | 3 |
| Phiên bản guideline lúc gán | v1.0 |
| Phiên bản guideline hiện tại | v1.1 |

## 2. Mẫu QC

- Cỡ mẫu: <<n>> ảnh (<<m>> ảnh ca khó + <<k>> ảnh chọn ngẫu nhiên, seed <<42>>).
- Cách chọn: <<script qc_sample.py; vì sao nới lỏng so với quy tắc "lô < 500 ảnh kiểm 100%">>.
- Người kiểm: <<MSHV - Họ tên>>; ngày: <<YYYY-MM-DD>>.

## 3. Số đo

### 3.1 IAA (trước calibration)

| Chỉ số | Giá trị | Mẫu số | Ngưỡng | Đạt? |
|---|---|---|---|---|
| IoU trung bình (cặp box ghép được) | <<>> | <<số cặp box>> | ≥ 0.85 | <<>> |
| Đồng thuận lớp | <<>> % | <<số cặp box>> | ≥ 95% | <<>> |
| Box không ghép được (bỏ sót/thừa) | <<>> | <<tổng box>> | — | — |
| Đồng thuận `occluded_level` / `truncated` | <<>> | <<>> | — | — |

### 3.2 Tỉ lệ lỗi (mẫu QC, sau calibration)

| Cách đếm | Lỗi | Mẫu số | Tỉ lệ |
|---|---|---|---|
| Theo **đối tượng** | <<>> | <<đối tượng trong mẫu>> | <<>> % |
| Theo **ảnh** (ngưỡng Release ≤ 2%) | <<>> | <<ảnh trong mẫu>> | <<>> % |

## 4. Lỗi

Top 3 loại lỗi (trong 5 loại: bỏ sót, thừa, sai lớp, sai vị trí/kích thước, sai thuộc tính), mỗi loại một ảnh ví dụ:

| Hạng | Loại lỗi | Số lỗi | Ảnh ví dụ | Nguyên nhân gốc (guideline mơ hồ / người gán sai?) |
|---|---|---|---|---|
| 1 | <<>> | <<>> | <<img_0xx>> | <<>> |
| 2 | <<>> | <<>> | <<>> | <<>> |
| 3 | <<>> | <<>> | <<>> | <<>> |

Có **lỗi lặp lại cùng một kiểu** không? <<có/không, kiểu nào>>

## 5. Calibration

- Số ảnh bị cờ: <<>> / 40. Số quyết định đã ghi: <<>> (xem `calibration_decisions.csv`).
- Ba quyết định quan trọng nhất (mỗi cái một dòng): <<ca → quyết định → phiên bản guideline>>
- Quy tắc mới đã vào guideline: <<v1.1: ...>> hoặc <<không có quy tắc mới vì ...>>
- Nguyên nhân bất đồng chính: <<guideline mơ hồ / chưa nắm quy tắc / cùng hiểu sai>>

## 6. Script kiểm được gì, không kiểm được gì

- Script đã kiểm: <<tên lớp, giá trị thuộc tính, box trong ảnh, ...>>
- Sửa script: `scripts/validate_labels.py` dòng 20, regex tên file nhãn đổi từ `^(\d+)_` thành `^([A-Za-z0-9]+)_` vì MSHV của nhóm có dạng `2A2026xxxxx` (có chữ), bản gốc chỉ nhận MSHV toàn số. Không đổi logic kiểm khác.
- Script **không** kiểm được, và nhóm đã dùng mắt người ra sao: <<box ôm sát? cả nhóm cùng bỏ sót? cùng hiểu sai?>>

## 7. Quyết định

**<<Release / Hold / Rework>>** — người kiểm: <<MSHV - Họ tên>>, ngày <<YYYY-MM-DD>>.

Lý do (đi theo cây Hold → Rework → Release):
1. Guideline chưa rõ / dữ liệu hỏng? <<>>
2. > 2% ảnh lỗi, lỗi lặp lại, hoặc IAA dưới ngưỡng? <<>>
3. Kết luận: <<>>

Nếu **Rework / Hold**: việc cần làm tiếp và ai làm: <<>>
