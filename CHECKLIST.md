# Day 21 Lab 21 Checklist

> Mục tiêu: làm xong lab LoRA/QLoRA đúng rubric, đủ artifact để nộp, đủ số liệu để defend report.

## 0. Chọn hướng làm

- [ ] Chọn notebook đúng GPU
  - [ ] `Lab21_LoRA_Finetuning_T4.ipynb` nếu chỉ có Colab Free T4
  - [ ] Notebook GPU lớn nếu có L4/A100
- [ ] Chốt submission option
  - [ ] `Option B` làm chuẩn chính: ZIP + HuggingFace Hub
  - [ ] Ghi nhận `Option A` / `Option C` chỉ là fallback nếu Hub lỗi
- [ ] Ghi rõ model base sẽ dùng trong report
  - [ ] Cùng 1 model cho cả `r=8`, `r=16`, `r=64`

## 1. Đọc spec trước khi chạy

- [ ] Đọc `README.md` để nắm mục tiêu học phần và path bài học
- [ ] Đọc `Lab21_Rubric_and_Format.md` để nắm deliverable, rubric, và format nộp
- [ ] Mở notebook và rà submission checklist cuối file

## 2. Setup môi trường

- [ ] Bật GPU runtime
- [ ] Verify GPU nhận đúng trong notebook
- [ ] Cài dependencies cần thiết
- [ ] Mount Google Drive nếu muốn lưu checkpoint bền
- [ ] Tạo `OUTPUT_DIR`
- [ ] Kiểm tra dung lượng lưu trữ đủ cho 3 adapter + CSV + report

## 3. Chuẩn bị dataset

- [ ] Chốt dataset: `5CD-AI/Vietnamese-alpaca-gpt4-gg-translated`
- [ ] Dùng 200 samples từ `train` split như notebook spec
- [ ] Đảm bảo data có format Alpaca:
  - [ ] `instruction`
  - [ ] `input`
  - [ ] `output`
- [ ] Có 100-500 examples sạch
- [ ] Dedup data
- [ ] Loại sample có output quá ngắn
- [ ] Filter template/noise nếu có
- [ ] Làm token length analysis
- [ ] Set `max_seq_length = p95`
- [ ] Round `max_seq_length` hợp lý
- [ ] Split 90/10 train/eval với seed 42
- [ ] Verify số mẫu train/eval khớp expected

## 4. Configure baseline LoRA

- [ ] Load base model 4-bit QLoRA
- [ ] Bật gradient checkpointing
- [ ] Set baseline rank:
  - [ ] `r=16`
  - [ ] `lora_alpha=32`
  - [ ] `target_modules=["q_proj", "v_proj"]`
  - [ ] `lora_dropout=0`
- [ ] Verify trainable params xuất hiện đúng
- [ ] Ghi lại config baseline để đưa vào report

## 5. Train baseline `r=16`

- [ ] Chạy SFTTrainer baseline
- [ ] Train đủ số epoch theo notebook/spec
- [ ] Theo dõi training loss
- [ ] Nếu có eval loss, ghi lại
- [ ] Plot loss curve
- [ ] Save adapter `r16/`
- [ ] Save checkpoint trước khi làm bước khác
- [ ] Ghi thời gian train baseline
- [ ] Ghi peak VRAM baseline

## 6. Train rank experiment

- [ ] Reset/cleanup model state trước mỗi lần train mới
- [ ] Train `r=8`
  - [ ] Set `alpha=16`
  - [ ] Save adapter `r8/`
  - [ ] Ghi train time
  - [ ] Ghi peak VRAM
  - [ ] Ghi trainable params
  - [ ] Ghi eval loss
  - [ ] Ghi perplexity
- [ ] Train `r=64`
  - [ ] Set `alpha=128`
  - [ ] Save adapter `r64/`
  - [ ] Ghi train time
  - [ ] Ghi peak VRAM
  - [ ] Ghi trainable params
  - [ ] Ghi eval loss
  - [ ] Ghi perplexity
- [ ] Đảm bảo cả 3 rank train trên cùng dataset, cùng hyperparameters, chỉ đổi rank/alpha

## 7. Evaluation

