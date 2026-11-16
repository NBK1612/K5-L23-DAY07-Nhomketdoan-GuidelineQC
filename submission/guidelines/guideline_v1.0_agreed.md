# Guideline gán nhãn — v1.0 (thống nhất) — Nhóm G-05_t-004_Nhóm kết đoàn

- Phiên bản: v1.0
- Ngày: <<YYYY-MM-DD>>
- Người viết / duyệt: 03191 - Nguyễn Bảo Khánh, 03103 - Lê Ngọc Dũng, 03201 - Bùi Quang Huy, 03245 - Nguyễn Sỹ Kiên, 03101 - Nguyễn Xuân Anh
- Loại nhãn: bounding box, CVAT, xuất COCO 1.0
- Taxonomy đi kèm: `submission/taxonomy.json` (version `v1.0`)

> Chốt bản này xong thì **không sửa cho tới hết Pha 3**. Ca nào còn mơ hồ: tick `can_review` rồi ghi chú lại.

---

## 0. Các chỗ bất đồng giữa 5 bản cá nhân và quyết định

| # | Vấn đề | Các phương án trong bản cá nhân (của ai) | Nhóm chốt | Lý do | Ảnh |
|---|---|---|---|---|---|
| 1 | <<ví dụ: có thêm lớp bicycle / three_wheeler / bus không>> | <<03191: thêm; 03xxx: ...>> | <<>> | <<>> | <<img_0xx>> |
| 2 | | | | | |
| 3 | | | | | |

## 1. Mục tiêu

<<Mô hình dùng nhãn để làm gì, sai ở đâu thì nguy hiểm nhất, sai nào gây nhiễu.>>

## 2. Lớp và thuộc tính

### 2.1 Lớp

Phép thử: *"mô hình có phải đoán ra điều này không?"* Nếu có thì đó là lớp. Nếu chỉ dùng để lọc hoặc QC thì đó là thuộc tính.

| Lớp | Định nghĩa (khớp `definition` trong taxonomy.json) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | | | |
| `truck` | | | |
| `van` | | | |
| `motorcycle` | | | |
| `person` | | | |
| `ignore_region` | | | |

**Vật không thuộc lớp nào:** <<>>

### 2.2 Thuộc tính

**`occluded_level`** (phần trăm diện tích vật bị vật khác che):

| Mức | Ngưỡng | Ví dụ |
|---|---|---|
| `0` | | |
| `1` | | |
| `2` | | |
| `3` | | |

**`truncated`:**
- `yes`: <<>>
- `no`: <<>>

**`can_review`:** <<tick khi nào>>

## 3. Quy tắc vẽ box

1. <<Ôm sát phần nhìn thấy / amodal>>
2. <<Vật bị che>>
3. <<Vật bị cắt mép>>
4. <<Kích thước tối thiểu (min_box_height_px = 20)>>
5. <<Người trên xe hở / trong xe kín>>
6. <<Xe chở vật / chở xe khác>>
7. <<Không gán: tượng, đồ chơi, mô hình, ma-nơ-canh, hình in, phản chiếu>>
8. <<Xe chủ (ego vehicle)>>
9. <<Đám đông / xe dày đặc và ignore_region>>
10. <<Vật nhoè, ảnh tối>>

## 4. Ca khó

| Ảnh | Tình huống | Quyết định | Lý do |
|---|---|---|---|
| img_0xx | | | |

## 5. QC và phiên bản

- Ngưỡng QC (theo `taxonomy.json`, không đổi): `match_iou = 0.5`; IAA đạt khi IoU trung bình ≥ 0.85 và đồng thuận lớp ≥ 95%; tỉ lệ ảnh lỗi ≤ 2% mới Release.
- Ca mơ hồ khi gán: tick `can_review`, ghi chú tên ảnh, đem ra calibration.
- Người chốt ca mơ hồ: <<>>

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v0 | Bản khởi đầu của giảng viên | — |
| v1.0 | <<>> | <<>> |
