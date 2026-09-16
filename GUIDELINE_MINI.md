# Mini guideline - nhóm: `cá nhân (lớp chỉ định làm một mình)`  |  người gán: `Trịnh Nam Trung — 2A202602113`  |  ngày: `16/09/2026`

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

**Luật nền, áp cho mọi dòng dưới đây — "phép thử ước lượng":** khi một khớp không nhìn thấy,
tôi ước lượng vị trí của nó từ giải phẫu và từ các khớp liền kề đã thấy, rồi hỏi: *toạ độ ước
lượng đó rơi vào đâu?*

- Rơi **trong khung ảnh** → `v = 1`, đặt chấm tại vị trí ước lượng (dù bị vật gì che cũng vậy).
- Rơi **ngoài mép ảnh** → `v = 0`, không đặt chấm.
- Không ước lượng nổi (không đủ căn cứ giải phẫu) → vẫn `v = 1` nếu còn suy được khoảng vị trí;
  nếu hoàn toàn không, ghi vào mục 3 thay vì tự quyết im lặng.

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Luôn `v = 1` và đặt chấm ước lượng, **trừ khi** ước lượng rơi ngoài mép ảnh (khi đó `v = 0`). Lấy mốc: hông nằm trên đường nối giữa vai cùng bên và đầu gối cùng bên, ngay dưới đường thắt lưng của quần. | Hông **không bao giờ** nhìn thấy được trên người mặc quần áo — nó là ước lượng giải phẫu, không phải thứ quan sát được (slide 12). Nếu cho phép `v = 0` vì "không thấy" thì gần như mọi người trong bộ ảnh đều mất hai khớp hông. Số liệu của tôi: hông `%v=1` = 31% / 21%, và chỉ **1 người duy nhất** có hông `v = 0` — người ngồi xe máy ở `train_04`, mà đó là do hông ra ngoài mép dưới khung hình. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Còn thấy **một phần vành tai hoặc thấy rõ đường viền tóc/mũ ôm lấy tai** → `v = 1` và đặt chấm (không dùng `v = 2`, vì không xác định được tâm khớp). Chỉ dùng `v = 2` khi thấy trọn vành tai. | Đây là khớp lệch nhiều nhất trong bài của tôi: **`left_ear` 69%, `right_ear` 62%** `v = 1` — cao nhất bảng. Vì tai bị tóc/mũ che là chuyện thường xuyên chứ không phải ngoại lệ, phải có luật cứng, nếu không mỗi ảnh sẽ quyết một kiểu. `train_11` (người đội mũ lưỡi trai, tóc xoã) là ca điển hình. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Áp **phép thử ước lượng** ở trên, và **phải tính theo tư thế thật**: người **đứng** thì hông→gối thõng xuống ~1,1 lần chiều dài thân; người **ngồi** thì hông→gối nằm **gần như ngang**, chỉ thấp hơn hông 30–60 px. | Cùng một "khớp không thấy" nhưng ra hai kết quả ngược nhau, và đây là ca tôi mất nhiều thời gian nhất: `train_11` (ngồi sau bàn) gối ước lượng ở y≈387 trong ảnh cao 427 ⇒ **trong khung** ⇒ `v = 1`; `train_13` (ông mặc vest đang đi) gối ước lượng ở y≈363 trong ảnh cao 281 ⇒ **ngoài khung** ⇒ `v = 0`. Nếu dùng tỉ lệ người đứng cho người ngồi thì sẽ kết luận sai. |
| Cổ tay nằm sau tay lái / sau thân mình | `v = 1` + đặt chấm, suy từ hướng cẳng tay (khuỷu → cổ tay) và vị trí bàn tay nếu thấy được. `v = 0` chỉ khi bàn tay ra khỏi mép ảnh. | Cổ tay `%v=1` = 38% / 28%, nhưng chỉ **1 khớp cổ tay** trong cả bài là `v = 0`: người đeo mũ bảo hiểm bên trái ảnh `train_04`, tay phải vươn ra khỏi **mép trái** khung hình. Phân biệt được hai chuyện đó là điều kiện để DMS trả lời đúng câu "tay nào rời vô-lăng". |
| Hai người chồng lên nhau | **Làm xong hẳn một người rồi mới bắt đầu người kế tiếp.** Khi đặt điểm, truy theo **chuỗi xương** (vai→khuỷu→cổ tay, hông→gối→cổ chân) chứ không chọn đầu mút gần nhất. Không rõ khớp thuộc về ai → ghi vào mục 3, không đoán. | 8/20 ảnh có 2 người; `train_16` là ca nặng nhất — hai người bật nhảy, thân chồng lấn, cánh tay đan vào nhau. Chọn "cái cổ tay gần nhất" là cách sinh lỗi `nham_nguoi`, mà lỗi đó nằm chung mục **25 điểm** với lỗi đảo trái/phải. Kết quả: `check_pose_labels.py` không báo một cảnh báo "nhầm người" nào trên cả 20 ảnh. |
| Người nhỏ đến mức nào thì không gán nữa | Gán khi xác định được **≥ 3 khớp** trên người đó — **kể cả khi người đó bị nhoè/out nét**. Nhoè không phải lý do bỏ; chỉ "không xác định nổi 3 khớp" mới là. Mặt không đọc được thì mũi/mắt/tai vẫn `v = 1` ước lượng theo đường viền đầu. | Đây là luật tôi phải **sửa sau khi chấm với gold**: ở lượt gán đầu tôi **bỏ sót** người đi phía sau, sát mép trái `train_13` (box **43 × 133 px**, bị out nét vì máy lấy nét vào người phía trước) — evaluator báo `thieu_nguoi = 1`. Gold vẫn gán người này. Sau khi bổ sung, người đó thành **người nhỏ nhất trong bài** và vẫn đủ 17 điểm; `thieu_nguoi` về 0 và `OKS@0.75` lên 1,000. Bài học: bỏ sót người **không** làm `OKS trung bình` xấu đi (nó chỉ tính trên người ghép được) nhưng phạt thẳng vào `OKS@0.75` và mục **Độ bao phủ**. |

