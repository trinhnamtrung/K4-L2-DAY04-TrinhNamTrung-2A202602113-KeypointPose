# Kiểm chéo - Ngày 4

Người gán: `Trịnh Nam Trung — 2A202602113`
Ngày: `16/09/2026`

## Về việc không có bạn cùng nhóm

Lớp chỉ định bài này làm **cá nhân**, không ghép cặp, nên không có bài của người khác để kiểm
chéo và không chạy được `visibility_report.py --compare`. Phần dưới là **bản tự kiểm trên chính
bài của mình**, dùng đúng quy trình ba lượt của slide 42 và đúng bộ tiêu chí trong
`REVIEWER_CHECKLIST.md`.

Công cụ đã chạy:

```bash
python tools/check_pose_labels.py --images dataset/images/train --labels dataset/labels/train
python tools/visualize_pose.py   --images dataset/images/train --labels dataset/labels/train --out outputs/vis_train
python tools/visibility_report.py --labels dataset/labels/train --out outputs/visibility_report.json --markdown reports/visibility_report.md
```

## Lỗi tìm được và đã sửa

| Ảnh | Người thứ | Khớp | Lỗi gì | Sửa thế nào |
| --- | ---: | --- | --- | --- |
| `train_16.jpg` | người mặc áo trắng số 15 (bên phải ảnh) | `left_hip`/`right_hip`, `left_knee`/`right_knee`, `left_ankle`/`right_ankle` | **Đảo trái/phải toàn bộ nửa thân dưới.** Vai/khuỷu/cổ tay chỉ một chiều (trái ở bên trái ảnh, đúng với người quay lưng), nhưng hông/gối/cổ chân chỉ chiều ngược lại — lệch 180° giữa thân trên và thân dưới, không thể có về giải phẫu | Đổi lại 3 cặp trái↔phải trong CVAT, giữ nguyên cờ `v = 2`. Sau khi sửa: vai −51,4 px · hông −47,4 px · gối −55,8 px · cổ chân −69,2 px, **cả bốn cặp cùng chiều** |
| `train_11.jpg` | người đội mũ lưỡi trai | `left_knee`, `right_knee` | **Xoá khớp bị che**: để `v = 0` (Outside) cho hai đầu gối, trong khi người này **đang ngồi** nên đầu gối ước lượng vẫn nằm trong khung ảnh (y ≈ 387 / ảnh cao 427) | Bật **Occluded** (`q`) và đặt chấm ước lượng dưới mặt bàn → `v = 1`. Hai cổ chân giữ `v = 0` vì ước lượng rơi ngoài đáy ảnh |

Kết quả trước/sau khi sửa (từ `check_pose_labels.py`):

| | Trước | Sau |
| --- | ---: | ---: |
| Cảnh báo | 8 | **6** |
| `v = 2` | 304 | 304 |
| `v = 1` | 141 | **143** |
| `v = 0` | 31 | **29** |
| Cảnh báo "đảo trái/phải" **thật** | 1 người | **0** |

## Cảnh báo đã kiểm và kết luận KHÔNG sửa

| Ảnh | Cảnh báo | Vì sao không sửa |
| --- | --- | --- |
| `train_16.jpg` | `left_shoulder/right_shoulder` và `left_hip/right_hip` ngược chiều so với hai mắt — người mặc áo đỏ số 23 | Mặt anh ta nghiêng gần 90° nên **hai mắt chỉ cách nhau 1,7 px** — dấu của hiệu số đó là nhiễu, không phải tín hiệu hướng. Kiểm toàn chuỗi vai/khuỷu/cổ tay/hông/gối/cổ chân đều **cùng một chiều** và khớp với việc anh ta quay lưng ⇒ nhãn đúng, cảnh báo là *false positive* |
| `train_04.jpg` (2 người), `train_10.jpg`, `train_13.jpg` | "có N khớp `v=0` trong khi cả người nằm gọn giữa ảnh" | CVAT tính box của skeleton **bao quanh các điểm đã đặt**; khớp `Outside` không có toạ độ nên box co lên, khiến người bị mép ảnh cắt trông như nằm gọn giữa ảnh. Đã kiểm từng ca bằng mắt: các khớp đó thật sự ở ngoài khung hình |

## Hai câu kết luận

- **Lỗi lặp đi lặp lại nhiều nhất của bài này:** dùng `Outside` (`v = 0`) cho khớp **bị che nhưng
  vẫn còn trong khung hình** — tức lẫn "không nhìn thấy" với "không còn ở đó". Xảy ra ở `train_11`,
  và suýt xảy ra thêm ở `train_13` theo chiều ngược lại.
- **Nó là lỗi thao tác hay lỗi guideline chưa rõ:** **lỗi guideline chưa rõ.** Trước khi làm, tôi
  chưa có một luật kiểm chứng được để phân biệt hai trạng thái đó ở người bị cắt ở mép ảnh. Sau khi
  so `train_11` với `train_13` tôi đã bổ sung "phép thử ước lượng" và điều kiện tư thế (đứng / ngồi)
  vào `GUIDELINE_MINI.md` mục 2 — đó là luật mà nếu có từ đầu thì lỗi này đã không xảy ra.
  Riêng lỗi đảo trái/phải ở `train_16` thì là **lỗi thao tác**, đúng như slide 43 cảnh báo: nó xảy
  ra ở lúc làm nhanh, không phải ở ảnh khó.
