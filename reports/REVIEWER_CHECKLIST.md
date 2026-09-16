# Reviewer checklist - điền khi kiểm bài người khác

Người gán: `Trịnh Nam Trung — 2A202602113`   Người kiểm: `Trịnh Nam Trung (tự kiểm - lớp chỉ định làm cá nhân)`   Ngày: `16/09/2026`

> Lớp không ghép cặp cho bài này nên không có bài của người khác để kiểm. Checklist dưới đây được
> điền cho **chính bài của mình**, sau vòng sửa lỗi. Chi tiết lỗi tìm được xem `reports/review_partner.md`.

Chạy trước khi soi bằng mắt:

```bash
python3 tools/check_pose_labels.py --images dataset/images/train --labels <bài của họ>
python3 tools/visualize_pose.py --images dataset/images/train --labels <bài của họ> --out /tmp/vis_review
python3 tools/visibility_report.py --labels dataset/labels/train --compare <bài của họ>
```

| | Mục kiểm | Đạt? | Ghi chú / ảnh nào |
| --- | --- | --- | --- |
| 1 | Mọi người trong ảnh đều có đủ 17 điểm, không ai bị thiếu | ☑ | 29 skeleton × 17 = **493 khớp**, khớp đúng tổng `v=2 312 + v=1 152 + v=0 29`. Người bị bỏ sót ở `train_13` đã được bổ sung ở vòng rework ⇒ `thieu_nguoi = 0`, gold 29 / ghép được 29 |
| 2 | Bật đường nối: không có xương nào cắt chéo ở vai hoặc hông | ☑ | Sau khi sửa `train_16` (áo trắng #15): vai −51,4 · hông −47,4 · gối −55,8 · cổ chân −69,2 px, cùng chiều. Cảnh báo còn lại ở `train_16` (áo đỏ #23) đã kiểm và xác định là *false positive* do mặt nghiêng (hai mắt lệch 1,7 px) |
| 3 | Không có xương nào kéo dài sang một cơ thể khác | ☑ | `check_pose_labels.py` không báo cảnh báo "nhầm người" nào; evaluator với gold cũng cho `nham_nguoi = 0` |
| 4 | Khớp bị che dùng `v = 1` **và có chấm**, không phải `v = 0` | ☑ | **152 khớp `v = 1`** (31% tổng số). Đã sửa 2 đầu gối ở `train_11` từ `v = 0` sang `v = 1` có chấm |
| 5 | `v = 0` chỉ xuất hiện ở khớp thật sự ra ngoài mép ảnh | ☑ | 29 khớp `v = 0`, tập trung ở cổ chân (18) và gối (8) của người bị cắt ở mép dưới, cộng 1 cổ tay + 2 hông ở `train_04`. Đã kiểm từng ca; 4 cảnh báo nhóm này là do CVAT tính box bao quanh điểm đã đặt |
| 6 | Không có dấu hiệu dùng `Hidden` (điểm `v = 2` nằm ở chỗ vô lý) | ☑ | Ở vòng sửa `train_11`, `v = 2` giữ nguyên **304** trong khi `v = 1` tăng 141→143 — chứng tỏ hai điểm sửa được gắn Occluded thật, không phải Hidden |
| 7 | Export đúng **COCO Keypoints 1.0**: mảng `keypoints` có 51 số mỗi người | ☑ | `annotations/coco_keypoints/person_keypoints_default.json`, 20 images / **29 annotations**, category `person` đúng 17 điểm đúng thứ tự COCO |
| 8 | Bản YOLO Pose: mỗi dòng 56 số, `kpt_shape: [17, 3]` | ☑ | Kiểm 20 file `dataset/labels/train/*.txt`: **không dòng nào khác 56 số**. `data.yaml` giữ nguyên `kpt_shape: [17, 3]` |
| 9 | Visibility report đã nộp, và hai bảng đã được đặt cạnh nhau | ☑ / — | `reports/visibility_report.md` + `outputs/visibility_report.json` đã có. Phần "hai bảng cạnh nhau" **không áp dụng** vì làm cá nhân; thay bằng tự phân tích ở `GUIDELINE_MINI.md` mục 4 |
| 10 | Mọi ca không rõ đều được ghi trong `GUIDELINE_MINI.md` | ☑ | 3 ca: `train_11` (gối người ngồi), `train_13` (gối người đứng), `train_16` (mặt nghiêng làm hỏng phép kiểm trái/phải) |
| 11 | `check_pose_labels.py` chạy 0 lỗi | ☑ | `ĐẠT định dạng`, 0 lỗi, 8 cảnh báo — cả 8 đã kiểm và giải thích được (4 do box co lại, 4 do phép thử hướng mắt gặp mặt nghiêng/nhoè) |

## Lỗi tìm được

Chép sang `reports/review_partner.md`. Mỗi dòng một lỗi, đủ bốn cột - người sửa phải
mở đúng chỗ đó được mà không cần hỏi lại.

| Ảnh | Người thứ | Khớp | Lỗi gì | Sửa thế nào |
| --- | --- | --- | --- | --- |
| `train_16.jpg` | áo trắng số 15 (bên phải ảnh) | `hip` / `knee` / `ankle` (cả 3 cặp) | Đảo trái/phải toàn bộ nửa thân dưới | Đổi lại 3 cặp trái↔phải trong CVAT, giữ cờ `v = 2` |
| `train_11.jpg` | người đội mũ lưỡi trai | `left_knee`, `right_knee` | Xoá khớp bị che (`v = 0` cho khớp vẫn còn trong khung) | Bật Occluded (`q`) + đặt chấm ước lượng dưới mặt bàn → `v = 1` |
| `train_13.jpg` | người đi phía sau, sát mép trái | cả skeleton | Thiếu hẳn một người — bỏ qua vì bị out nét (phát hiện khi chấm với gold) | Vẽ bổ sung skeleton đủ 17 điểm; `thieu_nguoi` 1 → 0, `OKS@0.75` 0,966 → 1,000 |

## Hai câu kết luận

- Lỗi lặp đi lặp lại nhiều nhất của bài này: **dùng `Outside` cho khớp bị che nhưng vẫn còn trong
  khung hình** — lẫn "không nhìn thấy" với "không còn ở đó".
- Nó là lỗi **thao tác** hay lỗi **guideline chưa rõ**? → Phần lớn là **guideline chưa rõ**: thiếu luật
  phân biệt "bị che" với "ra ngoài khung" cho người bị cắt ở mép ảnh, và thiếu luật nói rõ **nhoè
  không phải lý do bỏ người**. Cả hai đã bổ sung vào `GUIDELINE_MINI.md` mục 2. Riêng ca đảo trái/phải
  ở `train_16` là lỗi **thao tác** lúc làm nhanh — đúng như slide 43 cảnh báo.
