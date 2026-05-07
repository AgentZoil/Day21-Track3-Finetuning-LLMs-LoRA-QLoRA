# Lab 21 — Evaluation Report

**Học viên**: Nhữ Gia Bách — 2A202600248  
**Ngày nộp**: 2026-05-07  
**Submission option**: B (ZIP + HuggingFace Hub)

## 1. Setup

- **Base model**: `unsloth/Qwen2.5-3B-bnb-4bit`
- **Dataset**: `5CD-AI/Vietnamese-alpaca-gpt4-gg-translated`, 200 samples (180 train + 20 eval)
- **max_seq_length**: 1024 (p95 rounded up)
- **GPU**: Tesla T4, 15.6 GB VRAM
- **Training cost**: $0.07 (~11.9 phút @ $0.35/hr)
- **HF Hub link**: https://huggingface.co/AgentZoil/lab21/tree/main/lab21_lora_t4

## 2. Rank Experiment Results

| rank | alpha | trainable_params | train_time_min | peak_vram_gb | eval_loss | eval_perplexity | target_modules | use_dora |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 8 | 16 | 1843200 | 3.8960320949554443 | 7.216137728 | 1.5576941967010498 | 4.747860980375087 | q_proj,v_proj | False |
| 16 | 32 | 3686400 | 4.173518033822377 | 6.617750016 | 1.5160834789276123 | 4.554353000077306 | q_proj,v_proj | False |
| 64 | 128 | 14745600 | 3.7958032846450807 | 7.998511616 | 1.4768110513687134 | 4.378959115873907 | q_proj,v_proj | False |
| Base | - | - | - | - | - | - | reference only | - |

### Bonus experiments

| variant | trainable_params | train_time_min | peak_vram_gb | eval_loss | eval_perplexity | target_modules | use_dora |
| --- | --- | --- | --- | --- | --- | --- | --- |
| all layers | 29933568 | 4.612304818630219 | 10.589895168 | 1.4948303699493408 | 4.458580179516097 | q_proj,k_proj,v_proj,o_proj,gate_proj,up_proj,down_proj | False |
| DoRA request, fallback to LoRA | 3686400 | 3.7548898418744403 | 12.8724608 | 1.5160834789276123 | 4.554353000077306 | q_proj,v_proj | False |

- Bonus all-layers improved perplexity a bit versus the q+v baseline, but cost noticeably more VRAM and parameters.
- The DoRA request fell back to plain LoRA in this Unsloth build, so it did not provide a real DoRA comparison here.

## 3. Loss Curve Analysis

![Loss curve](results/loss_curve.png)

- **Quan sát**: Không có overfitting rõ rệt.
- **Lý do**: Train loss giảm khá đều từ khoảng 1.61 xuống 1.39 qua các checkpoint, chỉ có dao động nhỏ ở giữa quá trình train. Eval loss của các cấu hình cuối cùng cũng giảm khi rank tăng, nên xu hướng chung là mô hình còn hưởng lợi từ thêm capacity hơn là bị overfit.

## 4. Qualitative Comparison (5 examples)

