# Ghi chú cá nhân — 03191 - Nguyễn Bảo Khánh

## 1. Pha 1: ba ca khó tôi tìm ra trong 40 ảnh
| Ảnh | v0 không trả lời được gì | Quyết định ban đầu của tôi |
|---|---|---|
| img_004, img_016, img_035 | Tuk-tuk / auto-rickshaw không thuộc lớp nào trong v0 (car, truck, van, motorcycle, person) | Đề xuất lớp mới `three_wheeler`; nếu nhóm không thêm thì gán `motorcycle` |
| img_002 | Xe đầu kéo chở 8–9 ô tô: gán mấy box, ô tô được chở có gán không | 1 box `truck` cho đầu kéo + rơ-moóc; mỗi ô tô thấy ≥ 30% thân thì một box `car`, `occluded_level` theo khung sắt che |
| img_017, img_027, img_033 | v0 không nói gì về tượng, bia mộ hình xe, ma-nơ-canh | Không gán, để thành mẫu âm |

## 2. Pha 2: chỗ tôi bất đồng với nhóm
- <<Chỗ nào bản của tôi khác bản của bạn? Cuối cùng nhóm chốt gì, tôi đồng ý / chưa đồng ý vì sao?>>

## 3. Pha 3: ảnh tôi đã gán
- Số ảnh được giao: <<>> · Số box đã vẽ: <<>> · Số lần tôi tick `can_review`: <<>>
- Ca nào tôi lưỡng lự nhất khi gán (ảnh, vì sao)? <<>>

## 4. Pha 4–5: tôi lệch ở đâu
- Trong `calibration_candidates.csv`, các ca có tên tôi: tôi lệch vì guideline mơ hồ hay vì tôi chưa nắm quy tắc? <<>>
- Quyết định calibration nào tôi thấy quan trọng nhất? <<>>

## 5. Dùng AI
- <<Công cụ nào, cho việc gì, bạn kiểm tra kết quả thế nào? Ghi trung thực, ví dụ: Claude Code dùng để đọc đề, liệt kê tình huống trong ảnh, tạo khung file; tôi tự xem lại từng ảnh và tự quyết định...>>

## 6. Bài học
- Một điều tôi sẽ làm khác khi viết guideline lần sau: <<>>
