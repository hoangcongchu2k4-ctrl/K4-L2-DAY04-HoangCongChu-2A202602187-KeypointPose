# Reviewer checklist - điền khi kiểm bài người khác
 
Người gán:  Hoàng Công Chứ   Người kiểm:   Hoàng Công Chứ  Ngày: 16/09/2026

Chạy trước khi soi bằng mắt:

```bash
python3 tools/check_pose_labels.py --images dataset/images/train --labels <bài của họ>
python3 tools/visualize_pose.py --images dataset/images/train --labels <bài của họ> --out /tmp/vis_review
python3 tools/visibility_report.py --labels dataset/labels/train --compare <bài của họ>
```

| | Mục kiểm | Đạt? | Ghi chú / ảnh nào |
| --- | --- | --- | --- |
| 1 | Mọi người trong ảnh đều có đủ 17 điểm, không ai bị thiếu | ☑ |Đã kiểm tra 20 ảnh, 29 skeleton, mỗi người có đủ 17 keypoint. |
| 2 | Bật đường nối: không có xương nào cắt chéo ở vai hoặc hông | ☐ | Cần xem lại train_02 và train_16, có dấu hiệu đảo trái/phải ở vai và hông|
| 3 | Không có xương nào kéo dài sang một cơ thể khác | ☑ | Không phát hiện skeleton nối nhầm sang người khác.|
| 4 | Khớp bị che dùng `v = 1` **và có chấm**, không phải `v = 0` | ☐ | train_04, train_10, train_11, train_13 có một số khớp bị che nhưng đang để v=0.|
| 5 | `v = 0` chỉ xuất hiện ở khớp thật sự ra ngoài mép ảnh | ☐ |train_04 người thứ 2 có left_hip v=2 nhưng tọa độ ngoài ảnh (0.276, 1.004).|
| 6 | Không có dấu hiệu dùng `Hidden` (điểm `v = 2` nằm ở chỗ vô lý) | ☑ |Không sử dụng Hidden. |
| 7 | Export đúng **COCO Keypoints 1.0**: mảng `keypoints` có 51 số mỗi người | ☑ | Đã chuyển đổi thành công từ COCO sang YOLO Pose.|
| 8 | Bản YOLO Pose: mỗi dòng 56 số, `kpt_shape: [17, 3]` | ☑ | Tool kiểm tra định dạng đạt yêu cầu.|
| 9 | Visibility report đã nộp, và hai bảng đã được đặt cạnh nhau | ☑ | Đã tạo visibility report: v=2 349, v=1 115, v=0 29.|
| 10 | Mọi ca không rõ đều được ghi trong `GUIDELINE_MINI.md` | ☑ | Đã ghi các trường hợp hông, tai, cổ tay, người che nhau.|
| 11 | `check_pose_labels.py` chạy 0 lỗi | ☐ | Hiện còn 1 lỗi cần sửa: train_04.txt:2 left_hip có v=2 nhưng tọa độ ngoài ảnh.|

## Lỗi tìm được

## Lỗi tìm được

| Ảnh | Người thứ | Khớp | Lỗi gì | Sửa thế nào |
| --- | ---: | --- | --- | --- |
| train_02 | 1 | left_shoulder / right_shoulder | Vai trái và vai phải có dấu hiệu bị đảo ngược so với vị trí mắt, làm đường nối skeleton sai hướng. | Mở `visualize_pose.py`, xác định lại trái/phải theo cơ thể người và đổi lại vị trí hai điểm vai. |
| train_02 | 1 | left_hip / right_hip | Hông trái và hông phải có dấu hiệu bị đảo ngược. | Kiểm tra lại hướng cơ thể, đổi lại hai điểm hông theo chuẩn COCO keypoints. |
| train_04 | 1 | left_knee, right_knee, left_ankle, right_ankle | Khớp nằm trong ảnh nhưng bị gán `v=0`, trong khi trường hợp này phải là khớp bị che. | Đặt lại visibility thành `v=1` và giữ điểm ước lượng tại vị trí khớp. |
| train_04 | 2 | left_hip | Điểm `left_hip` có `v=2` nhưng tọa độ `y=1.004` vượt ngoài phạm vi ảnh. | Nếu khớp nằm ngoài ảnh thì đổi thành `v=0`; nếu vẫn nhìn thấy trong ảnh thì kéo điểm về đúng vị trí. |
| train_10 | 1 | knee / ankle | Có 4 khớp `v=0` trong khi người vẫn nằm gọn trong ảnh, khả năng cao là khớp bị che. | Đổi các khớp bị che sang `v=1`, không xóa điểm. |
| train_11 | 1 | knee / ankle | Có 4 khớp `v=0` trong khi khớp không ra khỏi ảnh. | Đổi sang `v=1` và đặt điểm ước lượng theo vị trí giải phẫu. |
| train_13 | 3 | knee / ankle | Có 4 khớp `v=0` sai quy tắc visibility. | Kiểm tra lại trong CVAT, đổi sang `v=1` nếu chỉ bị che khuất. |
| train_16 | 2 | left_shoulder / right_shoulder | Vai trái/phải có dấu hiệu đảo ngược. | Kiểm tra lại skeleton bằng `visualize_pose.py` và sửa thứ tự keypoint. |
| train_16 | 2 | left_hip / right_hip | Hông trái/phải có dấu hiệu đảo ngược. | Đổi lại vị trí keypoint theo trái/phải của cơ thể người. |
## Hai câu kết luận

- Lỗi lặp đi lặp lại nhiều nhất của bài này:Nhầm giữa khớp bị che (v=1) và khớp nằm ngoài ảnh (v=0), ngoài ra có lỗi xác định trái/phải ở vai và hông.
- Nó là lỗi **thao tác** hay lỗi **guideline chưa rõ**?
Chủ yếu là lỗi guideline chưa rõ, do chưa phân biệt chính xác trường hợp bị che và ngoài khung ảnh. Một phần nhỏ là lỗi thao tác khi đặt sai visibility hoặc đảo vị trí trái/phải.