| prompt | base | finetuned |
| --- | --- | --- |
| Giải thích khái niệm machine learning cho người mới bắt đầu. | Machine learning là một phân khúc của trí tuệ nhân tạo, nó tập trung vào việc thiết lập các mô hình máy móc để học tập từ dữ liệu và từ đó có thể dự đoán hoặc hành động. Nhìn vào khía cạnh đơn giản, nó có nghĩa là máy tính học tập từ dữ liệ | Machine learning là một bộ môn công nghệ máy tính dựa trên việc học tập và cải thiện các dự đoán dựa trên dữ liệu mà không có sự hướng dẫn trực tiếp từ người dùng. Nó là một phần của AI (trí tuệ nhân tạo) và sử dụng thuật toán và các mô hìn |
| Viết đoạn code Python tính số Fibonacci thứ n. | Để tính số Fibonacci thứ n, bạn có thể sử dụng hàm đệ quy hoặc vòng lặp. Đây là một đoạn mã Python cho phép bạn tính số Fibonacci thứ n:  ```python def fibonacci(n):     if n <= 0:         return "N phải là một số dương"     elif n == 1:    | Để tính số Fibonacci thứ n, bạn có thể viết một đoạn code Python như sau:  ```python def fibonacci(n):     if n < 0:         raise ValueError("Input phải là một số nguyên dương.")     elif n == 0:         return 0     elif n == 1:         r |
| Liệt kê 5 nguyên tắc thiết kế UI/UX. | 1. Thân thiện với người dùng: Mục đích của thiết kế UI/UX là cung cấp trải nghiệm người dùng thân thiện, dễ sử dụng và thân thiện. Điều này có thể được thể hiện thông qua việc sắp xếp bố cục, màu sắc, font chữ, hình ảnh và các yếu tố khác đ | 1. Chuyển đổi: UI/UX thiết kế phải hướng tới việc giúp người dùng thực hiện hành động. 2. Thích ứng: UI/UX thiết kế phải thích ứng với nhiều loại thiết bị và kích thước màn hình. 3. Đơn giản: UI/UX thiết kế phải dễ dàng sử dụng và dễ hiểu.  |
| Tóm tắt sự khác biệt giữa LoRA và QLoRA. | LoRA (Low-Rank Adaptation) và QLoRA (Quantized LoRA) là hai phương pháp cải thiện hiệu năng của mô hình NLU (NLP) bằng cách sử dụng các phép biến đổi thấp độ phức tạp. LoRA là phương pháp cải thiện hiệu năng của mô hình bằng cách thay đổi c | LoRA (Layer-wise Adaptive Regularization Optimization) và QLoRA (Quantized LoRA) là hai phương pháp regularization được phát triển để cải thiện hiệu quả và độ ổn định của các mạng neural network trong các mô hình có số lượng các layer lớn.  |
| Phân biệt prompt engineering, RAG, và fine-tuning. | Prompt engineering, RAG (retrieval augmented generation), và fine-tuning là ba cách khác nhau để cải thiện hiệu suất của mô hình máy học. Prompt engineering là một kỹ thuật để cải thiện hiệu suất của mô hình bằng cách cung cấp cho nó một câ | Prompt engineering, RAG và fine-tuning là ba kỹ thuật khác nhau được sử dụng trong lĩnh vực AI và tự động hóa. Prompt engineering là một kỹ thuật tập trung vào việc xây dựng câu lệnh (prompt) để giúp hệ thống AI giải quyết các vấn đề và thự |

- **Nhận xét chung**: Cả 5 ví dụ đều cho thấy mô hình fine-tuned trả lời có cấu trúc hơn, bám format tốt hơn và ít lỗi ngôn ngữ hơn. Điểm yếu là một vài câu trả lời vẫn chưa thật sự sâu, nên qualitative gain ở đây rõ nhất nằm ở độ rõ ràng và tính hữu dụng của câu trả lời chứ không phải ở kiến thức mới.

> Lưu ý: base-model perplexity riêng không được log thành một số độc lập trong run này; phần quantitative bên dưới phản ánh các adapter đã train xong, còn base model được dùng làm mốc so sánh qualitative trong bảng trên.

## 5. Conclusion về Rank Trade-off

Rank cho ROI tốt nhất trên bộ dữ liệu này là `r=16`. So với `r=8`, nó giảm eval loss từ 1.5577 xuống 1.5161 và perplexity từ 4.7479 xuống 4.5544, trong khi số tham số trainable chỉ tăng gấp đôi và vẫn giữ VRAM ở mức rất dễ chịu cho T4. `r=64` cho chất lượng tốt nhất về mặt tuyệt đối, với eval loss 1.4768 và perplexity 4.3790, nhưng lợi ích thêm so với `r=16` đã nhỏ hơn nhiều so với phần chi phí tăng thêm về tham số và VRAM, nên đây là điểm xuất hiện diminishing returns khá rõ. Nếu phải deploy production cho bài toán này, tôi chọn `r=16` vì nó cân bằng tốt giữa chất lượng, kích thước adapter và khả năng vận hành. Bonus experiments cũng củng cố nhận định này: target all layers cải thiện metric thêm chút ít, nhưng đổi lại chi phí tăng mạnh; DoRA không đem lại cải thiện rõ ràng trong run này.

## 6. What I Learned

- LoRA rank không chỉ ảnh hưởng chất lượng mà còn kéo theo memory footprint và khả năng train trên GPU nhỏ, nên chọn rank phải gắn với budget thật.
- Qualitative evaluation rất quan trọng: cùng một perplexity gần nhau nhưng cách trả lời khác nhau về độ rõ ràng, cấu trúc và độ an toàn.
- Tôi hiểu rõ hơn về trade-off giữa target modules hẹp (`q_proj,v_proj`) và target all layers, cũng như vì sao bonus kỹ thuật chưa chắc đáng deploy nếu chi phí tăng mạnh.
