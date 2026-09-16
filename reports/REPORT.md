# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: `Trịnh Nam Trung — 2A202602113`   Nhóm: `cá nhân (lớp chỉ định làm một mình)`   Ngày: `16/09/2026`

> Mọi số trong báo cáo lấy trực tiếp từ output của `tools/*.py` và từ `outputs/*.json`;
> không ước lượng hay sửa tay.

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 312 / 152 / 29 |
| Thời gian trung bình mỗi ảnh | ~10 phút |

*(Tổng 493 khớp = 29 × 17, trung bình 16,0 khớp có `v > 0` mỗi người.)*

Về tốc độ: ~10 phút/ảnh, **chậm hơn mốc ~4 phút/ảnh** mà `GUIDE.md` đặt ra, tổng khoảng 200 phút
cho 29 skeleton (≈ 7 phút/người). Hai lý do: đây là lần đầu tôi dùng công cụ Skeleton của CVAT —
khác hẳn thao tác vẽ bbox của Ngày 2 và Ngày 3; và 8/20 ảnh có 2 người, nên số skeleton nhiều hơn số
ảnh 45%. Phần lớn thời gian dôi ra nằm ở việc **quyết cờ `v` cho từng khớp bị che**, không phải ở
việc kéo chấm cho khớp pixel — đúng chỗ mà slide 9 khuyên không nên tiêu thời gian, nhưng ở đây là
thời gian tra luật chứ không phải chỉnh vị trí.

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_ear` — **69%** (v=2: 9, v=1: 20, v=0: 0)
2. `right_ear` — **62%** (v=2: 11, v=1: 18, v=0: 0)
3. `left_wrist` — **38%** (v=2: 18, v=1: 11, v=0: 0)

**Chúng có đúng là những khớp tôi thấy khó gán nhất không?** Đúng một nửa, và chỗ lệch mới là
chỗ đáng nói.

Tai đúng là khớp tôi phải dừng lại lâu nhất, nhưng vì lý do khác với "khó xác định vị trí": tai
**hay bị che** (tóc, mũ lưỡi trai ở `train_11`, mũ bảo hiểm ở `train_04`) chứ vị trí giải phẫu
của nó thì dễ suy — nó nằm ngay đường viền đầu, ngang tầm mắt. Bằng chứng là tai có `%v=1` cao
nhất nhưng `v=0` **bằng 0**: tôi luôn ước lượng được vị trí, chỉ là không nhìn thấy rõ.

Khớp thực sự **khó xác định vị trí** lại là **hông** (`left_hip` 31%, `right_hip` 21%) — nó không
có bề mặt nào để nhìn trên bất kỳ người mặc quần áo nào, nên mỗi lần đặt là một lần ước lượng
thuần giải phẫu. Nó không lọt top `%v=1` vì tôi để `v=1` một cách nhất quán chứ không phải vì nó dễ.

Nói cách khác, `%v=1` đo **mức độ bị che**, không đo **mức độ khó**. Hai chuyện đó trùng nhau ở
tai, nhưng tách nhau ở hông — và đó là lý do bảng đếm phải đọc kèm guideline chứ không đọc một mình.

## 2. Chấm với gold

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0,943 | 0,941 |
| OKS@0.50 | 0,966 | **1,000** |
| OKS@0.75 | 0,966 | **1,000** |
| Lỗi `dao_trai_phai` | 0 | 0 |
| Lỗi `nham_nguoi` | 0 | 0 |
| Lỗi `xoa_khop_bi_che` | 0 | 0 |
| Lỗi `thieu_nguoi` | 1 | **0** |
| Lỗi `lech_nhe` | 12 | 12 |
| Mức | Xuất sắc | Xuất sắc |

Người: gold 29 | ghép được 29 | thiếu 0 | thừa 0.

**Tôi đã sửa gì giữa hai lần chạy:**

- `train_13.jpg`, người đi phía sau sát **mép trái ảnh** (box 43 × 133 px), **cả skeleton**: gán bổ
  sung đủ 17 điểm. Ở lượt gán đầu tôi bỏ qua người này vì họ **bị out nét** (máy lấy nét vào người
  mặc vest phía trước). Gold vẫn gán người này 11/17 điểm, và evaluator báo `thieu_nguoi = 1` với
  OKS 0,000 cho skeleton đó.

Đó là thay đổi **duy nhất** ở vòng rework. Hai lỗi khác trong bài tôi tự phát hiện **trước khi khoá
nhãn** bằng `check_pose_labels.py` và `visualize_pose.py`, nên chúng không xuất hiện trong lần chấm
gold đầu tiên (chi tiết ở `reports/review_partner.md`):

- `train_16.jpg`, người mặc áo trắng số 15, `hip`/`knee`/`ankle` — **đảo trái/phải toàn bộ nửa thân
  dưới**, đã đổi lại 3 cặp.
- `train_11.jpg`, người đội mũ lưỡi trai, `left_knee`/`right_knee` — **xoá khớp bị che** (`v=0` cho
  khớp vẫn còn trong khung), đã chuyển sang `v=1` kèm chấm ước lượng.

**Vì sao OKS trung bình giảm 0,943 → 0,941 mà vẫn là cải thiện:** hai con số này có **mẫu số khác
nhau**. `mean_oks` chỉ tính trên những người **ghép được**, nên thêm một người khó (nhoè, OKS 0,762 —
thấp nhất bài) tất yếu kéo trung bình xuống. Còn `OKS@0.50`/`OKS@0.75` chia cho **toàn bộ người
trong gold cộng skeleton thừa**, nên người bị bỏ sót bị tính là 0 ở đó. Bỏ sót một người **không hề
làm `mean_oks` xấu đi** — nó chỉ biến mất khỏi phép tính. Đây chính là điều cẩm nang cảnh báo:
*"keep omission rate separate from localization error"*. Nếu chỉ nhìn `mean_oks` thì vòng rework này
trông như làm bài tệ đi, trong khi thực tế nó vá đúng lỗ hổng nghiêm trọng nhất.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào? Ảnh đó dễ hay khó?**

Trong `outputs/eval_vs_gold.json`: **không có lỗi `dao_trai_phai` nào** ở cả hai lần chạy. Nhưng tôi
**đã có một lỗi như vậy** và tự bắt được trước khi khoá nhãn: `train_16.jpg`, người mặc áo trắng số
15 — hông, gối và cổ chân bị đảo trái↔phải trong khi vai, khuỷu, cổ tay vẫn đúng.

Ảnh đó **khó** (hai vận động viên bật nhảy tranh đĩa, thân chồng lấn, cả hai đều quay lưng về phía
máy ảnh). Nhưng lý do tôi sai thì **không** nằm ở độ khó của ảnh: tôi xác định đúng hướng thân khi
gán nửa trên, rồi khi chuyển xuống nửa dưới tôi đặt điểm theo **vị trí trên màn hình** thay vì theo
cơ thể người. Đúng như slide 43 nói, lỗi này sinh ra lúc làm nhanh chứ không phải lúc nhìn không rõ.

Cách tôi bắt được nó lại **không cần nhìn ảnh**: vai/khuỷu/cổ tay cho hiệu số `left − right` âm, còn
hông/gối/cổ chân cho hiệu số dương — tức thân trên và thân dưới lệch nhau 180°, điều không thể xảy ra
về giải phẫu dù người đó vặn mình đến đâu.

## 3. Kiểm chéo

Bạn cùng nhóm: `không có — lớp chỉ định bài này làm cá nhân`

Không chạy được `visibility_report.py --compare` vì không có bài của người khác. Thay vào đó tôi tự
kiểm bằng ba lượt của slide 42; chi tiết ở `reports/review_partner.md` và `reports/REVIEWER_CHECKLIST.md`.

Bảng dưới là **tự đối chiếu trong chính bảng đếm của mình**, giữa khớp lệch nhiều nhất và khớp lệch
ít nhất:

| Khớp | Tôi | Đối chiếu | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| `left_ear` | 69% `v=1` | `right_shoulder` 3% | 66 điểm % | **Bản chất khớp, không phải gán sai.** Tai bị tóc/mũ/khăn che thường xuyên; vai thì gần như luôn thấy rõ. Bằng chứng: tai có `v=0 = 0`, tức tôi luôn ước lượng được vị trí |
| `left_hip` / `right_hip` | 31% / 21% | — | 10 điểm % | **Đáng ngờ về tính nhất quán.** Hai bên hông lẽ ra bị che gần như nhau. Chênh 10 điểm % gợi ý tôi chưa áp luật hông đều tay giữa các ảnh — nếu có người thứ hai cùng gán thì đây là chỗ thứ hai cần soi |

Nếu có bạn cùng nhóm, tôi dự đoán **tai** là khớp hai bảng đếm lệch nhau nhiều nhất, vì ranh giới
"thấy trọn vành tai mới cho `v=2`" phụ thuộc cảm nhận từng người.

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi tự đối chiếu:

- **Phép thử ước lượng:** ước lượng toạ độ khớp trước, rồi mới xét nó rơi **trong** hay **ngoài** khung
  ảnh — trong khung thì `v=1` kèm chấm, ngoài khung thì `v=0` không chấm. Lý do bị che không quan trọng.
- **Điều kiện tư thế:** người **đứng** thì hông→gối thõng xuống ~1,1 lần chiều dài thân; người **ngồi**
  thì hông→gối nằm gần như ngang, chỉ thấp hơn hông 30–60 px. Không dùng tỉ lệ người đứng cho người ngồi.
- **Nhoè không phải lý do bỏ người:** gán khi xác định được ≥ 3 khớp, kể cả khi người đó out nét.

## 4. Model

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0,8450 | 0,8450 | +0,0000 |
| pose_mAP50-95 | 0,6853 | 0,6908 | **+0,0055** |
| pose_precision | 0,9734 | 0,9792 | +0,0058 |
| pose_recall | 0,8462 | 0,8462 | +0,0000 |
| box_mAP50-95 | 0,8119 | 0,8041 | **−0,0078** |

*(Thêm: `box_mAP50` 0,9785 → 0,9600, chênh −0,0185. Môi trường: Tesla T4, ultralytics 8.4.153,
`yolo26n-pose` 2.926.494 tham số, 80 epoch, đánh giá trên tập test tách biệt 10 ảnh / 13 người.)*

### Trả lời năm câu hỏi ở cuối notebook

**1. `pose_mAP50-95` thay đổi bao nhiêu? Nếu giảm thì 20 ảnh dạy được gì và làm hỏng gì?**

`pose_mAP50-95` **tăng nhẹ: +0,0055** (0,6853 → 0,6908). Không giảm — nhưng mức tăng nhỏ đến mức
không nên coi là "fine-tune có tác dụng": trên tập test chỉ 13 người, chênh lệch cỡ này nằm trong
khoảng dao động ngẫu nhiên.

Điều đáng đọc hơn nằm ở **hướng ngược nhau của hai nhóm chỉ số**: pose tăng (+0,0055 mAP50-95,
+0,0058 precision) trong khi **box lại giảm** (−0,0078 mAP50-95, −0,0185 mAP50). 20 ảnh của tôi dạy
model **thói quen đặt khớp theo luật của lớp** — cụ thể là *"khớp bị che vẫn có chấm"* (152 khớp `v=1`,
31% tổng số, cao hơn hẳn gold COCO). Cái nó làm hỏng là **năng lực khoanh người**: 20 ảnh quá ít để
duy trì thứ model đã học từ hàng trăm nghìn người của COCO, nên khả năng detect bị mài mòn nhẹ —
đúng hiện tượng *catastrophic forgetting*.

**2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm người dễ hơn hay tìm khớp dễ hơn?**

Sau fine-tune: `box_mAP50-95` = 0,8041 vs `pose_mAP50-95` = 0,6908 → **chênh 0,1133**, tức khoảng
**11 điểm phần trăm**. Ở ngưỡng lỏng hơn khoảng cách còn rõ hơn: `box_mAP50` 0,9600 vs `pose_mAP50`
0,8450.

**Tìm người dễ hơn nhiều so với tìm khớp**, và lý do mang tính hình học: một cái box chỉ cần 4 số và
được chấm bằng IoU — chồng lấn 50% đã tính là đúng. Một pose cần **17 điểm đúng chỗ cùng lúc**, và
OKS phạt theo **bán kính dung sai riêng của từng khớp** (mắt chỉ ~11 px trên một người cỡ 220 px,
trong khi hông tới ~48 px). Model có thể khoanh đúng người mà vẫn đặt sai cổ tay — nên `pose_mAP`
luôn là chỉ số chặt hơn.

**3. Một ảnh test model đoán sai — gọi tên lỗi theo bốn loại của slide 43:**

`test_02.jpg` — ảnh trắng đen chụp cột đèn và vách đá tối trên nền trời. Model dựng **một skeleton
"person 0.31"** ở góc dưới bên trái, nằm trên **một mảng đá tối, không có người nào ở đó**. Toàn bộ
17 điểm bị nhồi vào một vùng nhỏ không tương ứng với khớp nào.

Đây là **"trượt hẳn"** — chấm vào chỗ không có khớp. Nó không phải đảo trái/phải (không có xương cắt
chéo), không phải nhầm người (không có người thứ hai nào để nhầm sang), cũng không phải lệch nhẹ (sai
hoàn toàn chứ không lệch vài pixel). Đúng hơn, đây là dạng nặng nhất: **cả skeleton trượt**, do
detector sinh ra một người không tồn tại ở `conf = 0.31`, sát ngưỡng lọc `0.25` của notebook.

**4. Ảnh nào có OKS thấp nhất giữa nhãn của tôi và model? Ai đúng, dựa vào đâu?**

Thấp nhất là **`train_13` — OKS 0,569**, rồi `train_06` 0,635, `train_11` 0,686 và `train_14` 0,686.

Ở `train_13`, **tôi đúng**, và căn cứ không phải cảm tính mà là bản gold: chính người bị nhoè ở mép
trái ảnh này là người tôi gán bổ sung ở vòng rework, và **gold cũng gán người đó**. Model thì hoặc bỏ
qua, hoặc đặt khớp rất lệch trên một chủ thể out nét — nên OKS giữa hai bên thấp. Thêm một căn cứ
độc lập: chấm với gold cho ảnh này ra **OKS 0,762 và 0,890** cho hai người còn lại, tức nhãn của tôi
vẫn trên ngưỡng "đủ chính xác để train", trong khi model chỉ đạt 0,569 khi so với chính nhãn đó.

**5. Ảnh tôi gán tệ nhất có cũng là ảnh model đoán tệ nhất không? Điều đó nói gì?**

**Có, và trùng khít.** Người tôi gán tệ nhất so với gold là ở **`train_13` (OKS 0,762 — thấp nhất
trong 29 skeleton)**, và đó cũng chính là **ảnh model bất đồng với tôi nhiều nhất (OKS 0,569)**.

Điều đó nói rằng **`train_13` là một bức ảnh khó, chứ không phải tôi gán kém riêng ở đó**: cả người
lẫn máy đều vấp ở cùng một chỗ, và chỗ đó có nguyên nhân vật lý rõ ràng — chủ thể nằm **ngoài mặt
phẳng lấy nét**, biên cơ thể bị nhoè nên không ai xác định được tâm khớp chính xác.

Hệ quả thực tế cho một pipeline dữ liệu: khi nhãn người và dự đoán model **cùng kém trên một ảnh**,
đó là tín hiệu ưu tiên **xem lại chính bức ảnh** (loại khỏi tập train, hoặc gán cờ chất lượng thấp),
chứ không phải tín hiệu để đi sửa người gán nhãn. Ngược lại, nếu người gán tốt mà model kém thì mới
là vấn đề của model.

## 5. Một rule evidence bạn đã dùng

**Ảnh `train_11.jpg`, người thứ 1 (phụ nữ đội mũ lưỡi trai ngồi sau bàn picnic), khớp `left_knee` và
`right_knee` — tôi chọn `v = 1` kèm chấm ước lượng, thay vì `v = 0`.**

Bằng chứng nhìn thấy được: toàn bộ thân trên của cô ấy hiện rõ tới ngang hông (hông ở y ≈ 318 px),
phần dưới bị **mặt bàn và thùng bánh** che kín; ảnh cao 427 px nên vẫn còn 109 px khung hình bên
dưới điểm hông. Cô ấy **đang ngồi**, nên đoạn hông→gối nằm gần như ngang chứ không thõng xuống —
ước lượng cho ra đầu gối ở **y ≈ 387**, tức **vẫn nằm trong ảnh**, chỉ bị mặt bàn che.

Theo luật lớp, khớp bị che mà còn trong khung phải là `v = 1` **và vẫn đặt chấm**, nên tôi bật
Occluded và kéo hai chấm về vị trí ước lượng. Hai **cổ chân** thì ngược lại: ước lượng tiếp xuống cho
ra y ≈ 530, vượt quá đáy ảnh, nên giữ `v = 0` không đặt chấm. Một người có thể vừa có khớp `v = 1`
vừa có khớp `v = 0` cùng lúc — câu hỏi *"còn trong khung không"* được hỏi cho **từng khớp**, không
phải cho cả người.

Đối chứng làm rõ luật này: ở `train_13.jpg`, ông mặc vest cũng bị che thân dưới và **bị
`check_pose_labels.py` cảnh báo y hệt**, nhưng ông ấy **đang đứng** — ước lượng cho ra đầu gối ở
y ≈ 363 trong khi ảnh chỉ cao 281 px, tức đã ra ngoài khung ⇒ `v = 0` là đúng. Hai ca nhìn giống
nhau, bị công cụ cảnh báo giống nhau, nhưng **tư thế** mới là thứ quyết định giá trị `v`.
