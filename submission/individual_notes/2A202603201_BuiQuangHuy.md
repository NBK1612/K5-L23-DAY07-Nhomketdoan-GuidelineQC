# Ghi chú cá nhân — 2A202603201 - Bùi Quang Huy

## 1. Pha 1: ba ca khó tôi tìm ra trong 40 ảnh

| Ảnh                                 | v0 không trả lời được gì                                                                                                                         | Quyết định ban đầu của tôi                                                                                                                                 |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| img_005, img_026, img_029           | Người đi xe máy/xe đạp: một box chung hay tách người và xe? Box xe có bao người không? Vật người cầm (ống dài img_029) có tính vào box xe không? | Tách riêng: mỗi người một box `person`, xe một box riêng chỉ bao xe, hai box được chồng nhau (R-02). Vật do người cầm không tính vào box xe (R-09)         |
| img_002                             | Xe đầu kéo chở nhiều ô tô: mấy box, ô tô được chở có gán không, người đứng trên rơ-moóc tính sao                                                 | 1 `truck` bao cả đầu kéo và rơ-moóc; mỗi ô tô được chở ≥ 20 px là một `car`, `occluded_level` theo phần khung che (R-08)                                   |
| img_017, img_027, img_038 / img_020 | v0 không nói gì về tượng, đài tưởng niệm hình pickup, xe mô hình, và xe trong gương chiếu hậu                                                    | Tượng/mô hình: không gán, không ignore, làm mẫu âm (R-05). Gương có hình rõ như xe thật: một `ignore_region` bao mặt gương (R-06); không gán xe chủ (R-07) |

Ca tôi chưa chốt, mang ra Pha 2 hỏi: img_011 (Predator là người hóa trang hay tượng), img_024 (xe vàng trên nóc là mô hình hay xe thật), img_028 (box `truck` có bao tượng ngựa không), img_033 (xe tải mép phải là thật hay phản chiếu), img_040 (xe máy thấy qua gương).

## 2. Pha 2: chỗ tôi bất đồng với nhóm

- Bản của tôi khác bản 101 ở: pickup (tôi xếp `truck` theo R-10, 101 xếp `car`); tuk-tuk và xích lô (tôi gộp vào một lớp `three_wheeler`, 101 tách `tuk_tuk` và `cyclo`); tủ đồ chơi img_038 (tôi không ignore, 101 dùng `ignore_region`).
- Nhóm chốt: <<điền sau họp Pha 2>>. Tôi đồng ý / chưa đồng ý vì: <<>>

## 3. Pha 3: ảnh tôi đã gán

- Số ảnh được giao: 24 · Số box đã vẽ: <<>> · Số lần tôi tick `can_review`: <<>>
- Ca nào tôi lưỡng lự nhất khi gán (ảnh, vì sao)? <<>>

## 4. Pha 4–5: tôi lệch ở đâu

- Trong `calibration_candidates.csv`, các ca có tên tôi: tôi lệch vì guideline mơ hồ hay vì tôi chưa nắm quy tắc? <<>>
- Quyết định calibration nào tôi thấy quan trọng nhất? <<>>

## 5. Dùng AI

- Claude Code: so guideline cá nhân với yêu cầu trong GUIDE/RUBRIC/template, đổi tên file sang mã 201 và bỏ thông tin cá nhân, thống nhất ký hiệu `[chưa chốt, cần họp]`, tạo khung ghi chú này. Cách tôi kiểm lại: <<tự mở lại các ảnh được dẫn, đối chiếu từng quyết định với ảnh, sửa chỗ không đồng ý>>.

## 6. Bài học

- Một điều tôi sẽ làm khác khi viết guideline lần sau: <<>>
