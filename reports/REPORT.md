# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: **Hoàng Công Chứ**  
Nhóm: **SOLO**  
Ngày: **16/09/2026**

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 349 / 115 / 29 |
| Thời gian trung bình mỗi ảnh | Chưa ghi nhận |

Ba khớp có `%v=1` cao nhất:

1. left_ear: 59%
2. right_ear: 45%
3. left_eye: 34%

Chúng có đúng là những khớp bạn thấy khó gán nhất không?

Không hoàn toàn. Các khớp có tỷ lệ `v=1` cao chủ yếu là những khớp thường bị che như tai và mắt do tóc, góc nhìn hoặc vật che. Tuy nhiên các khớp tôi gặp khó khăn nhiều hơn khi gán là vai, hông và chân vì dễ nhầm trái/phải hoặc khó phân biệt giữa bị che và nằm ngoài ảnh. Bằng chứng là công cụ `check_pose_labels.py` phát hiện lỗi đảo trái/phải ở vai, hông trong `train_02` và `train_16`, cùng lỗi visibility ở chân trong `train_04`, `train_10`, `train_11`, `train_13`.

---

## 2. Chấm với gold

Kết quả lần chạy đầu trước rework:

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.176 | Chưa chạy lại |
| OKS@0.50 | 0.000 | Chưa chạy lại |
| OKS@0.75 | 0.000 | Chưa chạy lại |
| Lỗi `dao_trai_phai` | 0 | Chưa chạy lại |
| Lỗi `nham_nguoi` | 1 | Chưa chạy lại |
| Lỗi `xoa_khop_bi_che` | 0 | Chưa chạy lại |

**Kết quả lỗi trước rework:**

- Gold có 2 người, nhãn của tôi ghép được 1 người, thiếu 1 người.
- Có 1 lỗi thiếu hẳn một người.
- Có 1 lỗi trượt hẳn: `train_02.jpg` người #2, khớp `left_elbow`.
- Có 11 lỗi lệch nhẹ.
- Có 5 trường hợp gold để `v=0` nhưng nhãn của tôi vẫn có gán điểm. Các trường hợp này chỉ dùng để chẩn đoán, không bị trừ điểm vì cần tuân theo guideline của lớp: khớp bị che nhưng còn trong ảnh phải gán `v=1`.

**Tôi đã sửa gì giữa hai lần chạy:**

- `train_02.jpg` + người thứ 1 + toàn bộ keypoint: kiểm tra lại vì skeleton bị thiếu người so với gold. Cần bổ sung lại người bị bỏ sót.
- `train_02.jpg` + người thứ 2 + `left_elbow`: kiểm tra lại vị trí điểm khuỷu tay vì bị đánh giá là trượt hẳn, điểm đặt không đúng vị trí khớp.
- `train_04` + người thứ 2 + `left_hip`: sửa visibility vì điểm có tọa độ ngoài ảnh nhưng đang để `v=2`, đổi thành `v=0` nếu thực sự ngoài khung.
- Các trường hợp `v=0` ở `train_04`, `train_10`, `train_11`, `train_13`: kiểm tra lại theo guideline, chuyển sang `v=1` nếu khớp bị che nhưng vẫn nằm trong ảnh.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?**

Trong kết quả so với gold chưa phát hiện lỗi `dao_trai_phai`.

Tuy nhiên công cụ kiểm tra nội bộ phát hiện dấu hiệu đảo trái/phải tại:
- `train_02`: `left_shoulder/right_shoulder`, `left_hip/right_hip`.
- `train_16`: `left_shoulder/right_shoulder`, `left_hip/right_hip`.

Các ảnh này khó vì người trong ảnh có góc quay nghiêng, dễ nhầm giữa hướng nhìn trong ảnh và trái/phải của cơ thể. Nguyên nhân là chưa áp dụng đủ quy tắc COCO khi xác định bên trái/phải.

## 3. Kiểm chéo

Bạn cùng nhóm: **SOLO (không có đối chiếu nhóm)**

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| Chưa có dữ liệu | - | - | - | Không thực hiện kiểm chéo với người khác |
| Chưa có dữ liệu | - | - | - | Không thực hiện kiểm chéo với người khác |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

- Nếu khớp vẫn nằm trong phạm vi ảnh nhưng bị che bởi quần áo, vật thể hoặc người khác thì phải đặt điểm ước lượng và gán `v=1`.
- Chỉ gán `v=0` khi khớp thực sự nằm ngoài mép ảnh, không thể đặt vị trí trong ảnh.

---

## 4. Model

Chưa có file `outputs/eval_model.json`, chưa thực hiện đánh giá model.

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | Chưa có | Chưa có | Chưa có |
| pose_mAP50-95 | Chưa có | Chưa có | Chưa có |
| pose_precision | Chưa có | Chưa có | Chưa có |
| pose_recall | Chưa có | Chưa có | Chưa có |
| box_mAP50-95 | Chưa có | Chưa có | Chưa có |

### Trả lời năm câu hỏi ở cuối notebook

1. `pose_mAP50-95` thay đổi bao nhiêu?

Chưa có kết quả đánh giá model nên chưa xác định được mức thay đổi. Cần dựa trên `outputs/eval_model.json` sau khi chạy fine-tune.

2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu?

Chưa có kết quả model. Tuy nhiên trong bài toán Pose Estimation, phát hiện người thường dễ hơn xác định chính xác vị trí 17 khớp vì keypoint yêu cầu độ chính xác không gian cao hơn.

3. Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43:

Chưa có ảnh test và kết quả dự đoán model.

4. Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model?

Chưa có kết quả OKS giữa model và nhãn.

5. Ảnh bạn gán tệ nhất có cũng là ảnh model đoán tệ nhất không?

Chưa có kết quả model để so sánh.

---

## 5. Một rule evidence bạn đã dùng

Ví dụ: `train_04` + người thứ 2 + `left_hip`

Khớp `left_hip` nằm ở vùng sát mép ảnh và tọa độ xuất hiện ngoài phạm vi ảnh (`y > 1`). Vì điểm này không còn nằm trong vùng quan sát nên không thể ước lượng vị trí khớp trong ảnh. Theo guideline, trường hợp này phải gán `v=0` và không đặt chấm. Nếu giữ `v=2`, model sẽ học rằng khớp nhìn thấy được ở vị trí không hợp lệ.
