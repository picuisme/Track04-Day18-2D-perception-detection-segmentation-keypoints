# Báo cáo Lab 18 — 2D Perception

Link notebook đã chạy: https://github.com/picuisme/Track04-Day18-2D-perception-detection-segmentation-keypoints/blob/main/lab_2d_perception_student.ipynb

Môi trường: Colab GPU T4, ultralytics 8.4.171, torch 2.11.0+cu130. Số liệu dưới đây lấy từ `ket_qua.json` của chính lần chạy này.

## Bonus 4C — tập val lật gương: metric nào đã che lỗi flip_idx?

| Model | Pose mAP50-95 — val gốc | Pose mAP50-95 — val lật gương |
|---|---|---|
| flip_idx giải phẫu | 0.457 | 0.439 |
| flip_idx đồng nhất | 0.417 | 0.298 |

Metric che lỗi chính là Pose mAP trên tập val gốc. Mọi con hổ trong tiger-pose đều quay phải (train 210/0, val 53/0), nên với flip_idx đồng nhất, model học quy ước "right_* là chân phía camera", và trên val gốc quy ước đó vẫn trùng với nhãn. Kết quả là hai model chỉ chênh 0.04 trên val gốc (0.457 và 0.417), mức chênh dễ bị coi là nhiễu giữa hai lần train.

Lỗi chỉ lộ ra khi tập đánh giá chứa hướng quay mà tập train không có. Trên val lật gương (ảnh lật ngang, nhãn đổi theo quy ước giải phẫu), model flip_idx giải phẫu gần như giữ nguyên (0.457 → 0.439, giảm 0.018), còn model flip_idx đồng nhất tụt từ 0.417 xuống 0.298 (giảm 0.119, khoảng 29%) vì nó gán nhãn trái/phải theo phía gần camera chứ không theo giải phẫu.

Bài học cho thiết kế tập val: tập val phải phủ phân phối triển khai, ở đây là cả hổ quay trái lẫn quay phải. Nếu không thu được ảnh thật thì ít nhất nên thêm một tập val lật gương như trên và báo cáo mAP trên cả hai tập, kèm số ảnh nghi đảo trái/phải (ở 4B: 4/53 ảnh).

## Kết quả chính (4B)

Fine-tune YOLO26n-pose trên tiger-pose, flip_idx giải phẫu, 40 epoch, imgsz 640, train 4.2 phút: Box mAP50-95 0.930, Pose mAP50 0.995, Pose mAP50-95 0.457.

## Phần chưa làm

Bài tập về nhà (auto-label + fine-tune yolo26n-seg, hoặc export ONNX và đo latency) chưa thực hiện.
