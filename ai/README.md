# Face Security System

Hệ thống giám sát an ninh và nhận dạng khuôn mặt theo mô hình **AI + Backend + Mobile App**.

Dự án này được xây theo hướng thực dụng:
- **Face Detection**: phát hiện khuôn mặt/người trong ảnh hoặc video.
- **Face Recognition**: trích embedding và so khớp với gallery đã đăng ký.
- **Tracking**: theo dõi đối tượng qua video thời gian thực.
- **Behavior Engine**: phát hiện các tình huống bất thường theo luật (rule-based).
- **Backend API**: cung cấp REST/WebSocket để nhận sự kiện và trả dữ liệu cảnh báo.
- **Mobile App**: hiển thị danh sách cảnh báo và thông tin giám sát.

## 1. Tổng quan hệ thống

Pipeline chính:

```mermaid
flowchart LR
    A[Camera / Video / Image] --> B[Tiền xử lý ảnh & video]
    B --> C[Face Detection]
    C --> D[Face Recognition]
    D --> E[Tracking]
    E --> F[Behavior Engine]
    F --> G[Cảnh báo / Log / Snapshot]
    G --> H[Backend API]
    H --> I[Mobile App]
```

Tài liệu thiết kế của đồ án xác định đề tài là: **xây dựng hệ thống nhận dạng khuôn mặt, giám sát an ninh, phân tích video thời gian thực**; đồng thời nhấn mạnh các mục tiêu như nhận diện người lạ, phát hiện xâm nhập trái phép, và cảnh báo bất thường.  
Trong phần kiến trúc AI, tài liệu đề xuất một pipeline thực dụng gồm **YOLOv8-face hoặc RetinaFace** cho detection, **ArcFace hoặc FaceNet** cho recognition, **ByteTrack** cho tracking, và **rule-based behavior** cho các tình huống như vùng cấm, loitering, và người lạ. citeturn0file1turn0file0

## 2. Điểm nổi bật của repo

- Có sẵn notebook cho từng bước của pipeline AI.
- Có script tiền xử lý dữ liệu ảnh/video.
- Có khung backend FastAPI.
- Có cấu trúc tách biệt giữa `ai/`, `backend/` và `app flutter/`.
- Có notebook đánh giá mô hình bằng các chỉ số như FAR/FRR, mAP, FPS, ID switch rate.

## 3. Cấu trúc thư mục

```text
face-security-system-haituan/
├── ai/
│   ├── notebooks/
│   │   ├── 01_data_preprocessing.ipynb
│   │   ├── 02_face_detection_recognition.ipynb
│   │   ├── 03_tracking_behavior.ipynb
│   │   └── 04_evaluation.ipynb
│   ├── src/
│   │   ├── detection/
│   │   ├── recognition/
│   │   ├── tracking/
│   │   ├── behavior/
│   │   └── utils/
│   └── requirements.txt
├── backend/
│   ├── app/
│   │   ├── main.py
│   │   ├── config.py
│   │   ├── database.py
│   │   ├── models/
│   │   ├── schemas/
│   │   ├── routers/
│   │   ├── services/
│   │   └── tests/
│   └── requirements.txt
├── app flutter/
│   └── README.md
└── README.md
```

## 4. AI Pipeline trong dự án

### 4.1 Tiền xử lý dữ liệu
Notebook `01_data_preprocessing.ipynb` phụ trách:
- tạo cấu trúc thư mục dataset,
- tải / mô phỏng dataset danh tính,
- phát hiện và căn chỉnh khuôn mặt,
- tách gallery / probe,
- trích frame từ video,
- augmentation cho ảnh gallery.

Trong notebook này có các hàm chính như:
- `imread_unicode`
- `imwrite_unicode`
- `detect_and_align`
- `is_blurry`
- `is_face_too_small`
- `extract_frames`

### 4.2 Detection + Recognition + Tracking
Notebook `02_face_detection_recognition.ipynb` triển khai:
- khởi tạo InsightFace,
- xây gallery embedding,
- cosine similarity matching,
- YOLOv8 + ByteTrack cho luồng cascade.

Các hàm chính:
- `get_embedding`
- `match_face`
- `run_cascade_on_video`

### 4.3 Behavior Engine
Notebook `03_tracking_behavior.ipynb` xây dựng:
- vùng cấm (ROI),
- phát hiện loitering,
- phát hiện người lạ,
- cơ chế cooldown để tránh spam cảnh báo.