Với mỗi luật, chèn **một ảnh mẫu** (screenshot từ CVAT) thay vì chỉ viết một câu.
Slide 12 nói rõ: khớp không có bề mặt nhìn thấy được thì phải có ảnh mẫu, không phải
một câu văn chung chung.

<!-- ẢNH MẪU CẦN CHỤP TỪ CVAT (mỗi ảnh 1 screenshot, chèn bằng ![](assets/...)):
     1. Hông            -> train_04, người đeo mũ bảo hiểm bên trái (hông v=0 vì ra ngoài mép dưới)
     2. Tai bị che      -> train_11, người đội mũ lưỡi trai (hai tai v=1)
     3. Cắt ở mép ảnh   -> đặt cạnh nhau train_11 (ngồi, gối v=1) và train_13 (đứng, gối v=0)
     4. Cổ tay          -> train_04, cổ tay phải ra ngoài mép trái ảnh
     5. Hai người chồng -> train_16, hai người bật nhảy
     6. Người nhỏ       -> train_19, người phía sau 56x156 px
-->

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_11.jpg`, người thứ `1` (người đội mũ lưỡi trai), khớp `left_knee` / `right_knee`

- **Mơ hồ ở chỗ nào:** người đang **ngồi sau bàn picnic**, toàn bộ thân dưới bị mặt bàn và thùng
  bánh che kín. Không nhìn thấy đầu gối, cũng không nhìn thấy mép dưới cơ thể, nên không biết ngay
  được là "bị che" hay "đã ra khỏi khung hình" — hai cái này ra hai giá trị `v` ngược nhau.
- **Bạn quyết thế nào:** `v = 1` cho cả hai đầu gối, đặt chấm ước lượng dưới mặt bàn (y ≈ 387,
  thấp hơn hông ~60 px, hơi lệch về phía trước). Hai **cổ chân** thì giữ `v = 0`.
- **Vì sao:** ước lượng theo tư thế **ngồi** — hông→gối gần như nằm ngang chứ không thõng xuống —
  cho ra toạ độ đầu gối **vẫn nằm trong ảnh cao 427 px**, nên theo luật lớp phải là `v = 1` có chấm.
  Nhưng ước lượng tiếp xuống cổ chân thì rơi quá đáy ảnh, nên cổ chân đúng là `v = 0`. Một người
  có thể vừa có khớp `v = 1` vừa có khớp `v = 0` — câu hỏi "còn trong khung không" hỏi **cho từng
  khớp**, không hỏi cho cả người.
- **Nếu người khác quyết ngược lại thì model học sai cái gì:** để cả bốn khớp `v = 0` là dạy model
  rằng **đầu gối biến mất mỗi khi người ta ngồi vào bàn**. Với DMS/OMS trong cabin — nơi tài xế
  **luôn ngồi** và ghế **luôn che hông/gối** — đó chính là trường hợp phổ biến nhất, model sẽ
  không bao giờ học được cách suy ra tư thế chân của người ngồi.

### Ca 2 - ảnh `train_13.jpg`, người `mặc vest đang hút thuốc` (người lớn phía trước), khớp `left_knee` / `right_knee`

- **Mơ hồ ở chỗ nào:** giống hệt Ca 1 về mặt quan sát — không nhìn thấy đầu gối, thân dưới khuất
  sau chiếc cặp và mép dưới bức ảnh. `check_pose_labels.py` cũng báo cảnh báo y hệt Ca 1.
- **Bạn quyết thế nào:** `v = 0` cho cả đầu gối lẫn cổ chân, không đặt chấm.
- **Vì sao:** người này **đang đứng/đi**, nên hông→gối thõng xuống theo tỉ lệ người đứng. Hông ở
  y ≈ 214, thân người ≈ 136 px ⇒ gối ước lượng ở y ≈ 363, trong khi **ảnh chỉ cao 281 px**. Khớp
  đã ra khỏi mép dưới khung hình ⇒ `v = 0`. Điểm mấu chốt: Ca 1 và Ca 2 **nhìn giống nhau và bị
  script cảnh báo giống nhau**, nhưng khác nhau ở **tư thế**, và tư thế mới là thứ quyết định.
- **Nếu người khác quyết ngược lại thì model học sai cái gì:** đặt chấm `v = 1` ở đây là **bịa ra
  một khớp nằm ngoài bức ảnh**. Toạ độ đó sẽ bị kẹp vào trong khung khi chuẩn hoá, tạo một đầu gối
  giả ngay sát mép dưới. Model học rằng người bị cắt ngang bụng thì đầu gối nằm ở rìa ảnh — và sẽ
  sinh ra keypoint ma ở mép mọi khung hình trong thực tế.

### Ca 3 - ảnh `train_16.jpg`, người `mặc áo đỏ số 23`, khớp `left_eye` / `right_eye` (và cặp trái/phải nói chung)

- **Mơ hồ ở chỗ nào:** hai người đang **bật nhảy và vặn người** tranh đĩa. Anh áo đỏ quay lưng về
  phía máy ảnh, đầu xoay nghiêng gần 90°, nên **hai mắt gần như chồng lên nhau**: trong nhãn của
  tôi chúng chỉ cách nhau **1,7 px**. `check_pose_labels.py` báo *"vai/hông nằm ngược chiều so với
  hai mắt - dấu hiệu đảo trái/phải"* cho chính người này.
- **Bạn quyết thế nào:** **giữ nguyên, không sửa.** Xác định lại hướng thân bằng cách khác: anh ta
  quay lưng (số áo 23 nằm trên lưng, thấy gáy) ⇒ bên trái cơ thể phải ở bên trái ảnh. Kiểm toàn bộ
  chuỗi: vai −38,5 px, khuỷu −112,1 px, cổ tay −196,2 px, hông −35,6 px, gối −48,8 px, cổ chân
  −28,9 px — **tất cả cùng một chiều**. Kết luận: nhãn đúng, cảnh báo là báo động giả.
- **Vì sao:** phép thử "so hướng hai mắt với hướng vai" chỉ có giá trị khi mặt **quay rõ về phía
  máy ảnh hoặc quay rõ ra sau**. Khi mặt nghiêng, khoảng cách hai mắt chỉ còn vài pixel và **dấu của
  nó là nhiễu**, không phải tín hiệu. Script không đặt ngưỡng tối thiểu cho tín hiệu mắt nên vẫn kêu.
  *(Đối chiếu: người mặc áo trắng số 15 trong cùng ảnh này thì là lỗi **thật** — vai/khuỷu/cổ tay chỉ
  một chiều còn hông/gối/cổ chân chỉ chiều ngược lại, tức thân trên và thân dưới lệch nhau 180°, điều
  không thể xảy ra về mặt giải phẫu. Tôi đã sửa bằng cách đổi lại 3 cặp `hip`/`knee`/`ankle`.)*
- **Nếu người khác quyết ngược lại thì model học sai cái gì:** "sửa" theo cảnh báo — tức đảo vai và
  hông của người áo đỏ — sẽ **tạo ra** đúng lỗi đảo trái/phải mà cảnh báo định ngăn. Và vì huấn luyện
  còn lật ảnh để tăng dữ liệu (`flip_idx` trong `data.yaml`, `fliplr=0.5`), nhãn sai đó được dạy cho
  model **hai lần**. Bài học: **cảnh báo của công cụ là giả thuyết cần kiểm chứng, không phải phán
  quyết** — slide 45 gọi việc sửa một chỗ vốn đã đúng là *false positive* trong QC.

## 4. Sau khi so visibility report với bạn cùng nhóm

> Lớp chỉ định làm **cá nhân**, không có bạn cùng nhóm để chạy `visibility_report.py --compare`.
> Thay vào đó, dưới đây là phần tự đối chiếu bảng đếm của chính mình.

- Khớp lệch `%v=1` nhiều nhất: `left_ear` / `right_ear` (`69%` / `62%`) — cao gần gấp đôi nhóm khớp
  kế tiếp (cổ tay 38%, gối 34%), và **gấp 23 lần** khớp thấp nhất là `right_shoulder` (3%).
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**: theo tôi là **do bản chất
  của khớp, không phải do gán sai** — tai là khớp duy nhất trong bộ 17 điểm thường xuyên bị **tóc,
  mũ, khăn quàng** che một phần, và tôi có áp một luật nhất quán (thấy trọn vành tai mới cho `v = 2`).
  Bằng chứng ủng hộ: tai có `%v=1` cao nhưng **`v = 0` bằng 0**, tức tôi không lẫn "bị che" với "ngoài
  khung" ở khớp này. Tuy vậy đây **vẫn là khớp rủi ro nhất nếu có người thứ hai cùng gán**: ranh giới
  "thấy trọn vành tai" phụ thuộc cảm nhận, nên nếu có bạn cùng nhóm thì đây chính là chỗ tôi dự đoán
  hai bảng đếm sẽ lệch nhau nhiều nhất.
- Luật mới bổ sung vào mục 2 sau khi thống nhất: **"phép thử ước lượng"** ở đầu mục 2 (ước lượng toạ
  độ trước, rồi mới xét nó rơi trong hay ngoài khung ảnh) và **điều kiện tư thế** trong dòng "người bị
  cắt ở mép ảnh" (đứng và ngồi dùng hai tỉ lệ khác nhau). Cả hai đều sinh ra từ việc so Ca 1 với Ca 2.

### Ghi chú về 8 cảnh báo còn lại sau khi sửa — đã kiểm từng ca, không sửa

Cả 8 đều là *false positive*, thuộc đúng **hai nhóm nguyên nhân kỹ thuật**:

**Nhóm 1 — "có N khớp `v=0` trong khi cả người nằm gọn giữa ảnh"** (4 cảnh báo: `train_04` ×2,
`train_10`, `train_13` ông mặc vest). CVAT tính bounding box của skeleton **bao quanh các điểm đã
đặt**. Khớp để `Outside` thì không có toạ độ, nên khi người bị cắt ở mép dưới, box tự co lên tới
khớp thấp nhất còn lại và trông như "nằm gọn giữa ảnh", dù người đó thật sự chạm mép khung hình.

**Nhóm 2 — "vai/hông nằm ngược chiều so với hai mắt"** (4 cảnh báo: `train_16` người áo đỏ ×2,
`train_13` người nhoè sát mép trái ×2). Phép thử này so hướng nối hai mắt với hướng nối hai vai.
Khi mặt **nghiêng** hoặc **bị nhoè**, hai mắt gần như chồng lên nhau nên hiệu số chỉ còn **1–2 px** —
đó là nhiễu, không phải hướng. Script chỉ đặt ngưỡng tối thiểu cho hướng thân (`> 0,01`) mà không
đặt ngưỡng cho hướng mắt, nên vẫn kêu. Cách kiểm thay thế đã dùng: đọc **cả chuỗi** vai → khuỷu →
cổ tay → hông → gối → cổ chân; nếu tất cả cùng một chiều thì nhãn đúng.

| Người | mắt | vai | khuỷu | hông | gối | cổ chân | Kết luận |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | --- |
| `train_16` áo đỏ #23 | **+1,7** | −38,5 | −112,1 | −35,6 | −48,8 | −28,9 | thân nhất quán → đúng |
| `train_13` người nhoè | **+1,4** | −14,3 | −6,0 | −16,2 | −11,0 | −18,6 | thân nhất quán → đúng |

*(Đối chứng: `train_16` người áo trắng #15 trước khi sửa có vai −51,4 nhưng hông +44,8 — **thân trên
và thân dưới ngược nhau**, đó mới là lỗi thật, và đã sửa.)*