- [ ] Compute perplexity cho cả 3 ranks
- [ ] Có số cho base model nếu notebook/report yêu cầu
- [ ] Nếu eval OOM, dùng recovery path `safe_evaluate()`
- [ ] Chạy ít nhất 5 test prompts
- [ ] So sánh base vs fine-tuned trên cùng prompt
- [ ] Lưu qualitative examples rõ ràng
- [ ] Ghi case tốt và case xấu, không cherry-pick

## 8. Save artifacts

- [ ] Lưu `rank_experiment_summary.csv`
- [ ] Lưu `qualitative_comparison.csv`
- [ ] Lưu `loss_curve.png`
- [ ] Điền `REPORT.md` từ template
- [ ] Điền `LINKS.md` cho Option B
- [ ] Verify folder `r8/`
- [ ] Verify folder `r16/`
- [ ] Verify folder `r64/`
- [ ] Push adapter lên HuggingFace Hub
- [ ] Ghi link Hub public vào `REPORT.md`

## 9. Viết `REPORT.md`

- [ ] Có header với tên, MSSV, ngày nộp, submission option
- [ ] Section 1: Setup
  - [ ] Base model key
  - [ ] Dataset name + size
  - [ ] `max_seq_length`
  - [ ] GPU + VRAM
  - [ ] Training cost ước tính
  - [ ] HF Hub link nếu có
- [ ] Section 2: Rank Experiment Results
  - [ ] Bảng đủ 4 metric
  - [ ] Có `r=8`, `r=16`, `r=64`, base
- [ ] Section 3: Loss Curve Analysis
  - [ ] Đính kèm hoặc nhắc `loss_curve.png`
  - [ ] Nêu có/không overfitting và lý do
- [ ] Section 4: Qualitative Comparison
  - [ ] Ít nhất 5 examples
  - [ ] Có prompt, base, fine-tuned, nhận xét
- [ ] Section 5: Conclusion về Rank Trade-off
  - [ ] Tối thiểu 100 từ
  - [ ] Nêu rank ROI tốt nhất
  - [ ] Nêu diminishing returns
  - [ ] Nêu recommendation production
- [ ] Section 6: What I Learned
  - [ ] 2-3 bullet points phản tỉnh cá nhân

## 10. Verify rubric

- [ ] Functionality: 3 adapters train + save được
- [ ] Experiment design: có số liệu đủ 4 chiều
- [ ] Evaluation quality: perplexity đúng + 5 examples meaningful
- [ ] Report quality: đủ 6 sections, markdown sạch
- [ ] Bonus HF Hub có bằng chứng public, link verify được

## 11. Gói nộp

- [ ] Clear output không cần thiết nếu muốn ZIP nhẹ
- [ ] ZIP đúng folder output
- [ ] Kiểm tra trong ZIP có đủ file cần nộp: `REPORT.md`, `LINKS.md`, CSV, adapters
- [ ] Push code lên GitHub repo cá nhân nếu yêu cầu
- [ ] Gửi link cho instructor / LMS

## 12. Stretch goals nếu còn thời gian

- [ ] Thử target all layers
- [ ] Thử DoRA
- [ ] Merge + GGUF
- [ ] Thử W&B tracking

## 13. Bonus points plan

- [ ] Bonus +5: đã làm Option B
  - [ ] Push adapter lên HuggingFace Hub
  - [ ] Ghi link public Hub trong `REPORT.md`
  - [ ] Ghi link GitHub repo / artifact verifiable
- [ ] Bonus +10: stretch goals
  - [ ] Target ALL layers thay vì chỉ `q_proj` + `v_proj`
  - [ ] Train thêm adapter with DoRA (`use_dora=True`)
  - [ ] Merge adapter + base
  - [ ] Convert / test GGUF
  - [ ] Track run bằng W&B
  - [ ] Chụp / lưu evidence cho report

## 14. Final bonus sanity check

- [ ] Có Option B hoàn chỉnh
- [ ] Có evidence trong report cho mỗi bonus đã làm
- [ ] Nếu làm stretch goals, nói rõ impact lên time / VRAM / quality
- [ ] Nếu làm nhiều bonus, ưu tiên cái nào verify được rõ nhất

## 15. Quick final sanity check

- [ ] Không thiếu `REPORT.md`
- [ ] Không thiếu `rank_experiment_summary.csv`
- [ ] Không thiếu `qualitative_comparison.csv`
- [ ] Không thiếu 3 adapter folders
- [ ] Report nói rõ model, data, rank trade-off, và kết luận