Class chính:
- `BehaviorRuleEngine`

### 4.4 Evaluation
Notebook `04_evaluation.ipynb` tập trung đánh giá:
- **Recognition**: FAR / FRR / EER
- **Detection**: WIDER FACE
- **Tracking**: MOT17
- **Hệ thống**: độ trễ end-to-end, false alarm rate

## 5. Backend

Thư mục `backend/` hiện là khung triển khai FastAPI cho:
- enrollment,
- alert list / history,
- websocket push cảnh báo,
- lưu dữ liệu vào database.

Các model dự kiến:
- `Person`
- `Camera`
- `Alert`

Các service dự kiến:
- `pipeline_runner`
- `alert_service`
- `websocket_manager`

## 6. Mobile App

Thư mục `app flutter/` là phần giao diện mobile.  
Theo tài liệu thiết kế, ứng dụng di động dùng để:
- xem danh sách cảnh báo,
- xem chi tiết snapshot,
- nhận push notification,
- theo dõi trạng thái hệ thống.

## 7. Bộ công nghệ sử dụng

### AI
- Python
- OpenCV
- InsightFace / ArcFace
- YOLOv8
- ByteTrack
- ONNX Runtime
- scikit-learn
- pandas, numpy

### Backend
- FastAPI
- SQLite hoặc PostgreSQL
- WebSocket
- Firebase Admin SDK

### Mobile
- Flutter hoặc Android client
- Firebase Cloud Messaging
- Retrofit / API client
- RecyclerView / list view

## 8. Chạy dự án

### 8.1 Cài dependencies AI
```bash
cd ai
pip install -r requirements.txt
```

### 8.2 Cài dependencies backend
```bash
cd backend
pip install -r requirements.txt
```

### 8.3 Chạy notebook
Mở lần lượt các notebook trong `ai/notebooks/` theo thứ tự:
1. `01_data_preprocessing.ipynb`
2. `02_face_detection_recognition.ipynb`
3. `03_tracking_behavior.ipynb`
4. `04_evaluation.ipynb`

## 9. Luồng phát triển của nhóm

Theo kế hoạch phân công:
- **TV1**: AI / machine learning / deep learning
- **TV2**: xử lý dữ liệu ảnh-video, tạo API, backend
- **TV3**: Flutter app

Tài liệu tiến độ cũng chia lộ trình 7 tuần, gồm:
- nghiên cứu và thiết kế,
- thu thập và tiền xử lý dữ liệu,
- face detection + recognition,
- tracking + behavior + API,
- tích hợp end-to-end,
- đánh giá và tối ưu,
- hoàn thiện báo cáo.  
Bản kế hoạch còn nêu tuần dự phòng để xử lý phát sinh và luyện demo. citeturn0file0

## 10. Chỉ số đánh giá

Các chỉ số nên đưa vào báo cáo:
- Detection: `mAP@0.5`, `FPS`
- Recognition: `Accuracy`, `FAR`, `FRR`
- Tracking: `ID switch rate`
- Hệ thống: `end-to-end latency`, `false alarm rate`

Đây cũng là bộ chỉ số được tài liệu pipeline đề xuất cho chương đánh giá hệ thống. citeturn0file1

## 11. Lưu ý quan trọng

- Nhiều file trong repo hiện là **prototype / skeleton**, đặc biệt ở `backend/app/` và `ai/src/`.
- Các notebook đang dùng đường dẫn cứng theo máy local, nên cần sửa lại trước khi chạy trên máy khác.
- README này được viết lại theo đúng cấu trúc repo hiện tại và hai file tài liệu thiết kế của đồ án.

## 12. Gợi ý cải tiến tiếp theo

- Chuẩn hoá lại đường dẫn bằng `.env`.
- Tách logic notebook thành module Python thật.
- Hoàn thiện FastAPI backend.
- Thêm README riêng cho `ai/` và `backend/`.
- Viết sơ đồ kiến trúc, ER diagram và sequence diagram cho báo cáo.

## 13. Trạng thái hiện tại

Repo đang ở trạng thái:
- **AI notebook**: có nội dung và pipeline tương đối rõ.
- **Backend**: có khung, cần triển khai thêm.
- **Mobile app**: mới ở mức khởi tạo tài liệu / scaffold.

---

Nếu cần, có thể tách README này thành 3 phần riêng:
1. README tổng,
2. README cho `ai/`,
3. README cho `backend/`.