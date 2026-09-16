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
| `train_13.jpg` | người đi phía sau, sát mép trái ảnh | cả skeleton | **Thiếu hẳn một người** (`thieu_nguoi`). Người này bị **out nét** vì máy lấy nét vào người mặc vest phía trước, nên tôi bỏ qua ở lượt gán đầu. Gold vẫn gán người này 11/17 điểm | Vẽ bổ sung một skeleton đủ 17 điểm (box 43 × 133 px — người nhỏ nhất trong bài). Phát hiện ở lượt chấm với gold, **sau** khi đã khoá nhãn |

Hai lỗi đầu tự phát hiện bằng công cụ **trước** khi khoá nhãn (commit `a2c2e80`); lỗi thứ ba do
evaluator chỉ ra **sau** khi nhận gold, sửa ở vòng rework.

Kết quả trước/sau (từ `check_pose_labels.py`):

| | Lượt gán đầu | Sau tự kiểm | Sau rework với gold |
| --- | ---: | ---: | ---: |
| Skeleton | 28 | 28 | **29** |
| Cảnh báo | 8 | 6 | 8 *(2 cảnh báo mới đều là false positive của người vừa thêm)* |
| `v = 2` | 304 | 304 | 312 |
| `v = 1` | 141 | **143** | 152 |
| `v = 0` | 31 | **29** | 29 |
| Lỗi "đảo trái/phải" **thật** | 1 người | **0** | **0** |

Chấm với gold (`evaluate_pose_annotations.py`):

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0,943 | 0,941 |
| OKS@0.50 | 0,966 | **1,000** |
| OKS@0.75 | 0,966 | **1,000** |
| `dao_trai_phai` | 0 | 0 |
| `nham_nguoi` | 0 | 0 |
| `thieu_nguoi` | 1 | **0** |
| `lech_nhe` | 12 | 12 |
| Mức | Xuất sắc | Xuất sắc |

**Vì sao OKS trung bình giảm nhẹ mà vẫn là cải thiện:** `mean_oks` chỉ tính trên những người
**ghép được**, nên thêm một người khó (nhoè, OKS thấp hơn trung bình) sẽ kéo nó xuống. Còn
`OKS@0.50`/`OKS@0.75` chia cho **toàn bộ người trong gold cộng skeleton thừa**, nên chúng mới là
chỗ phạt việc bỏ sót người — và cả hai đã lên 1,000. Một con số xuống, một con số lên, và con số
lên mới là con số phản ánh đúng chất lượng bao phủ.

## Cảnh báo đã kiểm và kết luận KHÔNG sửa

| Ảnh | Cảnh báo | Vì sao không sửa |
| --- | --- | --- |
| `train_16.jpg` | `left_shoulder/right_shoulder` và `left_hip/right_hip` ngược chiều so với hai mắt — người mặc áo đỏ số 23 | Mặt anh ta nghiêng gần 90° nên **hai mắt chỉ cách nhau 1,7 px** — dấu của hiệu số đó là nhiễu, không phải tín hiệu hướng. Kiểm toàn chuỗi vai/khuỷu/cổ tay/hông/gối/cổ chân đều **cùng một chiều** và khớp với việc anh ta quay lưng ⇒ nhãn đúng, cảnh báo là *false positive* |
| `train_13.jpg` | cùng cảnh báo trên, ở người nhoè mới gán bổ sung | Mặt bị out nét nên hai mắt lệch **1,4 px** và hai tai lệch 1,1 px. Cả chuỗi thân cùng chiều âm (vai −14,3 · khuỷu −6,0 · hông −16,2 · gối −11,0 · cổ chân −18,6) ⇒ nhãn đúng, cùng loại *false positive* |
| `train_04.jpg` (2 người), `train_10.jpg`, `train_13.jpg` (người mặc vest) | "có N khớp `v=0` trong khi cả người nằm gọn giữa ảnh" | CVAT tính box của skeleton **bao quanh các điểm đã đặt**; khớp `Outside` không có toạ độ nên box co lên, khiến người bị mép ảnh cắt trông như nằm gọn giữa ảnh. Đã kiểm từng ca bằng mắt: các khớp đó thật sự ở ngoài khung hình |

## Hai câu kết luận

- **Lỗi lặp đi lặp lại nhiều nhất của bài này:** lẫn **"không nhìn thấy"** với **"không còn ở đó"** —
  biểu hiện ở hai dạng. Dạng một: dùng `Outside` cho khớp bị che nhưng vẫn trong khung (`train_11`).
  Dạng hai: bỏ hẳn một người vì **nhìn không rõ** (`train_13`, người bị out nét) — cũng là "không
  nhìn thấy rõ" bị đối xử như "không có".
- **Nó là lỗi thao tác hay lỗi guideline chưa rõ:** **lỗi guideline chưa rõ.** Trước khi làm, tôi
  chưa có một luật kiểm chứng được để phân biệt hai trạng thái đó ở người bị cắt ở mép ảnh. Sau khi
  so `train_11` với `train_13` tôi đã bổ sung "phép thử ước lượng" và điều kiện tư thế (đứng / ngồi)
  vào `GUIDELINE_MINI.md` mục 2 — đó là luật mà nếu có từ đầu thì lỗi này đã không xảy ra.
  Riêng lỗi đảo trái/phải ở `train_16` thì là **lỗi thao tác**, đúng như slide 43 cảnh báo: nó xảy
  ra ở lúc làm nhanh, không phải ở ảnh khó.
