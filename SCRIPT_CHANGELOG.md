# Nhật ký chỉnh sửa `cabin_check` — báo lại Lab Coach

- **Người sửa:** Nguyễn Thị Thúy An · bộ ảnh **G03**
- **Ngày:** 21–22/11/2026 (Lab Ngày 12)
- **Công cụ:** Claude Code (AI), có coach cho phép sửa script. Đã khai báo trong `prompts.md`.
- **Bản gốc:** commit `b759ad5` của repo student. Xem toàn bộ thay đổi bằng `git diff b759ad5 -- scripts/`.
- **File sửa:** `scripts/cabin_check.py` (hàm `check_image`) và `scripts/cabin_schema.py` (giá trị `occupied`). Phần `diff` không đổi.
- **Không sửa:** `GUIDE.md`, `labels_cabin_v1.json` trong repo, file export, file XML.

> Lưu ý: script đã sửa chỉ dùng để tự kiểm. Coach chấm bằng script của coach nên kết quả có thể khác (xem mục "Kết quả trên bài G03").

---

## Tóm tắt

| # | Thay đổi | Loại | Mức | Căn cứ |
|---|---|---|---|---|
| 1 | Kiểm `modality` theo tên file: `_rgb` → rgb; `_ir` và `D##` → ir | Bỏ sót lỗi → `[ERROR]` | Nghiêm trọng | GUIDE 4.3, 4.9; CHECKPOINTS dòng 32 |
| 2 | `child_seat.occupied` chấp nhận `yes` | Báo lỗi sai → hợp lệ | Nghiêm trọng | Bộ nhãn ban tổ chức (`message.txt`, 21/11) và quyết định coach (xem dưới) |
| 3 | `face` trên ảnh SVIRO có thuộc tính mắt/kính/gaze khác `unknown`/`other` | Thiếu quy tắc → `[WARN]` | Trung bình | GUIDE 4.1: OMS chỉ box |
| 4 | `hand`, `phone`, `face13` trên ảnh SVIRO | Thiếu quy tắc → `[WARN]` | Trung bình | GUIDE 4.3 dòng 101; 4.6 |
| 5 | `child_seat`, `infant_seat`, `object` trên ảnh D## | Thiếu quy tắc → `[WARN]` | Thấp | GUIDE 4.2: DMS không vẽ `object` |
| 6 | `person` trên D## có `seat` khác `driver` | Thiếu quy tắc → `[WARN]` | Thấp | GUIDE 4.3 |
| 7 | Trái/phải mắt trên D## so theo trục **y** (mắt trái có y lớn hơn) | Báo nhầm → sửa | Trung bình | GUIDE §3, 4.4-2, 4.6 |
| 8 | Độ rộng mặt cho luật 60 px trên D## đo theo trục y (tai → tai) | Sai trục → sửa | Thấp | GUIDE 4.6 |
| 9 | Cùng ghế SVIRO có `person` và `child_seat`/`infant_seat` | Thiếu quy tắc → `[WARN]` | Thấp | Quyết định coach: trẻ ngồi trong ghế không gán `person` |
| 10 | SVIRO: số `person` > số `face` | Thiếu quy tắc → `[WARN]` | Trung bình | GUIDE 4.2, 4.5 |

**Quyết định của coach (21/11):**
- GUIDE giữ bản cũ.
- Riêng trẻ em / em bé ngồi trong ghế thì **không gán `person`**. `child_seat` có trẻ thì ghi **`occupied = yes`**.

Lý do: GUIDE §3 ghi "Bộ ảnh không có trẻ em ngồi", nhưng bộ G03 có trẻ hoặc em bé ngồi trong ghế. Theo nhãn đã gán, đó là S01, S03, S04, S05, S06, S07. Đây là lỗi tài liệu lab, đã báo coach.

---

## Chi tiết

### 1 · `modality` không được kiểm theo tên file
- **Script gốc:** chỉ kiểm giá trị thuộc {ir, rgb}, không so với tên ảnh.
- **Ảnh hưởng thật trên G03:** bản export đầu tiên có `S01_rgb`, `S02_rgb` để mặc định `modality = ir` (giá trị mặc định của CVAT). Script gốc **không phát hiện**.
- **Sửa:** sai thì báo `[ERROR] frame_meta: modality='ir' nhưng ảnh này phải là 'rgb'`.

