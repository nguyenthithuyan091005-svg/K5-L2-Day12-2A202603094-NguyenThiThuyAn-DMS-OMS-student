# Danh sách lớp v0 — Nguyễn Thị Thúy An · G03

Viết tiếp từ slide 54 bài giảng. Mỗi lớp trả lời: **model cần nhận ra cái này để làm gì?**

## DMS (ít nhất 6 lớp)

| # | Lớp / thuộc tính | Giá trị | Để làm gì (chức năng DMS) | Nhìn thấy được trên IR? |
|---|---|---|---|---|
| 1 | `face` → `eye_left`, `eye_right` | open / closed / unknown (từng mắt) | Phát hiện buồn ngủ: mắt nhắm lâu, chớp mắt chậm (PERCLOS) | Có, khe mắt thấy rõ trên NIR thật. Mất khi tay che (D06) hoặc kính phản xạ, lúc đó phải ghi `unknown` |
| 2 | `face` → `gaze_zone` | road / mirror_l / mirror_r / mirror_c / cluster / center / lap / other | Phát hiện mất tập trung: không nhìn đường quá lâu, nhìn xuống đùi hoặc màn hình giữa | Một phần. Ước lượng chủ yếu theo hướng đầu, vì đồng tử khó thấy khi mặt nghiêng (D02 quay hẳn về sau) |
| 3 | `face` → `glasses` | none / clear / sun / unknown | Biết khi nào trạng thái mắt kém tin cậy (kính phản xạ, kính râm), tránh báo buồn ngủ nhầm | Có với gọng kính (D03, D05, D06). Kính râm trên NIR có thể trông trong suốt, nên không chắc thì ghi `unknown` |
| 4 | `face13` (13 điểm mặt) | 13 điểm + cờ `occluded` / `outside` | Đo độ mở mắt, miệng (ngáp), tư thế đầu; là đầu vào cho mô hình mắt và gaze | Có, khi mặt rộng ≥ 60 px. Điểm bị tay hoặc điện thoại che phải đánh cờ, không đặt cho đủ |
| 5 | `hand` → `on_wheel` (+ `side`, `whose`) | yes / no / unknown · left / right · driver / passenger | Kiểm tra tay rời vô lăng (hands-off), biết tay nào đang làm việc khác | Có, tay và cổ tay sáng rõ trên NIR. Trên xe tự lái thử nghiệm, `on_wheel = no` là bình thường |
| 6 | `phone` → `state` | held / mounted / lap | Phát hiện dùng điện thoại khi lái: gọi điện áp tai (D03), nhắn tin trên đùi | Có nếu thấy cạnh hoặc góc máy. Màn hình không phát sáng trên NIR, nên phải nhận bằng hình dạng |
| 7 | `person` → `seatbelt` (tài xế) | worn_correct / worn_incorrect / not_worn / unknown | Nhắc thắt dây an toàn, phát hiện đeo sai cách (dưới nách, sau lưng) | Có, dải dây tối nổi trên áo sáng (D02, D06). Đang thắt dở (D05) thì ghi `unknown` |

## OMS (ít nhất 6 lớp)

| # | Lớp / thuộc tính | Giá trị | Để làm gì (chức năng OMS) | Nhìn thấy được trên IR? |
|---|---|---|---|---|
| 1 | `person` → `seat` | driver / front_passenger / rear_left / rear_middle / rear_right | Đếm người theo từng ghế: nhắc dây an toàn, điều chỉnh túi khí, điều hòa theo vùng | Có (hình dạng cơ thể). Phải đổi ghế theo xe vì camera nhìn ngược: trái ảnh là `rear_right` |
| 2 | `person` → `seatbelt` (hàng sau) | not_worn / unknown (SVIRO) | Nhắc hành khách hàng sau thắt dây | IR-sim giữ được hình dạng dây. Nhưng SVIRO không có ai thắt dây, nên bộ này không dạy được `worn_correct` |
| 3 | `child_seat` → `occupied` | no / unknown / yes | Nhắc quên trẻ trong xe khi khóa xe; biết ghế có trẻ để cảnh báo | Có (hình dạng ghế, đầu và thân trẻ). Ảnh tối như S01_rgb vẫn nhận ra nhờ dáng người |
| 4 | `infant_seat` → `facing` | rear / front | Tắt túi khí khi nôi quay về phía sau ở ghế trước; nhắc quên em bé | Có (dáng nôi và tay cầm). Hướng quay khó xác định khi không thấy em bé (S01) |
| 5 | `object` → `type` | bag / box / cup / other | Nhắc quên đồ khi rời xe; phân biệt đồ vật với người để không báo nhầm có người | Có theo hình dạng (thùng giấy S08, ba lô S05). Không dựa vào màu, vì IR không có màu |
| 6 | `face` (OMS, chỉ box) | box mặt | Xác nhận người lớn có mặt và ngồi đúng tư thế; khoảng cách đầu tới túi khí | Có, nhưng IR-sim không tái hiện chói kính hay mắt sáng, nên trên S## chỉ gán box |
| 7 | `frame_meta` → `light`, `camera_blocked` | day / night / unknown · yes / no | Biết khung hình có dùng được không: camera bị che thì tắt cảnh báo, tránh báo sai | `light` không suy được trên ảnh dựng (SVIRO luôn `unknown`). `camera_blocked` thấy rõ trên cả RGB và IR |

## 2 câu hỏi leo thang

Câu hỏi bạn muốn hỏi người viết guideline (hoặc khách hàng) vì guideline v1.0 chưa trả lời. Mỗi câu kèm **tên khung** làm ví dụ.

1. Câu hỏi: Trẻ em đang ngồi trong ghế trẻ em thì gán thế nào? GUIDE §3 ghi "Bộ ảnh không có trẻ em ngồi", §4.1 định nghĩa `child_seat` là ghế trống và cấm `occupied = yes`. Nhưng bộ G03 có trẻ ngồi thật, và bộ nhãn ban tổ chức gửi sau đã thêm giá trị `yes`.
   Khung ví dụ: G03 S01_rgb / S01_ir (bé trai ngồi ghế trẻ em ở `rear_right`).
   Bạn tạm gán thế nào và vì sao: `child_seat`, `seat = rear_right`, `occupied = yes`, không vẽ `person` cho trẻ. Đây là cách coach đã chốt trong buổi lab. Nó cũng giữ được thông tin "có trẻ trong ghế", là thông tin quan trọng nhất cho chức năng nhắc quên trẻ. Guideline v1.x nên sửa §3, §4.1, §4.8 cho thống nhất.

2. Câu hỏi: Ghế em bé (nôi xách) **có em bé bên trong** thì gán thế nào? `infant_seat` chỉ định nghĩa cho nôi trống và không có thuộc tính `occupied`. Vì vậy hiện không có cách ghi "có em bé" như `child_seat`.
   Khung ví dụ: G03 S05_rgb / S05_ir (nôi có em bé ở `rear_right`) và G03 S06_rgb / S06_ir (nôi có em bé ở `rear_left`, ảnh tối).
   Bạn tạm gán thế nào và vì sao: `infant_seat`, `facing = rear`, không vẽ `person` cho em bé, theo cùng tinh thần với quyết định cho trẻ trong `child_seat`. Hình dạng là nôi xách nên tôi không gán `child_seat`. Nhưng như vậy dữ liệu mất thông tin "nôi có em bé", nên tôi đề xuất thêm thuộc tính `occupied` cho `infant_seat`.
