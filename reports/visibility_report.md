# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 28 skeleton, trung bình 15.96 khớp có v > 0 mỗi người
- Tổng: v=2 304 | v=1 143 | v=0 29

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 21 | 7 | 0 | 25% |
| 1 | left_eye | 19 | 9 | 0 | 32% |
| 2 | right_eye | 19 | 9 | 0 | 32% |
| 3 | left_ear | 9 | 19 | 0 | 68% |
| 4 | right_ear | 11 | 17 | 0 | 61% |
| 5 | left_shoulder | 24 | 4 | 0 | 14% |
| 6 | right_shoulder | 27 | 1 | 0 | 4% |
| 7 | left_elbow | 21 | 7 | 0 | 25% |
| 8 | right_elbow | 24 | 4 | 0 | 14% |
| 9 | left_wrist | 18 | 10 | 0 | 36% |
| 10 | right_wrist | 19 | 8 | 1 | 29% |
| 11 | left_hip | 19 | 8 | 1 | 29% |
| 12 | right_hip | 21 | 6 | 1 | 21% |
| 13 | left_knee | 16 | 8 | 4 | 29% |
| 14 | right_knee | 14 | 10 | 4 | 36% |
| 15 | left_ankle | 12 | 7 | 9 | 25% |
| 16 | right_ankle | 10 | 9 | 9 | 32% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