### 2 · `occupied = yes`
- Bộ nhãn ban tổ chức gửi (`message.txt`): `child_seat.occupied` = `unknown, no, yes`. Đối chiếu bằng máy toàn bộ JSON: đây là **điểm khác duy nhất** so với `labels_cabin_v1.json` của repo.
- **Script gốc:** `yes`, vốn chọn được trong CVAT, bị báo `[ERROR]`.
- **Sửa:** `cabin_schema.py` thêm `yes`, nên schema sinh ra **trùng 100%** với `message.txt`. `yes` hợp lệ, không báo. Giá trị lạ (ví dụ `maybe`) vẫn `[ERROR]`.

### 7 · Trái/phải mắt trên D##
- Ảnh D## xoay khoảng 90°, nên mắt trái của tài xế có **y lớn hơn**. Script gốc so theo trục x nên báo nhầm.
- **Ví dụ thật trên G03:** script gốc báo `D05: left_eye_outer nằm bên trái ảnh so với right_eye_outer`, trong khi nhãn đúng theo quy tắc y. Script đã sửa không báo.

---

## Kiểm thử trên dữ liệu G03

Chạy cùng lệnh, so script gốc (`b759ad5`) với script đã sửa:

```
python3 scripts/cabin_check.py check <file> [--only ...] --expect <n> --manifest G03/oms/manifest.csv
```

**A. File giả lỗi.** Lấy bản sao bản export G03 đầu tiên (`--only S01,S02`), cài thêm 2 lỗi vào `S01_ir`: `modality = rgb` và `child_seat.occupied = maybe`.

| Trường hợp | Script gốc | Script đã sửa |
|---|---|---|
| `S01_ir` modality = rgb (cài) | **Không phát hiện** | ERROR ✔ |
| `S01_rgb`, `S02_rgb` modality = ir (lỗi thật) | **Không phát hiện** | ERROR ✔ |
| `occupied = maybe` (cài) | ERROR | ERROR ✔ |
| `occupied = yes` ở `S01_rgb` (đúng theo quyết định coach) | ERROR (báo sai) | Hợp lệ ✔ |
| **Tổng** | 2 lỗi, 1 cảnh báo | 4 lỗi, 1 cảnh báo |

**B. Bản export G03 đầu tiên, đủ 22 ảnh** (trước mọi lần sửa nhãn):

| | Script gốc | Script đã sửa |
|---|---|---|
| Lỗi | 11. Trong đó 9 lỗi chỉ vì giá trị `occupied = yes`, vốn hợp lệ theo bộ nhãn mới. Đồng thời bỏ sót 2 lỗi `modality` | 4 (2 `modality`, 2 thiếu `frame_meta`) |
| Cảnh báo | 4. Có 1 báo nhầm trái/phải mắt ở D05 | 5. Thêm 2 cảnh báo S08 thiếu `face`, đúng là lỗi nhãn |

**C. Bản nộp `export_final_v2.zip`:**

| | Script gốc | Script đã sửa |
|---|---|---|
| Kết quả | **6 lỗi**, đều là `occupied = yes` ở S01, S04, S07 (đúng theo quyết định coach) | **0 lỗi, 0 cảnh báo** |

> Nếu coach chấm bằng script gốc, 6 lỗi `occupied = yes` ở trên là do script chưa cập nhật theo bộ nhãn mới, không phải lỗi gán nhãn.

---

## Giới hạn còn lại

- Script không có đáp án nên **không** bắt được ghế lật trái/phải (`rear_left` ↔ `rear_right`) hay nhầm `child_seat` ↔ `infant_seat`. Phải tự soát.
- Script không phân biệt được giá trị người gán chọn với giá trị CVAT điền sẵn.

## Câu hỏi gửi coach

1. Có cho phép nộp kèm script đã sửa và nhật ký này không?
2. Khi chấm E1, coach dùng script đã cập nhật `occupied = yes` chưa?
