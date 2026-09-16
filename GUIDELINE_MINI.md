# Mini guideline - Solo  |  người gán: Hoàng Công Chứ  |  ngày: `16/09/2026`

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

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Đặt điểm hông tại vị trí giải phẫu ước lượng. Nếu hông bị che nhưng vẫn nằm trong khung ảnh thì gán `v=1`. | Đảm bảo vị trí khớp nhất quán, không bỏ khớp chỉ vì bị quần áo che. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Ước lượng vị trí tai và gán `v=1` nếu vẫn còn trong khung ảnh. | Tai bị che một phần nhưng vẫn thuộc cơ thể và cần được gán để giữ đủ 17 khớp. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Các khớp nằm ngoài khung ảnh gán `v=0` và không đặt điểm. Các khớp còn trong ảnh vẫn gán bình thường. | Phân biệt rõ giữa khớp bị che (`v=1`) và khớp nằm ngoài ảnh (`v=0`). |
| Cổ tay nằm sau tay lái / sau thân mình | Ước lượng vị trí cổ tay và gán `v=1` nếu cổ tay vẫn nằm trong khung ảnh. | Cổ tay chỉ bị che khuất, không phải biến mất khỏi ảnh. |
| Hai người chồng lên nhau | Gán riêng từng người, không dùng khớp của người này cho người kia. Khớp bị che vẫn gán `v=1`. | Giữ đúng skeleton của từng người và tránh nhầm lẫn giữa các đối tượng. |
| Người nhỏ đến mức nào thì không gán nữa | Không gán nếu người quá nhỏ hoặc không thể xác định đáng tin cậy phần lớn các khớp theo guideline của bài. | Tránh tạo nhãn không chính xác làm giảm chất lượng dữ liệu huấn luyện. |

Với mỗi luật, chèn **một ảnh mẫu** (screenshot từ CVAT) thay vì chỉ viết một câu.
Slide 12 nói rõ: khớp không có bề mặt nhìn thấy được thì phải có ảnh mẫu, không phải
một câu văn chung chung.

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_02`, người thứ `1`, khớp `left_shoulder / right_shoulder`

- Mơ hồ ở chỗ nào:Khó xác định vai trái và vai phải khi người quay nghiêng.
- Bạn quyết thế nào:Gán theo trái/phải của cơ thể người.
- Vì sao:COCO quy định trái/phải theo cơ thể, không theo hướng nhìn của ảnh
- Nếu người khác quyết ngược lại thì model học sai cái gì:Model sẽ học sai quy ước trái/phải, dẫn đến dự đoán nhầm vai và kéo theo sai toàn bộ cánh tay.

### Ca 2 - ảnh `train_04`, người thứ `1`, khớp `left_knee, right_knee, left_ankle, right_ankle`

- Mơ hồ ở chỗ nào:Chân bị che một phần nhưng người vẫn nằm hoàn toàn trong khung ảnh.
- Bạn quyết thế nào:Ban đầu gán v=0
- Vì sao:Nghĩ rằng các khớp không nhìn thấy nên coi là ngoài ảnh.
- Nếu người khác quyết ngược lại thì model học sai cái gì:Theo kết quả kiểm tra, đây là lỗi. Các khớp này phải dùng v=1 vì chỉ bị che, không ra khỏi ảnh. Nếu giữ v=0 thì model sẽ học rằng các khớp không tồn tại và bị mất điểm OKS.

### Ca 3 - ảnh `train_16`, người thứ `1`, khớp `left_hip / right_hip`

- Mơ hồ ở chỗ nào:Người đứng nghiêng nên rất dễ đảo trái và phải.
- Bạn quyết thế nào:Gán theo trái/phải cơ thể
- Vì sao:Tuân theo quy tắc của COCO.
- Nếu người khác quyết ngược lại thì model học sai cái gì:Skeleton sẽ bị đảo hai bên, ảnh hưởng đến toàn bộ phần chân và các bài toán Pose Estimation.

## 4. Sau khi so visibility report với lần gán đầu

- Khớp lệch `%v=1` nhiều nhất: đầu gối và mắt cá chân.

- Nguyên nhân là **guideline chưa được áp dụng đúng**, đặc biệt ở việc phân biệt giữa `v=0` (khớp nằm ngoài khung ảnh) và `v=1` (khớp bị che nhưng vẫn nằm trong khung ảnh). Ngoài ra còn xuất hiện lỗi nhầm trái/phải ở vai và hông.

- Luật mới bổ sung vào mục 2 sau khi thống nhất:

  - Chỉ sử dụng `v=0` khi khớp thực sự nằm ngoài khung ảnh.
  - Nếu khớp bị che nhưng vẫn nằm trong khung ảnh thì phải gán `v=1` và ước lượng vị trí khớp.
  - Luôn xác định trái/phải theo cơ thể người, không theo hướng nhìn của ảnh.
  - Trước khi nộp, kiểm tra lại bằng `visualize_pose.py` để phát hiện lỗi đảo vai và hông.