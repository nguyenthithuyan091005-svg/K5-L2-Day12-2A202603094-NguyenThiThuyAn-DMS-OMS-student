# TEAM — Ngày 12

Mã bộ ảnh: **G03** · Hình thức: cá nhân

| Họ tên | MSSV | Đã gán đủ 22 ảnh? | Review chéo với (peer diff) | Số dòng disagreement đã xử lý |
|---|---|---|---|---|
| Nguyễn Thị Thúy An | 2A202603094 | có | Nguyễn Việt Quân (G03) | 54 / 54 |

Ca hai bạn vẫn không thống nhất và đã hỏi coach (ghi tên ảnh + đối tượng):

- G03 S01_rgb / S01_ir · `infant_seat.facing`: không thấy em bé trong nôi nên không xác định được hướng. Tôi ghi `rear`, Quân ghi `front`.
- G03 S01, S05, S06 · trẻ em / em bé ngồi trong ghế: GUIDE §3 ghi bộ ảnh không có trẻ em. Coach chốt: không gán `person`, `child_seat` có trẻ thì `occupied = yes`.
- G03 D03, D04 · `gaze_zone`: không chắc tài xế nhìn đâu. D04 tôi đã đổi thành `other` trong v2.

## Cá nhân

### NguyenThiThuyAn-2A202603094
- Phần mình làm (ngoài 22 ảnh của mình, ví dụ ai review ai, ai tổng hợp ca hỏi coach): review chéo với Nguyễn Việt Quân, xử lý 54 dòng bất đồng (23 dòng tôi đúng, 3 dòng Quân đúng và tôi đã sửa trong `export_final_v2.zip`, 28 dòng mơ hồ). Tổng hợp các ca trẻ em ngồi trong ghế để hỏi coach.
- Điều mình học được hôm nay: phải kiểm từng thuộc tính chứ không chỉ kiểm vị trí box, vì giá trị mặc định của CVAT (`modality = ir`, `seat = driver`) rất dễ bị bỏ sót. Không thấy mặt (không thấy mắt lẫn mũi) thì không vẽ `face`, không dùng `unknown` để giữ chỗ.

Làm cá nhân: chỉ cần điền dòng của mình, tên bạn được coach ghép, và mục "Cá nhân" của mình.
