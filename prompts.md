# Khai báo dùng AI — Nguyễn Thị Thúy An

Nhắc lại: không đưa ảnh cabin hay ảnh tài xế (kể cả ảnh chụp màn hình) cho bất kỳ công cụ AI nào.

| # | Công cụ | Bạn hỏi gì (tóm tắt) | Bạn dùng kết quả thế nào | Bạn có kiểm lại không? Bằng gì? |
|---|---|---|---|---|
| 1 | Claude Code (Claude) | Đọc đề bài lab, tạo checklist HTML (`checkpoint.html`), giải thích Guideline Cabin v1.0 (class, thuộc tính, nhãn nào cho ảnh OMS/DMS) | Dùng làm checklist và tài liệu tra cứu khi gán | Có, đối chiếu lại GUIDE, CHECKPOINTS, SUBMISSION |
| 2 | Claude Code | Rà và sửa script `cabin_check` (coach cho phép): kiểm `modality` theo tên file, `occupied = yes` theo bộ nhãn mới, `face` OMS chỉ box, nhãn sai loại ảnh, trái/phải mắt trên ảnh D## xoay 90°. Chi tiết trong `SCRIPT_CHANGELOG.md` | Dùng script đã sửa để tự kiểm | Có, kiểm thử bằng file export giả lỗi, so script gốc và script mới |
| 3 | Claude Code | Chạy `cabin_check check` trên các bản export G03 và giải thích lỗi/cảnh báo; tạo `check_cpA.txt`, `check_cpB.txt` từ bản export G03 đầu tiên | Tôi tự sửa nhãn trên CVAT theo các lỗi | Có, chạy lại script sau mỗi lần sửa (bản cuối 0 lỗi) |
| 4 | Claude Code | Chạy `cabin_check diff` giữa bài tôi và bài Nguyễn Việt Quân (G03), đề xuất verdict / guideline_rule / action cho 54 dòng | Tôi duyệt từng dòng; sửa 3 chỗ Quân đúng (S01_rgb, D02, D04) rồi export `export_final_v2.zip`; giữ D03 theo quyết định của tôi | Có, mở lại ảnh trên CVAT và đối chiếu GUIDE |

Claude không gán nhãn thay tôi. Mọi nhãn đều do tôi tự gán trên CVAT local. `exit_ticket.md` tôi tự làm, không dùng AI.
