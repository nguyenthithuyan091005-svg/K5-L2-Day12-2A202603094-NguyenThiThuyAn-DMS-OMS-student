# Tự đánh giá — Nguyễn Thị Thúy An · G03

## 1. Tự rà soát 4 cảnh

Nghỉ tay 1 phút, mở lại bài của mình, chọn 4 ảnh (2 S##, 2 D##), đọc như người lạ.

Loại lỗi: `seat_flip`, `wrong_class`, `guess`, `box`, `missing`, `extra`, `lr_flip` (trái/phải mặt), `flag` (thiếu cờ điểm), `ok`.

| Ảnh | Đối tượng | Loại lỗi | Ghi chú | Đã sửa? |
|---|---|---|---|---|
| S05_ir / S05_rgb | ghế bên trái ảnh (`rear_right`) | `wrong_class` | Tôi gán `child_seat`, `occupied = yes`, nhưng đó là nôi xách có em bé nằm, nên phải là `infant_seat`, `facing = rear`. Ở S05_ir, ba lô ở giữa còn bị gán nhầm thành `child_seat`, `seat = driver` | Có: đổi thành `infant_seat` rear; box giữa đổi thành `object` bag `rear_middle` |
| S01_ir | ghế trẻ em có bé trai | `seat_flip` | `seat = driver`, `occupied = unknown` là giá trị mặc định của CVAT, tôi quên sửa sau khi copy từ ảnh RGB. Đúng là `rear_right`, `occupied = yes` | Có |
| D05 | tài xế · `seatbelt` | `guess` | Tôi ghi `worn_correct`, nhưng tài xế đang kéo dây để thắt (thắt dở). Theo GUIDE 4.7 phải là `unknown` | Có, đổi thành `unknown`. Hai mắt cũng đổi thành `unknown` vì mặt sát mép ảnh, tối và mờ |
| D02 | tài xế · `seatbelt` | `guess` | Tôi ghi `unknown`, nhưng dải dây vắt chéo qua ngực thấy rõ trên áo sáng. Phải là `worn_correct` | Có |

## 2. Lỗi mình lặp lại nhiều nhất hôm nay

Lỗi: để nguyên giá trị mặc định của CVAT mà không kiểm lại thuộc tính. Lỗi này xảy ra ở 6 ảnh: `modality = ir` trên ảnh `_rgb` (S01_rgb, S02_rgb), `seat = driver` trên ảnh SVIRO (S01_ir, S05_ir), và quên hẳn tag `frame_meta` (S04_rgb, S06_ir, đã bổ sung trên CVAT trước khi export lại).

Vì sao mình mắc: giá trị đầu tiên của mỗi thuộc tính là giá trị mặc định (`modality` là `ir`, `seat` là `driver`). Khi copy shape từ ảnh RGB sang ảnh IR hoặc vẽ nhanh, tôi chỉ nhìn box có đúng chỗ không mà không mở DETAILS ra kiểm. Tôi cũng không đặt tag `frame_meta` đầu tiên như GUIDE 4.3 hướng dẫn, nên có ảnh bị sót.

Lần sau tránh bằng cách: làm đúng thứ tự GUIDE 4.3, mở ảnh là đặt tag `frame_meta` ngay và đọc tên file để chọn `modality`. Với mỗi box OMS, đọc to vị trí ghế theo xe rồi mới chọn `seat` (trên SVIRO không bao giờ có `driver`). Sau khi copy sang ảnh `_ir`, duyệt lại từng shape ở DETAILS. Chạy `cabin_check` sau mỗi 2 cảnh thay vì đợi đến cuối.

## 3. Ghi chú về disagreement_log

Một dòng bạn của mình đúng và mình sai. Mình học được gì từ dòng đó: dòng **D02 · face · box**. Tài xế quay hẳn về phía sau, chỉ thấy tai, gáy và gọng kính, nhưng tôi vẫn vẽ một box `face` hẹp và để mắt `unknown`. Quân không vẽ box `face`, và Quân đúng. GUIDE 4.5 ghi không thấy cả mắt lẫn mũi thì **không vẽ box**, chỉ ghi lý do vào ghi chú. `unknown` dùng cho thuộc tính của một mặt đã nhìn thấy, không dùng để "giữ chỗ" cho một mặt không thấy. Lần sau, trước khi vẽ `face`, tôi sẽ tự hỏi có thấy ít nhất mắt hoặc mũi không. Tôi đã sửa trong `export_final_v2.zip`, cùng với hai dòng Quân đúng khác: S01_rgb (sót `object` cái gối ở `rear_middle`) và D04 (`gaze_zone` nên là `other` khi không chắc).
