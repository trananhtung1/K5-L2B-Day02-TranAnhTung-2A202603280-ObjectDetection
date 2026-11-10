# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Trần Anh Tùng
- MSSV: 2A202603280
- Email: trantung110604@gmail.com
- Lớp: L2B
- Link repo bài làm: https://github.com/trananhtung1/K5-L2B-Day02-TranAnhTung-2A202603280-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/github/trananhtung1/K5-L2B-Day02-TranAnhTung-2A202603280-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb

## 2. Schema và mini guideline

Mini guideline: [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md).

- `car` gồm sedan, SUV, taxi và pickup; pickup không được gán là `truck`.
- `truck` là xe tải chở hàng hoặc xe đầu kéo; `bus` là xe buýt/minibus chở khách; `van` là van/minivan có thân liền khối.
- Box chỉ ôm phần xe nhìn thấy. Xe bị che vẫn được gán khi xác định chắc class; xe cắt mép ảnh có box chạm mép; xe quá nhỏ hoặc mơ hồ không được tự đoán class.
- Ba ca đã ghi nhận: `drive_001` có xe bị cột điện che một phần; `drive_014` có xe bị cắt mép ảnh; `drive_030` có phương tiện xa/mờ không xác định chắc class nên không gán.
- Phần cần giảng viên làm rõ: Không có nội dung cần làm rõ thêm sau khi đối chiếu schema và mini guideline.

## 3. Annotation và self-QC

- Số file nhãn train: 50.
- Kết quả checker: `ĐẠT cấu trúc: 50/50 file nhãn hợp lệ.`
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: `drive_001` đến `drive_010`.
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: Đã kiểm tra phạm vi class, số lượng box và độ sát box; các nhãn được rework trước gold QC. Checker đạt 50/50; gold QC cuối đạt F1 0.8133 và mean IoU 0.8556.
- Commit khóa nhãn ban đầu: Không có commit khóa riêng trước gold QC; phiên bản cuối sau rework là 0ebb4bd.

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa |
|---|---:|---:|
| TP | 482 | 540 |
| FP | 687 | 135 |
| FN | 171 | 113 |
| Precision | 0.4123 | 0.8000 |
| Recall | 0.7381 | 0.8270 |
| F1 | 0.5291 | 0.8133 |
| mean_iou_of_matches | 0.8535 | 0.8556 |
| passed | false | true |

Kết quả cuối được lưu tại `outputs/annotation_evaluation.json`. Sau gold QC, bộ nhãn đã được rà lại các mismatch, cập nhật nhãn trong task gốc rồi export và đánh giá lại đến khi đạt cả hai ngưỡng bắt buộc: F1 >= 0.80 và mean IoU >= 0.70. Các đợt rework tập trung giảm box false positive, rà class và bổ sung/sửa các box chưa match; kết quả cuối là `passed: true`.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: Google Colab GPU NVIDIA L4; PyTorch 2.14.1+cu130; Ultralytics 8.4.208.
- Các tham số thay đổi so với notebook nếu có và lý do: Không thay đổi; train YOLO26n trong 50 epochs, `imgsz=640`, `batch=-1`.
- Đường dẫn best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`.

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.3899 |
| Recall | 0.5038 |
| mAP50 | 0.4278 |
| mAP50–95 | 0.2916 |

Đã đặt hai folder kết quả tại `assets/eval_predictions/` và `assets/yolo26_runs/`. Ảnh prediction nằm trong `assets/eval_predictions/visuals/`; log train và ảnh metrics nằm trong `assets/yolo26_runs/`.

Trả lời bốn câu hỏi cuối notebook:

1. truck và bus có recall thấp nhất, cùng bằng 0%. Ví dụ, trong drive_052, một truck bị model dự đoán là car. Điều này có thể liên quan số mẫu train của các lớp hiếm ít hơn car.
2. mAP50 của model là 0.4278, cao hơn mAP50–95 là 0.2916. Điều này cho thấy model có thể phát hiện đúng vật thể ở ngưỡng IoU 0.50, nhưng vị trí/kích thước box chưa đủ sát để vượt các ngưỡng IoU nghiêm ngặt hơn. Cần kiểm tra các box bị lỏng, lệch tâm, cắt thiếu phần xe hoặc ôm sang nền/xe bên cạnh.
3. Hai nhầm lẫn đáng chú ý là truck dự đoán thành car và bus dự đoán thành car/van. Confusion matrix cho thấy cả 5 truck trên eval bị dự đoán thành car; 2 bus không có dự đoán đúng, trong đó một bị nhầm là car và một là van. Các lỗi này liên quan các ranh giới cần làm rõ trong guideline, đặc biệt phân biệt xe tải với pickup/car và bus với van. Tuy nhiên, confusion matrix chỉ cho thấy lỗi model; chưa đủ để kết luận annotation sai, vì truck và bus là các lớp ít mẫu hơn car.
4. Không dùng 10 ảnh eval để train vì khi đó model đã nhìn thấy dữ liệu dùng để đánh giá, khiến metrics bị lạc quan và không phản ánh khả năng tổng quát hóa. Trong notebook, eval đóng vai trò validation: được dùng để theo dõi metric qua các epoch và chọn best.pt. Do dùng để chọn checkpoint, eval cũng không phải một test set độc lập.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Push repo GitHub | Credential GitHub cũ không hợp lệ; password authentication bị từ chối. | Tạo fine-grained personal access token với quyền Contents: Read and write cho đúng repository và đăng nhập lại khi `git push`. | Push thành công commit `0ebb4bd`. |
| Gold QC | Lần đánh giá đầu có F1 thấp do nhiều FP và FN. | Rework nhãn, export lại từ task gốc, chạy checker và evaluator lại. | Kết quả cuối: F1 0.8133, mean IoU 0.8556, đạt gate. |

## 7. Tự đánh giá theo rubric

- Định dạng YOLO: đạt checker 50/50 file.
- Annotation QC gold set: đạt (`F1 = 0.8133`, `mean_iou_of_matches = 0.8556`).
- Mini guideline: đã có định nghĩa class, quy tắc box và ba ca mơ hồ.
- Pipeline YOLO: đã chạy train/eval và đã lưu metrics, predictions, log vào thư mục assets.
## 8. Công cụ AI và tài liệu tham khảo

- Codex: dùng để giải thích schema YOLO, hướng dẫn thao tác CVAT, đọc và giải thích kết quả checker/evaluator, hướng dẫn Git/GitHub và diễn đạt báo cáo. Việc quan sát ảnh, gán nhãn, rework nhãn và chạy Colab được học viên tự thực hiện và tự kiểm tra.
- Tài liệu trong repo: `README.md`, `GUIDE.md`, `RULES.md`, `RUBRIC.md`, `SUBMISSION.md`.
- [CVAT documentation](https://docs.cvat.ai/): tham khảo định dạng Ultralytics YOLO và thao tác export/import.
- [GitHub documentation](https://docs.github.com/): tham khảo xác thực Git qua HTTPS bằng personal access token.
