# Lab 21 — Evaluation Report

**Học viên**: Nhữ Gia Bách — 2A202600248  
**Ngày nộp**: <YYYY-MM-DD>  
**Submission option**: B (ZIP + HuggingFace Hub)

## 1. Setup

- **Base model**: `unsloth/Qwen2.5-3B-bnb-4bit`
- **Dataset**: `5CD-AI/Vietnamese-alpaca-gpt4-gg-translated`, 200 samples (180 train + 20 eval)
- **max_seq_length**: 1024 (p95 rounded up)
- **GPU**: Tesla T4, 15.6 GB VRAM
- **Training cost**: $0.07 (~11.9 phút @ $0.35/hr)
- **HF Hub link**: https://huggingface.co/AgentZoil/qwen2.5-3b-vi-lab21-r16

## 2. Rank Experiment Results

| rank | alpha | trainable_params | train_time_min | peak_vram_gb | eval_loss | eval_perplexity | target_modules | use_dora |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 8 | 16 | 1843200 | 3.8960320949554443 | 7.216137728 | 1.5576941967010498 | 4.747860980375087 | q_proj,v_proj | False |
| 16 | 32 | 3686400 | 4.173518033822377 | 6.617750016 | 1.5160834789276123 | 4.554353000077306 | nan | nan |
| 64 | 128 | 14745600 | 3.7958032846450807 | 7.998511616 | 1.4768110513687134 | 4.378959115873907 | q_proj,v_proj | False |

## 3. Loss Curve Analysis

![Loss curve](loss_curve.png)

- **Quan sát**: <có / không có overfitting?>
- **Lý do**: <giải thích ngắn gọn dựa trên train loss / eval loss>

## 4. Qualitative Comparison (5 examples)

| prompt | base | finetuned |
| --- | --- | --- |
| Giải thích khái niệm machine learning cho người mới bắt đầu. | Machine learning là một phân khúc của trí tuệ nhân tạo, nó tập trung vào việc thiết lập các mô hình máy móc để học tập từ dữ liệu và từ đó có thể dự đoán hoặc hành động. Nhìn vào khía cạnh đơn giản, nó có nghĩa là máy tính học tập từ dữ liệ | Machine learning là một bộ môn công nghệ máy tính dựa trên việc học tập và cải thiện các dự đoán dựa trên dữ liệu mà không có sự hướng dẫn trực tiếp từ người dùng. Nó là một phần của AI (trí tuệ nhân tạo) và sử dụng thuật toán và các mô hìn |
| Viết đoạn code Python tính số Fibonacci thứ n. | Để tính số Fibonacci thứ n, bạn có thể sử dụng hàm đệ quy hoặc vòng lặp. Đây là một đoạn mã Python cho phép bạn tính số Fibonacci thứ n:  ```python def fibonacci(n):     if n <= 0:         return "N phải là một số dương"     elif n == 1:    | Để tính số Fibonacci thứ n, bạn có thể viết một đoạn code Python như sau:  ```python def fibonacci(n):     if n < 0:         raise ValueError("Input phải là một số nguyên dương.")     elif n == 0:         return 0     elif n == 1:         r |
| Liệt kê 5 nguyên tắc thiết kế UI/UX. | 1. Thân thiện với người dùng: Mục đích của thiết kế UI/UX là cung cấp trải nghiệm người dùng thân thiện, dễ sử dụng và thân thiện. Điều này có thể được thể hiện thông qua việc sắp xếp bố cục, màu sắc, font chữ, hình ảnh và các yếu tố khác đ | 1. Chuyển đổi: UI/UX thiết kế phải hướng tới việc giúp người dùng thực hiện hành động. 2. Thích ứng: UI/UX thiết kế phải thích ứng với nhiều loại thiết bị và kích thước màn hình. 3. Đơn giản: UI/UX thiết kế phải dễ dàng sử dụng và dễ hiểu.  |
| Tóm tắt sự khác biệt giữa LoRA và QLoRA. | LoRA (Low-Rank Adaptation) và QLoRA (Quantized LoRA) là hai phương pháp cải thiện hiệu năng của mô hình NLU (NLP) bằng cách sử dụng các phép biến đổi thấp độ phức tạp. LoRA là phương pháp cải thiện hiệu năng của mô hình bằng cách thay đổi c | LoRA (Layer-wise Adaptive Regularization Optimization) và QLoRA (Quantized LoRA) là hai phương pháp regularization được phát triển để cải thiện hiệu quả và độ ổn định của các mạng neural network trong các mô hình có số lượng các layer lớn.  |
| Phân biệt prompt engineering, RAG, và fine-tuning. | Prompt engineering, RAG (retrieval augmented generation), và fine-tuning là ba cách khác nhau để cải thiện hiệu suất của mô hình máy học. Prompt engineering là một kỹ thuật để cải thiện hiệu suất của mô hình bằng cách cung cấp cho nó một câ | Prompt engineering, RAG và fine-tuning là ba kỹ thuật khác nhau được sử dụng trong lĩnh vực AI và tự động hóa. Prompt engineering là một kỹ thuật tập trung vào việc xây dựng câu lệnh (prompt) để giúp hệ thống AI giải quyết các vấn đề và thự |

## 5. Conclusion về Rank Trade-off

<Viết tối thiểu 100 từ. Trả lời rõ: rank nào cho ROI tốt nhất, diminishing returns xuất hiện ở đâu, và nếu deploy production thì chọn rank nào.>

## 6. What I Learned

- <Bullet 1: insight cá nhân>
- <Bullet 2: insight cá nhân>
- <Bullet 3: optional>
