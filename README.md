# Xây dựng Mô hình Hỏi đáp trên Đồ thị Tri thức Hướng Thời gian Sử dụng Mô hình Ngôn ngữ Chuyên biệt

**Mã đề tài**: T826_KL_CNTTHUC10  
**Sinh viên**: Nguyễn Trọng Phúc (22127335) · Trần Thái Minh Quân (22127351)  
**Giảng viên hướng dẫn**: TS. Nguyễn Hồng Bửu Long · TS. Lương An Vinh  
**Trường**: Đại học Khoa học Tự nhiên, ĐHQG-HCM

---

## Giới thiệu

Đề tài nghiên cứu bài toán **Temporal Knowledge Graph Question Answering (TKGQA)** cho tiếng Việt, dựa trên pipeline **TimeR4** (EMNLP 2024). Mục tiêu là xây dựng hệ thống hỏi đáp có khả năng trả lời các câu hỏi liên quan đến thời gian trên đồ thị tri thức tiếng Việt.

### Pipeline TimeR4
```
Câu hỏi tiếng Việt
    ↓ [TEN] Temporal Expression Normalization  ← đóng góp của đề tài
    ↓ [Retrieve] Tìm kiếm fact từ TKG
    ↓ [Rewrite] Viết lại câu hỏi với thông tin thời gian
    ↓ [Retrieve] Tìm kiếm lại
    ↓ [Rerank] Xếp hạng fact
    ↓ Câu trả lời
```

---

## Cấu trúc Repo

```
GraduationThesis/
├── README.md
├── TimeR4_TEN_MultiTQ.ipynb          # Notebook chạy pipeline trên MultiTQ-VI
└── TimeR4_TEN_TimeQuestions_VI.ipynb # Notebook chạy pipeline trên TimeQuestions-VI
```

---

## Dataset

| Dataset | Nguồn | Số câu | Ngôn ngữ | Mô tả |
|---------|-------|--------|----------|-------|
| MultiTQ-VI | MultiTQ (ACL 2023) | 499,350 | Tiếng Việt | Dịch từ MultiTQ bằng Google Translate API |
| TimeQuestions-VI | TimeQuestions (CIKM 2021) | 16,181 | Tiếng Việt | Dịch từ TimeQuestions trong repo TimeR4 |

---

## Kết quả Thực nghiệm

### MultiTQ-VI

| Model | Hit@1 | Cải thiện |
|-------|-------|-----------|
| TimeR4 original (tiếng Anh, paper gốc) | 42.1% | — |
| TimeR4 baseline (tiếng Việt, chưa TEN) | 11.80% | — |
| TimeR4 + TEN (tiếng Việt, sau normalize) | 11.90% | +0.10 ↑ |

### TimeQuestions-VI

| Model | Hit@1 | Cải thiện |
|-------|-------|-----------|
| TimeR4 original (tiếng Anh, paper gốc) | 42.1% | — |
| TimeR4 baseline (tiếng Việt, chưa TEN) | 4.93% | — |
| TimeR4 + TEN (tiếng Việt, sau normalize) | 4.93% | 0.00 |

---

## Module TEN (Temporal Expression Normalization)

Module chuẩn hóa biểu thức thời gian trong câu hỏi tiếng Việt về dạng ISO chuẩn, trước khi đưa vào Retriever.

**4 pattern được hỗ trợ:**

| Pattern đầu vào | Đầu ra chuẩn |
|-----------------|--------------|
| `ngày DD tháng MM năm YYYY` | `YYYY-MM-DD` |
| `ngày DD/MM/YYYY` | `YYYY-MM-DD` |
| `tháng MM năm YYYY` | `YYYY-MM` |
| `tháng MM/YYYY` | `YYYY-MM` |

**Ví dụ:**
```
Trước ngày 22 tháng 10 năm 2008, Malaysia...
→ trước 2008-10-22, malaysia...

Ai ký thỏa thuận vào tháng 4 năm 2005?
→ ai ký thỏa thuận vào 2005-04?
```

---

## Cách chạy

### Yêu cầu
- Kaggle account (GPU T4 miễn phí)
- Dataset đã upload lên Kaggle Input

### Setup trên Kaggle
```
1. Session options → ACCELERATOR: GPU T4
2. Session options → INTERNET: ON
3. Session options → PERSISTENCE: Files only
4. Tab Input → Add Input → Upload dataset
5. Runtime → Restart Session
6. Run All
```

### Notebooks

| Notebook | Dataset | Thời gian |
|----------|---------|-----------|
| `TimeR4_TEN_MultiTQ.ipynb` | MultiTQ_vi_test.json | ~1.5-2 giờ |
| `TimeR4_TEN_TimeQuestions_VI.ipynb` | TimeQuestions_vi_test.json | ~1 giờ |

---

## Môi trường

| Thư viện | Version |
|----------|---------|
| torch | 2.3.1 |
| transformers | 4.44.0 |
| sentence-transformers | 3.3.1 |
| huggingface_hub | 0.23.4 |
| peft | 0.11.1 |
| accelerate | 0.34.0 |
| faiss-cpu | latest |

**LLM**: Mistral-7B-Instruct-v0.2 (float16, không quantize)  
**Retriever**: all-MiniLM-L6-v2 (baseline)

---

## Tham khảo

- **TimeR4**: [github.com/qianxinying/TimeR4](https://github.com/qianxinying/TimeR4) — *TimeR4: Time-aware Retrieval-Rewriting-Reranking for Temporal Knowledge Graph Question Answering* (EMNLP 2024)
- **MultiTQ**: *MultiTQ: Multi-granularity Temporal Question Answering* (ACL 2023)
- **TimeQuestions**: *EXAQT: Answering Complex Temporal Questions* (CIKM 2021)
