# LEARNING PROGRESS PREDICTION

## 1. Giới thiệu bài toán

Dự án tập trung vào việc dự báo số tín chỉ thực tế sinh viên sẽ hoàn thành (`TC_HOANTHANH`) sau mỗi học kỳ tại Đại học U. Thông qua việc phân tích dữ liệu tuyển sinh và lịch sử học tập, mô hình giúp:

* Phát hiện sớm nhóm sinh viên có nguy cơ chậm tiến độ.
* Hỗ trợ Cố vấn học tập đưa ra lộ trình đào tạo phù hợp.
* Tối ưu hóa công tác quản lý của phòng đào tạo.

## 2. Cấu trúc thư mục

```
├── outputs/
│   ├── plots/                # Các biểu đồ phân tích (SHAP, LIME, Error Analysis)
│   ├── metrics/              # Kết quả đánh giá mô hình (JSON/CSV)
│   ├── explainability/       # Kết quả giải thích mô hình (SHAP/LIME)
│   └── submission.csv        # File kết quả cuối cùng nộp kaggle
├── admission.csv             # Dữ liệu tuyển sinh
├── academic_records.csv      # Lịch sử học tập
├── main_analysis.ipynb       # Notebook xử lý chính (End-to-end)
├── requirements.txt          # Các thư viện cần thiết
├── README.md                 # Hướng dẫn này
└── test.csv                  # Danh sách SV cần dự báo (HK1 2024-2025)
```

## 3. Quy trình thực hiện (Pipeline)

Dự án được xây dựng gồm 5 giai đoạn:

1. **Tiền xử lý & Làm sạch**: Xử lý các giá trị không hợp lệ (GPA > 4, TC_DANGKY = 0) bằng phương pháp dự báo giá trị thiếu thông minh thay vì xóa bỏ.

2. **Kỹ thuật đặc trưng (Feature Engineering)**: 
   * Tạo các đặc trưng về xu hướng (`GPA_trend`, `CPA_velocity`).
   * Tính toán sự chênh lệch năng lực (`DIEM_LECH` giữa điểm SV và điểm chuẩn).
   * Mã hóa dữ liệu phân loại (Target Encoding, One-hot).

3. **Huấn luyện mô hình**: Sử dụng thuật toán XGBoost Regressor kết hợp với GridSearchCV để tối ưu hóa siêu tham số.

4. **Đánh giá độ ổn định**: Thực hiện 5-Fold Cross-Validation để đảm bảo mô hình không bị quá khớp (overfitting).

5. **Giải thích mô hình (XAI)**: Sử dụng SHAP và LIME để minh bạch hóa các quyết định của mô hình.

## 4. Hướng dẫn cài đặt và sử dụng

### Cài đặt môi trường

Yêu cầu cài đặt các thư viện cần thiết:

```bash
pip install -r requirements.txt
```

### Chạy mã nguồn

1. Mở file `main_analysis.ipynb` bằng Jupyter Notebook hoặc Google Colab.
2. Chạy lần lượt các cell theo thứ tự. Kết quả dự báo sẽ tự động được xuất ra thư mục `outputs/`.

## 5. Kết quả chính

### 5.1 Hiệu suất mô hình

**Tập huấn luyện (90,582 mẫu):**
- MAE: 2.32 tín chỉ
- RMSE: 3.32 tín chỉ
- R²: 0.725
- Độ chính xác (±1 TC): 33.99%
- Độ chính xác (±2 TC): 58.06%

**Tập kiểm định (15,144 mẫu):**
- MAE: 2.65 tín chỉ
- RMSE: 3.80 tín chỉ
- R²: 0.694
- Độ chính xác (±1 TC): 32.55%
- Độ chính xác (±2 TC): 53.85%

### 5.2 Đánh giá độ ổn định (5-Fold Cross-Validation)

Mô hình được kiểm tra qua 5 vòng Cross-Validation với kết quả:
- **Test RMSE**: 3.68 ± 0.02 tín chỉ
- **Test MAE**: 2.54 ± 0.01 tín chỉ
- **Test R²**: 0.66 ± 0.01

**Kết luận**: Độ lệch chuẩn RMSE = 0.021 < 0.5 → **MÔ HÌNH ỔN ĐỊNH**

### 5.3 Các đặc trưng quan trọng nhất (theo SHAP values)

Top 10 đặc trưng ảnh hưởng mạnh nhất đến dự báo:

1. **TC_DANGKY_current** (3.09) - Số tín chỉ đăng ký kỳ hiện tại
2. **CPA_last** (0.87) - Điểm trung bình tích lũy kỳ trước
3. **TC_GPA_interaction** (0.73) - Tương tác giữa GPA và TC đăng ký
4. **TC_HT_ratio_mean** (0.54) - Tỷ lệ hoàn thành trung bình
5. **TC_load_ratio** (0.51) - Tỷ lệ tải tín chỉ
6. **NAM_TUYENSINH** (0.41) - Năm tuyển sinh
7. **TC_HT_last** (0.28) - Số TC hoàn thành kỳ trước
8. **consecutive_fails** (0.27) - Số kỳ liên tiếp không đạt
9. **TC_change** (0.27) - Thay đổi số TC so với kỳ trước
10. **num_semesters** (0.19) - Số học kỳ đã học


## 6. Liên hệ

* **Đội thi**: DHC
* **Email**: 
  - ykhanh205.1@gmail.com
  - hoangthihanh2410@gmail.com