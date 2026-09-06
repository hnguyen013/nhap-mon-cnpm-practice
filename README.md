# TEAM 1 — PHASE 1 RESEARCH PLAYBOOK (v1)
## Viet12-STEM-Doc Dataset & Baseline Evaluation

> **Đề tài:** Nghiên cứu mô hình hiểu tài liệu Đa phương thức và Trích xuất tri thức từ Sách giáo khoa Tiếng Việt
> **Team 1:** Xây dựng Dataset & Đánh giá các Giới hạn (Người Xây Nền Móng & Bắt Bệnh)
> **Phạm vi tài liệu này:** PHASE 1A — 14 ngày đầu (Dataset Pilot & Baseline Validation)
> **Version:** v1.0.0
---

## ⚠️ QUY ƯỚC ĐỌC TÀI LIỆU (đọc trước khi làm bất cứ điều gì)

Toàn bộ tài liệu này phân biệt **3 loại thông tin** bằng nhãn đặt ở đầu câu/đoạn:

| Nhãn | Ý nghĩa | Ai được phép thay đổi |
|------|---------|----------------------|
| `[OFFICIAL]` | Yêu cầu chính thức từ tài liệu hướng dẫn của giảng viên (PDF gốc). **Không được tự ý đổi.** | Chỉ giảng viên |
| `[RECO]` | Khuyến nghị nghiên cứu do Playbook này đề xuất dựa trên best practices của Document AI / ML / CV. Là *đề xuất*, không phải mệnh lệnh. | Nhóm quyết định |
| `[IMPL]` | Chi tiết kỹ thuật triển khai (lệnh, cấu trúc thư mục, tham số). Có thể thay đổi theo phiên bản công cụ. | Nhóm điều chỉnh |

**Nguyên tắc vàng:** Nếu tài liệu gốc chưa quy định một điểm nào đó, Playbook sẽ ghi rõ:
> 🔶 **OPEN DECISION** — Đây là điểm chưa được tài liệu nguồn quy định và cần nhóm nghiên cứu quyết định (xem [Phụ lục Open Research Questions](#open-research-questions--decisions-required)).

Playbook này **không** khẳng định bất kỳ giả thuyết nghiên cứu nào là đúng. Mọi khẳng định về hiệu năng mô hình đều phải chờ **thực nghiệm** trong 14 ngày này chứng minh.

---

## MỤC LỤC (TABLE OF CONTENTS)

- [Executive Summary](#executive-summary)
- [PART 0 — How To Use This Handbook](#part-0--how-to-use-this-handbook)
- [PART 1 — Hiểu đề tài trước khi làm](#part-1--hiểu-đề-tài-trước-khi-làm)
- [PART 2 — Research Question và Research Logic](#part-2--research-question-và-research-logic)
- [PART 3 — Dataset Design](#part-3--dataset-design)
- [PART 4 — Data Source Selection](#part-4--data-source-selection)
- [PART 5 — PDF to Image Pipeline](#part-5--pdf-to-image-pipeline)
- [PART 6 — Noise & Data Augmentation](#part-6--noise--data-augmentation)
- [PART 7 — Annotation Taxonomy](#part-7--annotation-taxonomy)
- [PART 8 — Annotation Guideline](#part-8--annotation-guideline)
- [PART 9 — Golden Sample](#part-9--golden-sample)
- [PART 10 — Label Studio](#part-10--label-studio)
- [PART 11 — Mathematical Ground Truth](#part-11--mathematical-ground-truth)
- [PART 12 — Quality Control for Dataset](#part-12--quality-control-for-dataset)
- [PART 13 — Baseline Model Concepts](#part-13--baseline-model-concepts)
- [PART 14 — RT-DocLayout Pipeline](#part-14--rt-doclayout-pipeline)
- [PART 15 — UniMERNet Pipeline](#part-15--unimernet-pipeline)
- [PART 16 — Evaluation](#part-16--evaluation)
- [PART 17 — Experiment Design](#part-17--experiment-design)
- [PART 18 — Error Analysis](#part-18--error-analysis)
- [PART 19 — Research Notebook](#part-19--research-notebook)
- [PART 20 — Git / Repository Organization](#part-20--git--repository-organization)
- [PART 21 — 14-Day Execution Plan](#part-21--14-day-execution-plan)
- [PART 22 — Learning Path for Nguyên and Đức](#part-22--learning-path-for-nguyên-and-đức)
- [PART 23 — Daily Research Meeting](#part-23--daily-research-meeting)
- [PART 24 — Definition of Done](#part-24--definition-of-done)
- [PART 25 — Troubleshooting](#part-25--troubleshooting)
- [PART 26 — Common Research Mistakes](#part-26--common-research-mistakes)
- [PART 27 — Final Output After 14 Days](#part-27--final-output-after-14-days)
- [PART 28 — Meeting With Supervisor](#part-28--meeting-with-supervisor)
- [PART 29 — Appendices](#part-29--appendices)
- [MASTER CHECKLIST](#master-checklist--phase-1a-14-ngày)
- [QUESTIONS FOR SUPERVISOR](#questions-for-supervisor)
- [OPEN RESEARCH QUESTIONS / DECISIONS REQUIRED](#open-research-questions--decisions-required)

---

## EXECUTIVE SUMMARY

**Bối cảnh.** Team 1 chịu trách nhiệm cho *nền móng* của toàn bộ đề tài: xây dựng bộ dữ liệu **Viet12-STEM-Doc** và **đánh giá** xem các mô hình Document AI phương Tây hoạt động tốt/kém đến đâu trên Sách giáo khoa (SGK) STEM tiếng Việt. `[OFFICIAL]` Đầu ra của Team 1 (bộ dữ liệu đã gán nhãn + báo cáo lỗi) chính là **đầu vào** của Team 2 (fine-tuning) và gián tiếp Team 3 (LLM). Nếu nền móng sai, mọi thứ phía sau sai theo.

**Vấn đề của 14 ngày đầu.** `[RECO]` Sai lầm phổ biến nhất của nhóm nghiên cứu mới là *lao vào gán nhãn 150–200 trang ngay*, rồi phát hiện taxonomy chưa thống nhất, augmentation không tái lập được, hoặc metric không chạy — và phải làm lại từ đầu. Vì vậy 14 ngày đầu **không** đặt mục tiêu số lượng trang. Chúng ta xây một **lát cắt dọc hoàn chỉnh** của pipeline:

```
Source SGK → PDF/Image → Dataset → Annotation → Augmentation
           → Baseline Inference → Evaluation → Error Analysis
```

trên một **pilot nhỏ ~30–50 trang** `[RECO]`. Khi lát cắt này chạy end-to-end và được kiểm chứng, việc mở rộng lên 150–200 trang `[OFFICIAL]` chỉ còn là *nhân bản một quy trình đã biết chắc là đúng*.

**Mục tiêu đo được sau 14 ngày (Definition of Success của Phase 1A):**

1. Một pipeline PDF→ảnh **tái lập được** (cùng input → cùng output byte-for-byte).
2. **Annotation Guideline v1** đã được kiểm chứng bằng Golden Sample và đo độ đồng thuận (agreement) giữa 3 người.
3. **~30–50 trang** đã gán nhãn layout (COCO) + **Mathematical Ground Truth** (LaTeX) cho các công thức Display-Math.
4. Một **pipeline augmentation reproducible** (có seed, ánh xạ clean→augmented rõ ràng).
5. **Baseline inference** RT-DocLayout + UniMERNet chạy được trên ít nhất Clean + 1 điều kiện nhiễu.
6. **Prototype evaluation**: tính được IoU/mAP (layout) và điểm CDM (math) trên pilot.
7. **Error Analysis v0**: bảng phân loại lỗi + ví dụ đại diện.
8. **Experiment Log** đầy đủ để tái lập.
9. Danh sách **câu hỏi cho giảng viên** trước khi scale dataset.



---

## PART 0 — HOW TO USE THIS HANDBOOK

### 0.1. Tài liệu này dùng để làm gì?

Đây là **Research Handbook nội bộ** — không phải blog, không phải slide, không phải checklist ngắn.

Mỗi bước quan trọng đều được viết theo cùng một khung 7 câu hỏi (gọi là **khung 7Q**):

> **WHAT** (làm gì) → **WHY** (tại sao cần) → **HOW** (input → thao tác → output) → **HOW TO VERIFY** (làm sao biết đúng) → **WHAT CAN GO WRONG** (lỗi thường gặp) → **WHAT DOES IT MEAN** (kết quả nói lên điều gì) → **WHAT NEXT** (dùng ở đâu tiếp theo).

### 0.2. Đọc theo thứ tự nào?

| Thành viên | Đọc bắt buộc trước | Rồi đến |
|--------|-------------------|---------|
| **Anh Đức** | Part 0 → Part 1 → Part 2 → Part 7 (taxonomy) → Part 10 (Label Studio) | Part 22 (learning path của bạn) |
| **Hạnh Nguyên** | Part 0 → Part 1 → Part 2 → Part 3 → Part 7 → Part 8 → Part 9 → Part 11 → Part 16 | Part 18, Part 22 |
| **Đăng Khoa** | Toàn bộ, ưu tiên Part 3, 5, 6, 12, 13–16, 17, 20 | Toàn bộ phụ lục |

**Ngày 1**, cả 3 người đọc chung Part 0, 1, 2 và thống nhất cách hiểu đề tài *trước khi* chạm vào dữ liệu.

### 0.3. Khi gặp vấn đề tìm ở đâu?

1. Lỗi kỹ thuật cụ thể (Label Studio không mở, model không chạy, LaTeX render sai...) → **Part 25 (Troubleshooting)**.
2. Không biết một task thế nào là "xong" → **Part 24 (Definition of Done)**.
3. Bất đồng khi gán nhãn → **Part 8 (Disagreement protocol)**.
4. Không rõ một quy tắc taxonomy → **Part 7**; nếu Part 7 cũng chưa có → ghi vào **Disagreement log (Phụ lục D)** và các thành viên sẽ cùng quyết định.
5. Nghi ngờ mình đang mắc lỗi nghiên cứu → **Part 26 (Common Research Mistakes)**.

### 0.4. Quy tắc cập nhật tài liệu

`[RECO]` Playbook là **tài liệu sống**. Quy tắc:

- Mỗi khi nhóm ra một *quyết định nghiên cứu* (VD: "Caption gộp vào Text"), phải ghi vào **Research Decision Record — Phụ lục I** và cập nhật phần liên quan trong Playbook, kèm ngày và người quyết định.
- Không sửa nội dung `[OFFICIAL]`. Nếu thấy `[OFFICIAL]` có vấn đề, ghi vào **Questions for Supervisor**, đừng tự đổi.
- Tăng version ở đầu tài liệu mỗi lần có thay đổi lớn (v1.0 → v1.1...).
- Mọi thay đổi phải commit vào Git kèm message rõ ràng (Part 20).

### 0.5. Cách ghi lại quyết định nghiên cứu (Decision Log mini)

Mỗi quyết định ghi 1 dòng theo mẫu:

```
[YYYY-MM-DD] DECISION #007 | Chủ đề: <...> | Chọn: <phương án> |
Lý do: <...> | Người quyết định: <Khoa/nhóm/thầy> | Ảnh hưởng: <phần nào của pipeline>
```

**WHY phải ghi:** 3 tuần sau không ai nhớ vì sao "Inline-Math trong bảng lại gán là Table". Không có decision log = tranh cãi lại từ đầu = mất tính nhất quán của dataset = model học nhiễu.

### 0.6. Navigation map (bản đồ toàn tài liệu)

```
                         ┌── PART 1: Khái niệm nền  ─┐
   HIỂU (Day 1–3)  ──────┤   PART 2: Research logic  │
                         └── PART 13: Model concepts ┘
                                    │
   THIẾT KẾ  ───── PART 3 Dataset ─ PART 4 Source ─ PART 7 Taxonomy
                                    │
   XÂY DATA (Day 3–9) ─ PART 5 PDF→Img ─ PART 6 Augment ─ PART 8 Guideline
                        ─ PART 9 Golden ─ PART 10 LabelStudio ─ PART 11 Math GT
                                    │
   KIỂM SOÁT  ───── PART 12 QA ─ PART 24 DoD
                                    │
   CHẠY BASELINE (Day 9–12) ─ PART 14 RT-DocLayout ─ PART 15 UniMERNet
                                    │
   ĐÁNH GIÁ (Day 11–14) ─ PART 16 Metrics ─ PART 17 Experiments ─ PART 18 Error Analysis
                                    │
   GHI CHÉP & BÀN GIAO ─ PART 19 Notebook ─ PART 20 Git ─ PART 27 Outputs ─ PART 28 Supervisor
                                    │
   VẬN HÀNH NHÓM (xuyên suốt) ─ PART 21 Kế hoạch 14 ngày ─ PART 22 Learning ─ PART 23 Họp ─ PART 25 Troubleshoot ─ PART 26 Mistakes
```

---

## PART 1 — HIỂU ĐỀ TÀI TRƯỚC KHI LÀM

> Mục tiêu Part 1: cả 3 người nói cùng một ngôn ngữ. Mỗi khái niệm trả lời 4 câu: **Nó là gì? Tại sao ta cần? Trong đề tài này dùng ở đâu? Người mới hay hiểu sai điều gì?**

### 1.1. Document Understanding (Hiểu tài liệu) là gì?

**Khái niệm.** Document Understanding (DU) là lĩnh vực cho máy tính *đọc và hiểu tài liệu như con người*: không chỉ lấy ký tự (OCR), mà còn hiểu **cấu trúc** (đâu là tiêu đề, đoạn văn, bảng, hình, công thức), **quan hệ** (caption thuộc hình nào), và **thứ tự đọc** (reading order).

**Tại sao cần.** OCR thuần chỉ trả về một chuỗi ký tự phẳng. Một trang SGK có 2 cột, một bảng, một đồ thị và ba công thức — nếu chỉ OCR, ta được một "đống chữ" mất hết cấu trúc, vô dụng cho việc số hóa học liệu.

**Dùng ở đâu trong đề tài.** Toàn bộ đề tài là một hệ DU: Team 1 *đo* năng lực DU của mô hình có sẵn, Team 2 *cải thiện* nó, Team 3 *dùng* đầu ra có cấu trúc để sinh câu hỏi.

**Vấn đề dễ sai lầm:** nghĩ "DU = OCR". Sai. OCR là *một phần con* của DU. DU còn gồm layout analysis, table structure, formula recognition, reading order...

### 1.2. Multimodal Document Understanding là gì?

**Khái niệm.** "Đa phương thức" (multimodal) = kết hợp nhiều loại tín hiệu: **văn bản** (text/ngữ nghĩa), **thị giác** (vị trí, hình dạng, đường kẻ bảng, nét đồ thị), và đôi khi **bố cục** (layout như một tín hiệu riêng). Mô hình multimodal nhìn *ảnh trang* chứ không chỉ đọc *chuỗi text*.

**Tại sao cần.** Một công thức toán, một đồ thị hàm số, một bảng biến thiên — không thể hiểu chỉ bằng text. Phải *nhìn*. SGK STEM đầy những đối tượng chỉ có nghĩa khi nhìn bằng thị giác.

**Dùng ở đâu.** RT-DocLayout (nhìn ảnh → tìm vùng bố cục) và UniMERNet (nhìn ảnh công thức → sinh LaTeX) đều là mô hình thị giác. Team 3 dùng LLM đa phương thức để xử lý cả text lẫn LaTeX.

**Hiểu sai thường gặp:** nghĩ multimodal nghĩa là "có cả ảnh và âm thanh". Trong đề tài này, các "modality" chủ yếu là **văn bản + thị giác + cấu trúc bố cục** trên trang tài liệu.

### 1.3. Tại sao SGK khác tài liệu text thông thường?

`[OFFICIAL]` Đề tài nhấn mạnh SGK STEM có **cấu trúc trình bày đan xen**: inline-math nằm giữa dòng chữ, bảng biểu khép kín, đồ thị. Cụ thể SGK khác ở:

- **Mật độ công thức cao**, cả *inline* (trong dòng, VD: "vận tốc $v = s/t$ cho ta...") lẫn *display* (tách dòng, canh giữa).
- **Bố cục phức tạp**: hộp ghi nhớ, ví dụ có viền, bài tập đánh số, chú thích hình, sơ đồ.
- **Trộn lẫn nhiều modality trong một vùng nhỏ**: một câu văn có thể chứa công thức hóa học lồng trong đó.
- **Tính giáo dục**: có màu, icon, khung trang trí — nhiễu thị giác mà tài liệu khoa học/khế ước không có.

**Hiểu sai:** nghĩ "sách nào chả giống nhau". Một bài báo khoa học layout đơn giản, đồng nhất; SGK lớp 12 layout dày đặc và bất quy tắc hơn nhiều.

### 1.4. Tại sao SGK **tiếng Việt** có thách thức riêng?

`[OFFICIAL]` Tài liệu nêu 2 nguồn khó khăn giả định:
1. **Hệ thống dấu thanh điệu phức tạp** (dấu sắc/huyền/hỏi/ngã/nặng chồng lên nguyên âm, dễ bị nhầm khi ảnh nhiễu — ví dụ "?" vs "~" trên chữ).
2. **Cấu trúc đan xen** (inline-math, bảng khép kín, đồ thị) như 1.3.

> ⚠️ **Cảnh báo research logic (rất quan trọng):** Hai điều trên hiện là **giả thuyết (hypothesis)**, KHÔNG phải kết luận. Nhiệm vụ 14 ngày là *thiết kế cách kiểm chứng*, không phải *khẳng định trước*. Xem Part 2.

**Dùng ở đâu.** Chính hai giả thuyết này định hình *cách chọn dữ liệu* (Part 4: phải có trang giàu dấu thanh + trang đan xen công thức) và *cách phân tích lỗi* (Part 18: soi riêng lỗi dấu thanh, lỗi công thức lồng trong text).

**Hiểu sai:** coi "mô hình phương Tây chắc chắn kém trên tiếng Việt" là điều hiển nhiên. Có thể mô hình vẫn tốt ở layout nhưng kém ở math, hoặc ngược lại. Chỉ số liệu mới nói được.

### 1.5. Layout Understanding (Hiểu bố cục) là gì?

Bài toán *phát hiện và phân loại vùng* trên trang: mỗi vùng là một **bounding box** (hộp chữ nhật) kèm **nhãn lớp** (Title / Text / Table / Figure / Inline-Math / Display-Math). Về mặt kỹ thuật đây là **object detection** áp lên trang tài liệu.

**Tại sao cần.** Để "bóc" trang thành các khối có nghĩa trước khi xử lý sâu (OCR text ở khối Text, nhận dạng công thức ở khối Math, phân tích cấu trúc ở khối Table).

**Dùng ở đâu.** Đây là nhiệm vụ của RT-DocLayout. Ground Truth layout của ta (từ Label Studio → COCO) dùng để *chấm điểm* dự đoán của model bằng IoU/mAP.

**Hiểu sai:** tưởng layout = "chia trang thành cột". Thực ra layout ở đây là *phát hiện từng đối tượng ngữ nghĩa*, mịn hơn nhiều.

### 1.6. Mathematical Expression Recognition (MER) là gì?

**Là gì.** Chuyển **ảnh một công thức** → **mã LaTeX** biểu diễn đúng công thức đó. VD: ảnh $E=mc^2$ → chuỗi `E = m c ^ { 2 }`.

**Tại sao cần.** LaTeX là dạng *có cấu trúc, máy hiểu được, render lại được*. Có LaTeX thì Team 3 mới đưa công thức vào LLM để sinh câu hỏi; có LaTeX chuẩn thì mới *chấm* được model nhận dạng công thức.

**Dùng ở đâu.** Nhiệm vụ của UniMERNet. Ground Truth math của ta (LaTeX chuẩn từ Mathpix/gõ tay) dùng để chấm bằng CDM.

**Hiểu sai (cực kỳ quan trọng):** nghĩ "MER = OCR cho công thức". Không. OCR trả chuỗi ký tự tuyến tính; MER phải hiểu **cấu trúc 2 chiều** (tử số/mẫu số, mũ/chỉ số, căn, ma trận). "x2" có thể là `x_2` (chỉ số) hay `x^2` (số mũ) — OCR không phân biệt, MER phải phân biệt. Xem Part 11.

### 1.7. Ground Truth (GT) là gì?

**Là gì.** "Sự thật nền" — đáp án *đúng do con người xác lập*, dùng làm chuẩn để so với dự đoán của model. Với layout: các box + nhãn ta vẽ tay. Với math: mã LaTeX ta xác nhận render khớp ảnh gốc.

**Tại sao cần.** Không có GT thì *không đo được* model đúng hay sai. GT là "thước đo".

**Dùng ở đâu.** Mọi metric (IoU, mAP, CDM) đều là *so sánh prediction với GT*.

**Hiểu sai:** nghĩ GT "gần đúng là được". Sai. GT bẩn → mọi kết luận sai theo. GT là tài sản quý nhất của Team 1. `[OFFICIAL]` Tài liệu yêu cầu LaTeX "chuẩn 100%", render khớp hoàn toàn SGK.

### 1.8. Dataset là gì (trong ngữ cảnh này)?

**Là gì.** Một tập có tổ chức gồm: ảnh trang (nhiều điều kiện: clean/scanned/captured/augmented) + annotation layout (COCO) + Mathematical GT (LaTeX) + metadata (nguồn, phiên bản, cách sinh). Tên riêng của ta: **Viet12-STEM-Doc** `[OFFICIAL]`.

**Tại sao cần.** Là *nguyên liệu* cho mọi thứ: đo baseline (Team 1), fine-tune (Team 2). "Đầu ra Team 1 = đầu vào Team 2" `[OFFICIAL]`.

**Dùng ở đâu.** Toàn bộ đề tài.

**Hiểu sai:** nghĩ dataset chỉ là "thư mục ảnh". Dataset = ảnh **+** nhãn **+** metadata **+** khả năng tái lập. Thiếu metadata/version thì không phải dataset nghiên cứu, chỉ là đống file.

### 1.9. Baseline model là gì?

**Là gì.** Mô hình *có sẵn, không huấn luyện lại*, dùng làm **mốc so sánh**. Ở đây: RT-DocLayout và UniMERNet chạy bằng **pretrained weights** `[OFFICIAL]` ("không cần train, chỉ gọi pre-trained weights").

**Tại sao cần.** Để trả lời câu hỏi gốc của đề tài: *"Mô hình phương Tây/quốc tế hoạt động ra sao khi chưa được điều chỉnh cho tiếng Việt?"* Baseline là điểm xuất phát; Team 2 sẽ chứng minh fine-tune vượt qua nó.

**Dùng ở đâu.** Phase 1 = đo baseline. Điểm baseline này là con số Team 2 phải đánh bại (Delta Evaluation).

**Hiểu sai:** nghĩ baseline "phải yếu". Không — baseline chỉ là *mốc*. Nó có thể mạnh hoặc yếu; việc của ta là *đo trung thực*.

### 1.10. Evaluation là gì?

**Là gì.** Quy trình *định lượng* chất lượng dự đoán so với GT bằng các **metric**: layout dùng **IoU/mAP**, math dùng **CDM** `[OFFICIAL]` (tuyệt đối không BLEU/Edit Distance cho math).

**Tại sao cần.** Biến cảm giác "hình như model sai nhiều" thành con số có thể so sánh, tái lập, đưa vào báo cáo/bài báo.

**Dùng ở đâu.** Giai đoạn 4 của Team 1. Là bằng chứng (evidence) cho mọi claim.

**Hiểu sai:** nghĩ "một con số metric là đủ". Metric nói *bao nhiêu*, không nói *tại sao*. Phải đi kèm Error Analysis (Part 18).

### 1.11. Error Analysis (Phân tích lỗi) là gì?

**Là gì.** Đi *sâu vào các trường hợp sai*: phân loại lỗi (missed detection, nhầm lớp, sai dấu thanh, sai cấu trúc công thức...), đo tần suất, và trích *ví dụ đại diện*.

**Tại sao cần.** `[OFFICIAL]` Tài liệu yêu cầu chỉ ra *định lượng* (giảm bao nhiêu %) **và** *định tính* (ví dụ AI sai dấu ngã/hỏi, "bó tay" trước phương trình hóa học lồng trong văn bản). Đây mới là phần có giá trị nghiên cứu, phần làm nên bài báo.

**Dùng ở đâu.** Đầu ra (B) "Báo cáo lỗi" của Team 1; định hướng Team 2 nên fine-tune cái gì.

**Hiểu sai:** coi error analysis là "kể vài ví dụ cho vui". Không — nó phải có taxonomy lỗi, thống kê, và ví dụ *được chọn có chủ đích*.

### 1.12. Vì sao Phase 1 phải làm trước Phase 2?

`[OFFICIAL]` Cơ chế đề tài: "đầu ra nhóm này là đầu vào nhóm kia". Team 2 fine-tune **cần dữ liệu đã gán nhãn của Team 1** làm nguyên liệu huấn luyện. Không có dataset sạch, đúng, nhất quán → Team 2 không có gì để dạy model.

### 1.13. Vì sao Phase 2 không thể bắt đầu nghiêm túc nếu dataset Phase 1 chưa ổn?

Fine-tuning học *chính xác những gì có trong nhãn*. Nếu nhãn mâu thuẫn (cùng một loại vùng lúc gán Text lúc gán Inline-Math), model học nhiễu → điểm sau fine-tune có thể *tệ hơn* baseline, và ta không biết vì model hay vì data. **Garbage in, garbage out.** Đó là lý do 14 ngày này ưu tiên *chất lượng và tính nhất quán* hơn số lượng.

### 1.14. Chuỗi quan hệ cốt lõi

```
Dataset  →  Model  →  Evaluation  →  Evidence  →  Research Conclusion
(GT đúng)  (baseline) (IoU/mAP/CDM)  (số + ví dụ)  (chỉ rút ra SAU thực nghiệm)
```

Đọc chuỗi này từ trái sang phải: **kết luận nghiên cứu chỉ hợp lệ nếu mọi mắt xích bên trái vững.** GT sai → evidence sai → conclusion sai. Đây là "Quan hệ Nhân — Quả về Dữ liệu (Data Lineage)" mà sơ đồ đề tài nhấn mạnh.

---

## PART 2 — RESEARCH QUESTION VÀ RESEARCH LOGIC

> Part này biến định hướng trong PDF gốc thành **logic nghiên cứu tường minh**, và cài "hàng rào" chống kết luận vội.

### 2.1. Chuỗi logic nghiên cứu (đọc kỹ)

```
Observation → Problem → Hypothesis → Dataset → Baseline → Metric
            → Experiment → Error Analysis → Conclusion
```

Áp vào đề tài của ta:

| Mắt xích | Nội dung cụ thể của Team 1 |
|----------|---------------------------|
| **Observation** | Các mô hình Document AI mạnh chủ yếu huấn luyện trên tài liệu tiếng Anh/Trung, layout học thuật. |
| **Problem** | Chưa rõ chúng hoạt động ra sao trên SGK STEM **tiếng Việt** (dấu thanh + bố cục đan xen). |
| **Hypothesis** `[OFFICIAL]` | *Giả thuyết:* hiệu năng **suy giảm** trên SGK tiếng Việt do (H1) dấu thanh điệu, (H2) cấu trúc đan xen. |
| **Dataset** | Viet12-STEM-Doc (Clean / Scanned / Captured / Augmented). |
| **Baseline** | RT-DocLayout + UniMERNet, pretrained, không train. |
| **Metric** | Layout: IoU/mAP. Math: CDM. |
| **Experiment** | Chạy baseline trên từng điều kiện dữ liệu, đo delta giữa Clean và Noisy. |
| **Error Analysis** | Định lượng % giảm + định tính (dấu thanh, công thức lồng text). |
| **Conclusion** | *Chỉ rút ra sau khi có số.* Có thể ủng hộ, bác bỏ, hoặc không kết luận được (inconclusive). |

### 2.2. Phân biệt 5 khái niệm dễ lẫn

| Khái niệm | Định nghĩa ngắn | Ví dụ trong đề tài |
|-----------|-----------------|--------------------|
| **Research Question (RQ)** | Câu hỏi ta muốn trả lời | "Mô hình layout/MER phương Tây suy giảm bao nhiêu, ở đâu, khi xử lý SGK STEM tiếng Việt?" |
| **Hypothesis (H)** | Câu trả lời *dự đoán*, kiểm chứng được | H1: dấu thanh gây lỗi MER; H2: bố cục đan xen gây lỗi layout |
| **Experiment** | Thao tác tạo ra dữ liệu để kiểm H | Chạy UniMERNet trên tập Clean vs Captured, so CDM |
| **Evidence** | Kết quả quan sát được từ experiment | "CDM giảm từ 0.9 (clean) xuống 0.6 (captured) trên trang giàu dấu thanh" |
| **Claim** | Phát biểu ta khẳng định, *phải* dựa trên evidence | "Trên pilot của chúng tôi, MER suy giảm rõ ở ảnh chụp; cần xác minh trên tập lớn hơn" |

> 🔶 **OPEN DECISION:** Con số ngưỡng (VD giảm bao nhiêu % thì gọi là "suy giảm nghiêm trọng") chưa được tài liệu nguồn quy định → nhóm cần định nghĩa tiêu chí *trước* khi chạy, để tránh "vẽ đường cho hươu chạy" sau khi thấy số.

### 2.3. ⛔ Cảnh báo: KHÔNG viết kết luận kiểu này

> ❌ *"Model phương Tây chắc chắn kém trên SGK Việt Nam."*

Vì sao sai:
- Chữ "chắc chắn" là khẳng định tuyệt đối — nghiên cứu hầu như không bao giờ tuyệt đối.
- Chưa nói *kém ở đâu* (layout hay math?), *kém bao nhiêu*, *trong điều kiện nào*.
- Nó là **niềm tin**, không phải **bằng chứng**.

> ✅ Cách viết đúng: *"Trên pilot 40 trang, điểm CDM của UniMERNet giảm trung bình X% từ Clean sang Captured, với lỗi tập trung ở công thức có dấu thanh và biểu thức lồng trong văn bản (n ví dụ). Kết quả cần được xác nhận trên tập dữ liệu lớn hơn trước khi khái quát."*

### 2.4. Ba loại kết quả — phải phân biệt được

- **Evidence ỦNG HỘ hypothesis:** số liệu đi đúng hướng dự đoán, khác biệt đủ lớn và nhất quán qua nhiều trang. VD: CDM tụt mạnh & ổn định ở mọi trang nhiễu.
- **Evidence KHÔNG ủng hộ (bác bỏ):** số liệu đi ngược dự đoán. VD: model vẫn tốt trên ảnh chụp → H không đúng như nghĩ.
- **Inconclusive (không kết luận được):** số liệu mâu thuẫn/nhiễu/quá ít mẫu. VD: 3 trang tăng, 3 trang giảm, pilot quá nhỏ. → Kết luận đúng đắn nhất là *"chưa đủ dữ liệu để kết luận"* — và đó là một phát hiện hợp lệ, không phải thất bại.

> **WHY phần này quan trọng:** Cả sự nghiệp nghiên cứu được xây trên khả năng nói "tôi chưa biết chắc". Một nhóm dám báo cáo inconclusive trung thực đáng tin hơn một nhóm luôn "chứng minh được" điều mình muốn.

---

## PART 3 — DATASET DESIGN

> Thiết kế **Viet12-STEM-Doc** ở cấp độ kỹ thuật. Đây là bản thiết kế; số lượng cụ thể (30–50 trang pilot) ở Part 4.

### 3.1. Dataset cần chứa gì? (khung 7Q rút gọn)

**WHAT.** Ba nhóm nội dung: (a) **ảnh** trang ở nhiều điều kiện, (b) **annotation** (layout COCO + math LaTeX), (c) **metadata** (nguồn gốc, phiên bản, cách sinh nhiễu).
**WHY.** Đủ 3 nhóm mới *đo được* và *tái lập được*. Thiếu metadata → không biết ảnh nào sinh ra từ đâu → không tái lập → không phải nghiên cứu.
**NEXT.** Team 2 đọc thẳng cấu trúc này để nạp vào pipeline train.

### 3.2. Định nghĩa các đơn vị dữ liệu

| Thuật ngữ | Định nghĩa | Ví dụ |
|-----------|-----------|-------|
| **Sample** | Một đơn vị dữ liệu độc lập được model xử lý. Với layout: **một ảnh trang**. Với MER: **một ảnh công thức đã cắt**. | `math12_ch1_p045_clean.png` |
| **Page** | Một trang SGK (nguồn của layout sample). Một page có nhiều region. | Trang 45 sách Toán 12 |
| **Region** | Một vùng bố cục = 1 bounding box + 1 nhãn lớp. | box (x,y,w,h) nhãn `Display-Math` |
| **Formula** | Một biểu thức toán, có ảnh crop + GT LaTeX. Chỉ Display-Math (và tùy chọn Inline-Math) mới có formula GT. | ảnh $E=mc^2$ + `E = m c^{2}` |
| **Ground Truth** | Đáp án đúng do người xác lập (box+nhãn cho layout; LaTeX cho math). | (xem Part 7, 11) |
| **Variant** | Phiên bản của cùng một page ở một điều kiện: `clean`/`scanned`/`captured`/`augmented`. | trang 45 bản `captured` |

### 3.3. Bốn điều kiện dữ liệu — khác nhau thế nào & để làm gì?

`[OFFICIAL]` Đề tài yêu cầu 3 tập baseline: **Clean, Scanned, Captured/Augmented**. Ta tách rõ 4 khái niệm:

| Điều kiện | Sinh ra bằng | Mô phỏng tình huống thực | Dùng để trả lời câu hỏi |
|-----------|-------------|--------------------------|-------------------------|
| **Clean** | PDF gốc render thẳng ra ảnh | Học liệu số hoàn hảo | Model hoạt động ra sao ở điều kiện lý tưởng? (baseline trần) |
| **Scanned** `[OFFICIAL]` | In ra rồi scan lại | Giáo viên scan sách | Nhiễu scan (bụi, lệch, nhoè) ảnh hưởng ra sao? |
| **Captured** `[OFFICIAL]` | Chụp điện thoại nhiều góc/ánh sáng | Học sinh chụp trang sách | Méo phối cảnh + ánh sáng ảnh hưởng ra sao? |
| **Augmented** `[OFFICIAL]` | Script thêm nhiễu số hóa (OpenCV/Albumentations) | *Nhân bản* nhiễu số lượng lớn | Có thể sinh nhiễu quy mô lớn mà không gán nhãn lại? |

**WHY nhiều điều kiện:**
- **Clean data** = *thước chuẩn trên*: nếu model đã kém ở clean thì vấn đề nằm ở model/tiếng Việt, không phải nhiễu.
- **Noisy (scanned/captured) data** = *đo độ bền (robustness)* trong tình huống thực tế của sản phẩm cuối (giáo viên chụp/scan).
- **Captured data** = tình huống *khó nhất* (méo phối cảnh) — nơi mô hình phương Tây dễ "gãy".
- **Augmented data** = cách *scale* nhiễu rẻ, nhưng (xem Part 6) **không thay thế** được ảnh chụp thật.

> ⚠️ **Điểm mấu chốt về GT:** GT (box + LaTeX) được gán **một lần trên bản Clean**, rồi *tái sử dụng* cho scanned/captured/augmented **nếu và chỉ nếu** phép biến đổi giữ nguyên vị trí đối tượng (VD thêm nhiễu Gauss). Nếu phép biến đổi làm *dịch chuyển hình học* (xoay, méo phối cảnh), box GT phải được **biến đổi tọa độ tương ứng** hoặc gán lại. Đây là lý do augmentation phải *reproducible* và có ma trận biến đổi lưu lại (Part 6).

### 3.4. Schema thư mục đề xuất `[RECO]`

```
viet12-stem-doc/
├── raw/                      # PDF gốc + trang tách ra (KHÔNG chỉnh sửa, read-only)
│   └── math12/ physics12/ chem12/
├── clean/                    # Ảnh trang render từ PDF (điều kiện chuẩn)
│   └── images/
├── scanned/                  # Ảnh in-scan lại (thủ công)
├── captured/                 # Ảnh chụp điện thoại (thủ công)
├── augmented/                # Ảnh sinh tự động + transform params
│   ├── images/
│   └── transforms/           # file .json ghi seed + tham số cho từng ảnh
├── annotations/              # Nhãn layout
│   ├── labelstudio/          # export gốc từ Label Studio (.json)
│   └── coco/                 # COCO chuẩn hoá: instances_clean.json, ...
├── formulas/                 # Mathematical GT
│   ├── crops/                # ảnh công thức đã cắt
│   └── latex/                # GT LaTeX (mỗi crop 1 file .tex hoặc 1 dòng trong .jsonl)
├── metadata/                 # metadata cấp page + cấp dataset
│   ├── pages.csv
│   └── dataset_card.md
├── baseline/                 # output model (predictions)
│   ├── rtdoclayout/
│   └── unimernet/
├── evaluation/               # kết quả metric, bảng, biểu đồ
└── VERSION                   # phiên bản dataset (VD v0.1-pilot)
```

**Giải thích ý nghĩa từng thư mục:**
- `raw/` **read-only**. Không ai được sửa. Đây là "bản gốc pháp lý" để tái lập.
- `clean/` là gốc của mọi biến thể; GT gắn với `clean`.
- `augmented/transforms/` là *linh hồn của reproducibility*: có nó mới tái tạo lại đúng ảnh nhiễu từ ảnh clean.
- `annotations/labelstudio/` giữ export thô; `annotations/coco/` là bản đã chuẩn hoá cho model. Tách hai cái để khi Label Studio đổi format, bản COCO vẫn ổn định.
- `metadata/dataset_card.md` = "chứng minh thư" của dataset (Phụ lục K).

### 3.5. Naming convention & ĐỊNH DANH CHUẨN `[RECO]`

> **Đây là NGUỒN CHÂN LÝ DUY NHẤT cho mọi khoá (id).** Mọi Part sau (crop, GT, inference, eval) đều dùng đúng quy ước này. Không ai được tự chế id kiểu khác.

**Tên file ảnh (self-describing, sortable):**
```
{subject}{grade}_{book}_ch{CH}_p{PPP}_{variant}[_a{AA}].png
  math12_kntt_ch01_p045_clean.png
  math12_kntt_ch01_p045_captured.png
  math12_kntt_ch01_p045_augmented_a03.png
```

**Khoá định danh (BẤT BIẾN toàn dự án):**
```
page_uid    = {subject}{grade}_{book}_ch{CH}_p{PPP}     # KHÔNG chứa variant
              vd: math12_kntt_ch01_p045
region_uid  = {page_uid}_r{RR}   # RR = chỉ số vùng theo reading order (2 chữ số)
formula_uid = {page_uid}_f{FF}   # FF = chỉ số Display-Math theo reading order (2 chữ số)
```
- **Reading order** (định nghĩa vận hành ở pilot) = sort box theo `(y_top, x_left)` tăng dần. Trang nhiều cột: xử lý theo Part 7.5 + ghi Decision Record.
- `FF`/`RR` **gán một lần trên bản clean** và **giữ nguyên** cho scanned/captured/augmented ⇒ *cùng một công thức có cùng `formula_uid` xuyên mọi điều kiện* ⇒ mới so được suy giảm theo từng công thức. **Tuyệt đối không nhúng `variant` hay `annotation_id` vào `formula_uid`** (annotation_id đổi khi re-export ⇒ mất tái lập).

**`src/common/ids.py` — dùng ở MỌI nơi:**
```python
import re
CANON_VARIANTS = ("clean", "scanned", "captured", "augmented")

def page_uid_from_filename(file_name: str) -> str:
    """math12_kntt_ch01_p045_clean.png -> math12_kntt_ch01_p045 (bỏ variant + _aNN + đuôi)"""
    stem = file_name.rsplit(".", 1)[0]
    return re.sub(r"_(%s)(_a\d+)?$" % "|".join(CANON_VARIANTS), "", stem)

def region_uid(page_uid: str, reading_index: int) -> str:
    return f"{page_uid}_r{reading_index:02d}"

def formula_uid(page_uid: str, formula_reading_index: int) -> str:
    return f"{page_uid}_f{formula_reading_index:02d}"
```

> **WHY zero-pad:** `p009` sắp trước `p045` đúng thứ tự; `p9` thì sort chuỗi cho `p10 < p9` → loạn. `book_id` = bộ sách+môn+lớp (vd `math12_kntt`). (Hàm crop chuẩn dùng `formula_uid` nằm ở Part 15.2; hằng số category ở §10.0.)

### 3.6. Metadata schema chuẩn — `pages.csv` `[RECO]`

> **Schema DUY NHẤT** cho toàn dataset (Phụ lục A trỏ về đây — không duy trì hai bản).

```csv
page_uid,book_id,subject,grade,chapter,page_book,pdf_page_no,variant,aug_index,source_pdf,render_dpi,width,height,color_mode,transform_json,selection_reason,annotator,created_by,created_at,checksum_sha1
```
Ý nghĩa các cột dễ nhầm:
- **`page_book`** = số trang *in trên sách*; **`pdf_page_no`** = số trang *vật lý trong file PDF* (từ 1). **Render LUÔN theo `pdf_page_no`**; đặt tên file theo `page_book`. Kiểm chéo: trang render ra có trùng nội dung trang sách không.
- `page_uid`: khóa duy nhất (= tên file bỏ variant + đuôi, xem 3.5).
- `variant` ∈ {clean,scanned,captured,augmented}; `aug_index` chỉ có khi augmented.
- `source_pdf` + `pdf_page_no`: **provenance** — truy ngược về đâu trong PDF nào.
- `transform_json`: đường dẫn file seed/params (chỉ augmented).
- `selection_reason`: **bắt buộc** (khớp DoD 4.4) — trang này phủ ca khó nào.
- `checksum_sha1`: **traceability** — phát hiện file đổi/hỏng.

### 3.7. Bốn tính chất phải bảo đảm

| Tính chất | Nghĩa | Cách ta bảo đảm |
|-----------|-------|-----------------|
| **Reproducibility** (tái lập) | Người khác chạy lại được ra *đúng* kết quả | seed cố định + transform_json + VERSION |
| **Provenance** (xuất xứ) | Biết mỗi ảnh đến từ đâu | source_pdf + pdf_page_no |
| **Traceability** (truy vết) | Lần theo được một sample qua toàn pipeline | ID nhất quán + checksum |
| **Versioning** (phiên bản) | Biết đang dùng dataset bản nào | file VERSION + tag Git |

> 🔶 **OPEN DECISION (bản quyền):** Việc trích trang SGK vào một dataset và (có thể) chia sẻ cho bài báo/quốc tế liên quan **bản quyền SGK**. Tài liệu nguồn không quy định cách xử lý. → Cần hỏi giảng viên: phạm vi sử dụng, có được công bố ảnh trang không, hay chỉ công bố annotation + số liệu. (Xem Questions for Supervisor.)

---

## PART 4 — DATA SOURCE SELECTION

### 4.1. Vì sao KHÔNG chọn trang ngẫu nhiên hoàn toàn?

`[RECO]` Mục tiêu pilot là *kiểm thử pipeline trên đủ loại thách thức*, không phải ước lượng phân phối toàn sách. Nếu random, 40 trang có thể toàn trang chữ, thiếu bảng/đồ thị/công thức lồng — pipeline "trông có vẻ chạy" nhưng chưa hề bị thử thách ở ca khó. Ta dùng **stratified purposive sampling** (chọn có chủ đích, phủ đủ tầng độ khó).

### 4.2. Các chiều đa dạng cần phủ

Chọn sao cho pilot chứa đủ các loại sau (mỗi loại ≥ vài mẫu):

- text thuần (đoạn văn dày) — để test lớp Text & lỗi dấu thanh
- paragraph xen inline-math — **ca lõi của H2** (đan xen)
- table (bảng biến thiên, bảng số liệu) — test Table & Table/Figure confusion
- figure / đồ thị hàm số — test Figure & Figure/Text (caption)
- display math đơn giản ($E=mc^2$)
- display math phức tạp (phân số lồng, tích phân, ma trận, giới hạn)
- dense mathematical content (trang nhiều công thức)
- complex educational layout (hộp ghi nhớ, ví dụ có viền, nhiều cột)
- công thức hóa học lồng trong văn bản — **ca lõi bài toán "bó tay"** mà đề tài nêu

### 4.3. Phân bổ pilot đề xuất `[RECO]`

Pilot mục tiêu **~30–50 trang** (đề xuất vận hành, *không* thay mục tiêu dài hạn 150–200 trang `[OFFICIAL]`). Gợi ý phân bổ 40 trang:

| Môn | Số trang | Ưu tiên nội dung |
|-----|----------|------------------|
| Toán 12 | 22 | đồ thị, tích phân, ma trận, giới hạn, hệ thức |
| Vật lý 12 | 14 | công thức nhiều biến, bảng số liệu, sơ đồ mạch |
| Hóa học 12 | 14 | phương trình hóa học lồng trong văn bản, bảng tuần hoàn cục bộ |

> **WHY** trọng số Toán cao hơn: mật độ và độ phức tạp công thức toán cao nhất → thử thách MER mạnh nhất.

### 4.4. Page-selection checklist (dùng khi duyệt từng trang)

Một trang **đáng đưa vào pilot** nếu đạt ≥ 3/6 tiêu chí:

- [ ] Có ≥ 1 đối tượng "khó" (display-math phức tạp / bảng / đồ thị / công thức hóa lồng text)?
- [ ] Có ít nhất 2 lớp taxonomy khác nhau trên trang (không phải trang chỉ toàn Text)?
- [ ] Bổ sung một *loại* đa dạng còn thiếu trong pilot (tra bảng phủ ở 4.2)?
- [ ] Chất lượng bản gốc đủ tốt (không mờ, không thiếu nửa trang)?
- [ ] Không trùng lặp nội dung với trang đã chọn (tránh 5 trang gần giống nhau)?
- [ ] Có yếu tố *tiếng Việt đặc thù* (nhiều dấu thanh, chữ có dấu chồng) để test H1?

**Definition of Done cho việc chọn nguồn:** có bảng `metadata/pages.csv` liệt kê ≥30 page với lý do chọn (một cột `selection_reason`), được Nguyên + Khoa review, và ma trận phủ 4.2 không còn ô trống quan trọng.

**WHAT CAN GO WRONG:** chọn toàn trang đẹp, dễ → pilot không bao giờ chạm ca khó → baseline trông "ổn" giả tạo → kết luận sai. Cố tình *nhắm vào ca khó* là đúng tinh thần đề tài (đi tìm giới hạn của model).

---

## PART 5 — PDF TO IMAGE PIPELINE

> Chuyển PDF gốc → ảnh trang **nhất quán, tái lập được**. Đây là bước đầu tiên chạm dữ liệu thật.

### 5.0. ENVIRONMENT & PREREQUISITES (cài trước, pin version)

> **Nguyên tắc:** cài **một lần**, **pin version**, kiểm **trước** khi chạm dữ liệu. Không ai bắt đầu Part 5+ khi mục này chưa PASS.

**(a) Phụ thuộc hệ thống (OS-level):**
```bash
# Ubuntu/Debian (Windows dùng WSL)
sudo apt-get update
sudo apt-get install -y poppler-utils imagemagick               # pdftoppm, identify
sudo apt-get install -y texlive-latex-extra texlive-science dvipng   # render LaTeX (amsmath, mhchem) — Part 11.7
```
Kiểm: `pdftoppm -v` · `identify -version` · `latex --version` · `dvipng --version` đều in version.

**(b) Môi trường Python (một venv cho cả nhóm):**
```bash
python -m venv .venv && source .venv/bin/activate
python -m pip install -U pip
pip install pymupdf opencv-python-headless pillow numpy pycocotools albumentations matplotlib label-studio
pip freeze > env/requirements.lock.txt      # ← COMMIT file này: "ảnh chụp" môi trường
```
> ⚠️ `[IMPL]` `albumentations`/`unimernet` **đổi API theo version**. `requirements.lock.txt` đảm bảo 3 máy giống nhau; mọi experiment log ghi kèm hash của nó.

**(c) DoD của 5.0:**
- [ ] 4 lệnh hệ thống ở (a) đều in version.
- [ ] `python -c "import fitz,cv2,PIL,numpy,pycocotools,albumentations,matplotlib"` không lỗi.
- [ ] `render_latex(r"\ce{H2O}")` và `render_latex(r"\begin{matrix}a&b\\c&d\end{matrix}")` (Part 11.7) **ra ảnh, không lỗi** — nếu lỗi, math GT/eval sẽ hỏng ⇒ sửa TeX trước.
- [ ] `env/requirements.lock.txt` đã commit.

### 5.1. Sơ đồ pipeline (dạng text)

```
PDF gốc (raw/) 
   → [1] chọn trang cần lấy (theo pages.csv)
   → [2] render trang → ảnh raster ở DPI cố định
   → [3] chuẩn hoá: color mode, kích thước, format
   → [4] đặt tên theo naming convention
   → [5] ghi checksum + dòng metadata
   → lưu vào clean/images/
```

### 5.2. Các khái niệm phải hiểu trước khi chạy

| Khái niệm | Giải thích cho người mới | Vì sao quan trọng ở đây |
|-----------|--------------------------|-------------------------|
| **DPI** (dots per inch) | Số điểm ảnh trên mỗi inch khi "chụp" trang PDF. DPI cao → ảnh nét, file to. | Công thức toán nhỏ cần DPI đủ cao để nét; nhưng quá cao thì chậm & tốn bộ nhớ. |
| **Resolution** | Kích thước ảnh tính bằng pixel (rộng × cao). | Model có kích thước input ưa thích; ảnh quá nhỏ → mất chi tiết dấu thanh. |
| **Image format** | PNG (không mất dữ liệu) vs JPG (nén mất dữ liệu). | PNG cho bản clean/GT; JPG chỉ dùng khi *cố ý* mô phỏng nén (Part 6). |
| **RGB / Grayscale** | Ảnh màu 3 kênh vs ảnh xám 1 kênh. | Nhất quán màu để model & augmentation không "bối rối". SGK có màu → giữ RGB cho clean. |

### 5.3. Tham số chuẩn đề xuất `[RECO]` (ghi vào một config, dùng cho MỌI trang)

```yaml
# config/render.yaml
dpi: 200            # đề xuất: đủ nét cho công thức, file vừa phải. (Thử 150/200/300 rồi chốt)
format: png         # clean = png (lossless)
color_mode: RGB
resize: none        # KHÔNG resize ở bước này; giữ nguyên tỉ lệ trang
```

> **WHY nhất quán preprocessing là sống còn:** Nếu trang A render 150 DPI, trang B 300 DPI, thì kích thước công thức khác nhau → model & metric IoU bị lệch một cách *nhân tạo*. Mọi khác biệt hiệu năng phải đến từ *nội dung*, không phải từ *cách ta render*. **Một config, chạy tất cả.**

### 5.4. Lệnh mẫu `[IMPL]` (giải thích từng phần)

Dùng `pdftoppm` (bộ Poppler) — ổn định, hoặc `PyMuPDF` (fitz) trong Python để kiểm soát tốt hơn.

**Cách A — dòng lệnh (nhanh, ít phụ thuộc):**
```bash
# Render trang 45 của math12.pdf ra PNG 200 DPI
pdftoppm -f 45 -l 45 -r 200 -png raw/math12/math12_kntt.pdf tmp/math12_p045
#   -f 45 -l 45 : từ trang 45 đến trang 45 (first/last)
#   -r 200      : 200 DPI
#   -png        : xuất PNG
#   tmp/...     : tiền tố tên file output
```
- **Input:** PDF + số trang. **Output:** `tmp/math12_p045-45.png`.
- **Expected result:** một PNG RGB, nét, đọc được công thức nhỏ.

**Cách B — Python (kiểm soát metadata + đặt tên chuẩn) `[IMPL]`:**
```python
import fitz  # PyMuPDF
import hashlib, csv, datetime, os

def render_page(pdf_path, pdf_page_no, out_path, dpi=200):
    doc = fitz.open(pdf_path)
    page = doc[pdf_page_no - 1]                 # fitz đánh số từ 0
    zoom = dpi / 72                             # 72 = DPI mặc định của PDF
    mat = fitz.Matrix(zoom, zoom)
    pix = page.get_pixmap(matrix=mat, colorspace=fitz.csRGB)
    pix.save(out_path)                          # PNG
    doc.close()
    with open(out_path, "rb") as f:
        checksum = hashlib.sha1(f.read()).hexdigest()
    return pix.width, pix.height, checksum

# Ví dụ dùng:
w, h, chk = render_page("raw/math12/math12_kntt.pdf", 45,
                        "clean/images/math12_kntt_ch01_p045_clean.png", dpi=200)
print(w, h, chk)
```
- **Input:** đường dẫn PDF, số trang PDF, đường ra, DPI.
- **Output:** file PNG + trả về (width, height, sha1) để ghi vào `pages.csv`.
- **Expected:** in ra kích thước hợp lý (VD ~1654×2339 ở A4 200 DPI) và một chuỗi sha1.

### 5.5. Cách kiểm tra (HOW TO VERIFY)

- [ ] Mở 5 ảnh bất kỳ, phóng to công thức nhỏ nhất: có đọc được từng ký tự/dấu thanh không?
- [ ] `identify ảnh.png` (ImageMagick) hoặc kiểm trong Python: đúng RGB, đúng DPI mong đợi?
- [ ] Số lượng ảnh = số dòng variant=clean trong `pages.csv`?
- [ ] Mọi ảnh đều có checksum ghi lại?

### 5.6. Failure cases thường gặp

| Triệu chứng | Nguyên nhân | Xử lý |
|-------------|-------------|-------|
| Ảnh mờ, công thức rỗ | DPI quá thấp | tăng DPI (150→200→300) rồi chốt |
| File PNG khổng lồ, chậm | DPI quá cao | hạ DPI, cân bằng nét/dung lượng |
| Chữ có dấu bị vỡ hạt | render sai colorspace / anti-alias tắt | dùng csRGB, bật khử răng cưa |
| Thiếu trang, lệch số trang | `pdf_page_no` ≠ số in trên sách | luôn dùng *số trang vật lý trong PDF*, ghi cả số trang sách trong metadata |
| Trang trắng | PDF có trang bìa/đệm | kiểm tra bằng mắt, cập nhật pages.csv |

**WHAT NEXT:** ảnh `clean/` là (a) nguồn để gán nhãn (Part 7–10), (b) đầu vào augmentation (Part 6), (c) đầu vào baseline inference (Part 14–15).

---

## PART 6 — NOISE & DATA AUGMENTATION

> `[OFFICIAL]` Đề tài yêu cầu tạo dữ liệu nhiễu bằng 2 cách: **thủ công** (scan/chụp) và **tự động** (script OpenCV/Albumentations). Part này giải thích *bản chất* để không lạm dụng augmentation.

### 6.1. Ba loại "nhiễu" — KHÔNG được đánh đồng

| Loại | Cách tạo | Đặc trưng nhiễu | Vai trò nghiên cứu |
|------|----------|-----------------|--------------------|
| **Scanned data** `[OFFICIAL]` | in ra → scan | bụi, vệt, lệch nhẹ, hơi mờ, nền hơi xám | nhiễu *thật* của máy scan |
| **Captured data** `[OFFICIAL]` | chụp điện thoại nhiều góc/sáng | **méo phối cảnh**, bóng, loá, mất nét không đều, nền lộn xộn | nhiễu *thật, khó nhất*, sát sản phẩm cuối |
| **Synthetic Augmentation** `[OFFICIAL]` | script thêm nhiễu số hoá | Gauss, blur, contrast, xoay nhẹ... theo tham số | *nhân bản* quy mô lớn, rẻ |

### 6.2. ⚠️ Vì sao "thêm random noise" ≠ "giống ảnh chụp"?

Đây là hiểu lầm nguy hiểm nhất của Part này.

- Ảnh **chụp thật** có nhiễu *có cấu trúc vật lý*: bóng đổ theo hướng đèn, méo phối cảnh do góc máy, mất nét *cục bộ* (một góc mờ, góc kia nét), loá phản quang, cong vênh giấy. Những thứ này **tương quan không gian** và **phi tuyến**.
- **Random Gaussian noise** chỉ rắc hạt ngẫu nhiên *độc lập từng pixel* — không có bóng, không méo, không cong. Model học "khử hạt" rất dễ, nhưng ngoài đời gặp *méo phối cảnh* thì vẫn gãy.

> **Hệ quả nghiên cứu:** Nếu ta *chỉ* đo trên augmented rồi kết luận "model bền với nhiễu", ta **đánh lừa chính mình**. Vì vậy đề tài giữ **cả** scanned + captured thật để đo robustness *trung thực*, và dùng augmented để *scale* — không để *thay thế*. 🔶 **OPEN DECISION:** tỉ lệ augmented vs thật trong tập test, và có đưa augmented vào tập test đánh giá baseline hay chỉ để Team 2 train — cần nhóm thống nhất.

### 6.3. Từng augmentation — theo khung WHY→HOW→PARAMS→EFFECT→RISK→WHEN→VALIDATE

Ký hiệu thư viện: **A** = Albumentations, **CV** = OpenCV.

**(1) Gaussian noise**
- WHY: mô phỏng nhiễu cảm biến / hạt scan. HOW: cộng nhiễu phân phối chuẩn vào pixel (A: `GaussNoise`). PARAMS: phương sai `var_limit` (VD 10–50). EFFECT: ảnh lấm tấm hạt. RISK: quá mạnh → xoá dấu thanh nhỏ. WHEN: mô phỏng scan/cảm biến yếu. VALIDATE: mắt thường vẫn đọc được công thức nhỏ nhất.

**(2) Blur (làm mờ)**
- WHY: mô phỏng lệch nét/rung tay. HOW: lọc trung bình/Gaussian (A: `GaussianBlur`, `MotionBlur`). PARAMS: `blur_limit` (kernel 3–7). EFFECT: cạnh chữ nhoè. RISK: nhoè dính nét công thức → sai cấu trúc. WHEN: mô phỏng chụp rung/scan mờ. VALIDATE: `x^2` không biến thành `x2`.

**(3) Contrast change (độ tương phản)**
- WHY: mô phỏng máy scan/đèn khác nhau. HOW: co giãn histogram (A: `RandomBrightnessContrast`). PARAMS: `contrast_limit` (±0.2). EFFECT: chữ đậm/nhạt hơn. RISK: quá thấp → chữ chìm vào nền. WHEN: đa dạng thiết bị. VALIDATE: nét công thức không mất.

**(4) Brightness (độ sáng)**
- WHY: ánh sáng phòng/đèn flash. HOW: cộng/nhân độ sáng (A: `RandomBrightnessContrast`). PARAMS: `brightness_limit` (±0.2). RISK: cháy sáng vùng loá. WHEN: mô phỏng chụp thiếu/thừa sáng. VALIDATE: không vùng nào trắng xoá mất chữ.

**(5) Rotation (xoay)**
- WHY: đặt sách lệch khi scan/chụp. HOW: xoay quanh tâm (A: `Rotate`, `SafeRotate`; CV: `warpAffine`). PARAMS: `limit` (±3° đến ±5°, nhỏ). ⚠️ EFFECT: **tọa độ box GT thay đổi** → phải biến đổi box theo cùng ma trận. RISK: quên transform box → GT lệch → metric sai. WHEN: mô phỏng lệch nhẹ. VALIDATE: vẽ lại box lên ảnh xoay, box vẫn ôm đúng đối tượng.

**(6) Perspective distortion (méo phối cảnh)**
- WHY: **đặc trưng chụp điện thoại** — quan trọng nhất để mô phỏng captured. HOW: biến đổi phối cảnh 4 điểm (A: `Perspective`; CV: `getPerspectiveTransform` + `warpPerspective`). PARAMS: `scale` (0.02–0.06). ⚠️ EFFECT: **box GT phải biến đổi qua cùng homography** (box thành tứ giác → lấy bao lồi hoặc bbox bao ngoài). RISK: cao nhất về lệch GT. WHEN: mô phỏng captured. VALIDATE: overlay box, kiểm 5 ảnh bằng mắt.

**(7) Local skew (nghiêng cục bộ)** `[OFFICIAL nêu "xoay nghiêng cục bộ"]`
- WHY: một phần trang cong/nghiêng (gáy sách). HOW: warp phi tuyến vùng cục bộ (CV remap / elastic nhẹ). PARAMS: biên độ nhỏ. RISK: méo công thức → GT LaTeX vẫn đúng nhưng ảnh khó. WHEN: mô phỏng cong giấy. VALIDATE: công thức vẫn "đọc được" bởi người.

**(8) Compression artifacts (nhiễu nén)** `[RECO]`
- WHY: ảnh gửi qua app bị nén JPG. HOW: lưu lại ở JPG chất lượng thấp (A: `ImageCompression`). PARAMS: `quality` (30–60). RISK: khối 8×8 làm rỗ nét mảnh. WHEN: mô phỏng ảnh qua Zalo/messenger. VALIDATE: dấu thanh còn phân biệt được.

> **Nguyên tắc chung:** augment phải giữ ảnh **vẫn đọc được bởi con người**. Nếu người còn không đọc nổi công thức, đó là nhiễu *vô nghĩa* (model sai là đương nhiên, không nói lên điều gì về tiếng Việt).

### 6.4. Pipeline augmentation REPRODUCIBLE `[RECO]`

**Định nghĩa reproducibility ĐÚNG:** tái lập = **cùng seed + cùng `requirements.lock.txt` + single-thread** ⇒ kết quả lặp lại. **Không** hứa byte-for-byte qua mọi máy/version.

```python
import random, numpy as np, cv2
def fix_all_seeds(seed):
    random.seed(seed); np.random.seed(seed)
    cv2.setNumThreads(1)           # bỏ bất định đa luồng
    # Albumentations: theo version -> A.Compose(..., seed=seed) HOẶC dựa np.random
```
> ⚠️ `[IMPL]` Tên tham số đổi theo version: `GaussNoise(var_limit=...)` bản cũ có thể thành `std_range=...` bản mới; `A.Compose(seed=)` không có ở mọi bản. Kiểm `pip show albumentations` + tài liệu bản đang cài; ghi version vào log.

**`build_aug`** dùng đúng danh sách phép ở 6.3 (giữ nguyên), thêm `bbox_params` để **đồng bộ box**:
```python
import albumentations as A, cv2
def build_aug(seed):
    return A.Compose(
        [A.GaussNoise(var_limit=(10,40), p=0.5),          # (kiểm tên param theo version)
         A.GaussianBlur(blur_limit=(3,5), p=0.3),
         A.RandomBrightnessContrast(0.15,0.15, p=0.5),
         A.Perspective(scale=(0.02,0.05), p=0.4),
         A.Rotate(limit=4, border_mode=cv2.BORDER_CONSTANT, p=0.4)],
        bbox_params=A.BboxParams(format="coco", label_fields=["labels"]),
        seed=seed)
```

**Nạp box từ COCO chuẩn rồi augment (input/output RÕ RÀNG):**
```python
import cv2, json
def load_boxes_for_image(coco, image_id):
    boxes, labels = [], []
    for a in coco["annotations"]:
        if a["image_id"] == image_id:
            boxes.append(a["bbox"])            # [x,y,w,h] pixel
            labels.append(a["category_id"])    # id CHUẨN (§10.0)
    return boxes, labels

def augment_one(image_path, boxes, labels, seed, out_img, out_meta):
    fix_all_seeds(seed)
    image = cv2.imread(image_path)             # BGR; ghi lại BGR nên nhất quán trên đĩa
    out = build_aug(seed)(image=image, bboxes=boxes, labels=labels)
    cv2.imwrite(out_img, out["image"])
    json.dump({"seed": seed, "src": image_path,
               "bboxes_out": [list(map(float,b)) for b in out["bboxes"]],
               "labels_out": list(map(int, out["labels"]))},
              open(out_meta,"w"), ensure_ascii=False, indent=2)
    return out
```
- **Input:** `boxes`=`[x,y,w,h]` pixel lấy từ `instances_clean.json`; `labels`=`category_id` chuẩn. (Trước đây tài liệu không nói box từ đâu — nay đã rõ.)
- **`out_meta`** trong `augmented/transforms/`: bản đồ clean→augmented để tái lập.

### 6.5. Vai trò của OpenCV vs Albumentations `[IMPL]`

- **OpenCV (cv2):** thư viện thị giác nền tảng — đọc/ghi ảnh, các phép biến đổi hình học gốc (warpAffine/warpPerspective), lọc. Linh hoạt nhưng *phải tự đồng bộ box*.
- **Albumentations:** thư viện augmentation cấp cao *xây trên* NumPy/OpenCV, có sẵn hàng chục phép, **tự đồng bộ bounding box** theo phép biến đổi. → Dùng Albumentations cho pipeline chính; dùng OpenCV cho các phép đặc thù (local skew) chưa có sẵn.

### 6.6. Definition of Done cho augmentation

- [ ] Mỗi ảnh augmented có `transform.json` (seed + bboxes_out + labels_out).
- [ ] Chạy lại cùng seed **trong môi trường `requirements.lock.txt`, single-thread** → **`bboxes_out` TRÙNG KHÍT về số** (bắt buộc). Ảnh: checksum khớp *nếu cùng encoder*; nếu ảnh lệch nhẹ do encoder mà bbox vẫn khớp ⇒ **vẫn đạt**. (byte-for-byte KHÔNG dùng làm điều kiện Done cứng.)
- [ ] ≥5 ảnh có phép hình học (xoay/méo): overlay `bboxes_out`, xác nhận ôm đúng.
- [ ] Nếu dùng augmented để **đánh giá**: `instances_augmented.json` (6.7) tồn tại và `validate_coco` PASS.

### 6.7. Xuất `instances_augmented.json` `[RECO]` (bắt buộc nếu đánh giá trên augmented)

> Trước đây bước này **thiếu hẳn**: box được augment trong bộ nhớ nhưng không viết ra COCO ⇒ không có GT để chấm augmented. Nay bổ sung.

```python
import json
from src.common.categories import COCO_CATEGORIES   # §10.0
def build_augmented_coco(clean_coco_path, plan, out_coco):
    """plan = list (src_image_id, out_file_name, out_meta_json) đã augment."""
    clean = json.load(open(clean_coco_path, encoding="utf-8"))
    id2im = {im["id"]: im for im in clean["images"]}
    images, anns, aid = [], [], 1
    for new_id,(src_id,fname,meta_json) in enumerate(plan, 1):
        meta = json.load(open(meta_json, encoding="utf-8")); s = id2im[src_id]
        images.append({"id":new_id,"file_name":fname,"width":s["width"],"height":s["height"]})
        for (x,y,w,h),cid in zip(meta["bboxes_out"], meta["labels_out"]):
            anns.append({"id":aid,"image_id":new_id,"category_id":int(cid),
                         "bbox":[x,y,w,h],"area":w*h,"iscrowd":0}); aid+=1
    json.dump({"images":images,"annotations":anns,"categories":COCO_CATEGORIES},
              open(out_coco,"w",encoding="utf-8"), ensure_ascii=False, indent=2)
```
> **WHY:** nếu (OPEN DECISION 6.2) nhóm quyết *đánh giá* baseline trên augmented, **phải có GT augmented** này; nếu không, augmented chỉ để Team 2 train. `validate_coco` phải PASS cả file này.

**WHAT NEXT:** augmented data là (a) một điều kiện đầu vào baseline (Team 1), (b) nguyên liệu *nhân bản* để Team 2 train.

---

## PART 7 — ANNOTATION TAXONOMY

> `[OFFICIAL]` Taxonomy chốt gồm **6 lớp**: `Title, Text, Table, Figure, Inline-Math, Display-Math`. **Không tự ý thêm lớp mới** nếu tài liệu nguồn không yêu cầu; nếu cần rule bổ sung, đánh dấu `[RECO]` và đưa vào Decision Record.

### 7.1. Bảng định nghĩa 6 lớp

Với mỗi lớp: **Định nghĩa · Ví dụ ĐÚNG · Ví dụ SAI · Ca biên · Lỗi hay gặp · Rule · Vì sao rule quan trọng cho model.**

#### `Title` — Tiêu đề
- **Định nghĩa:** dòng/khối chữ đóng vai trò tiêu đề: tên chương, tên bài, đề mục lớn.
- **ĐÚNG:** "CHƯƠNG I. HÀM SỐ", "Bài 3: Tích phân".
- **SAI:** một câu in đậm giữa đoạn văn (đó là Text nhấn mạnh).
- **Ca biên:** số thứ tự bài + tên bài trên 2 dòng → gộp thành 1 Title.
- **Lỗi hay gặp:** gán mọi chữ in đậm là Title.
- **Rule:** Title = *chức năng phân cấp*, không phải *độ đậm*.
- **WHY:** model học phân đoạn tài liệu theo cấp; nếu Title lẫn Text, reading order & trích xuất tri thức (Team 3) hỏng.

#### `Text` — Văn bản thường
- **Định nghĩa:** đoạn văn, câu, danh sách chữ; bao gồm chữ có *inline-math nhỏ* nằm trong dòng (xem quy tắc 7.2).
- **ĐÚNG:** đoạn giảng giải, đề bài dạng chữ.
- **SAI:** một công thức tách dòng canh giữa (đó là Display-Math).
- **Ca biên:** caption của hình (xem 7.3).
- **Lỗi hay gặp:** cắt Text thành nhiều box vụn theo từng dòng.
- **Rule:** Text bao trọn một *khối văn bản mạch lạc*.
- **WHY:** box Text ổn định giúp OCR & đo IoU đúng.

#### `Table` — Bảng
- **Định nghĩa:** dữ liệu tổ chức theo hàng/cột, thường có đường kẻ hoặc căn cột rõ.
- **ĐÚNG:** bảng biến thiên, bảng số liệu, bảng tuần hoàn cục bộ.
- **SAI:** hai cột văn bản (đó là layout 2 cột, không phải Table).
- **Ca biên:** bảng biến thiên chứa công thức bên trong → xem 7.4.
- **Lỗi hay gặp:** nhầm sơ đồ/khung viền là Table.
- **Rule:** Table = cấu trúc *hàng×cột* thực sự.
- **WHY:** Table có nhiệm vụ downstream riêng (table structure); nhầm → sai cả nhánh.

#### `Figure` — Hình/đồ thị
- **Định nghĩa:** đồ thị hàm số, hình vẽ, sơ đồ, ảnh minh hoạ.
- **ĐÚNG:** đồ thị parabol, sơ đồ mạch điện, hình hình học.
- **SAI:** công thức lớn (đó là Display-Math, không phải Figure).
- **Ca biên:** đồ thị có nhãn trục chứa ký hiệu toán → vẫn là Figure (nhãn trục là một phần hình).
- **Lỗi hay gặp:** gộp cả caption vào Figure hoặc tách nhầm.
- **Rule:** Figure = vùng *đồ hoạ*; caption xử lý theo 7.3.
- **WHY:** phân biệt Figure/Table/Math là lõi của bài toán layout tiếng Việt.

#### `Inline-Math` — Công thức trong dòng
- **Định nghĩa:** biểu thức toán *nằm giữa dòng văn bản*, cùng baseline với chữ.
- **ĐÚNG:** "...với $v = s/t$ ta có...".
- **SAI:** công thức tách dòng canh giữa.
- **Ca biên:** một ký hiệu đơn lẻ ($x$, $\alpha$) — 🔶 **OPEN DECISION**: có gán Inline-Math cho ký hiệu đơn lẻ không, hay bỏ qua? Cần chốt trong Golden Sample.
- **Lỗi hay gặp:** gán mọi số trong câu là Inline-Math.
- **Rule:** Inline-Math = *biểu thức* (có phép toán/cấu trúc), không phải mọi con số.
- **WHY:** đây là **H2 (đan xen)**; ranh giới Text/Inline-Math quyết định độ khó thật của bài toán.

#### `Display-Math` — Công thức tách dòng
- **Định nghĩa:** biểu thức toán *đứng riêng một dòng/khối*, thường canh giữa, có thể đánh số.
- **ĐÚNG:** một phương trình tích phân canh giữa.
- **SAI:** công thức nhỏ trong câu (đó là Inline-Math).
- **Ca biên:** hệ nhiều phương trình xếp chồng → 🔶 **OPEN DECISION**: một box hay nhiều box? (khuyến nghị: một box cho cả hệ nếu là một đơn vị ngữ nghĩa).
- **Lỗi hay gặp:** cắt sát khít làm mất ký tự mép.
- **Rule:** Display-Math là **lớp DUY NHẤT bắt buộc có Mathematical GT (LaTeX)** ở Phase 1 `[OFFICIAL]`.
- **WHY:** đây là đầu vào của UniMERNet + chấm CDM.

### 7.2. TEXT vs INLINE-MATH (ranh giới quan trọng nhất)

- Nếu ký hiệu toán *hoà vào dòng chữ* và **không** phải trọng tâm → phần công thức gán `Inline-Math`, phần chữ quanh nó vẫn `Text`.
- 🔶 **OPEN DECISION (rất cần chốt sớm):** Khi inline-math nằm *giữa* một câu, ta (a) gán một box Inline-Math *chồng/nằm trong* box Text, hay (b) cắt Text thành các đoạn và chèn Inline-Math giữa? Cả hai đều có lý; **phải chọn một và ghi vào Guideline v1**, vì nó ảnh hưởng cách tính IoU và cách model học. (Khuyến nghị `[RECO]`: cho phép Inline-Math là box riêng *nằm trong* vùng Text, đánh dấu overlap là hợp lệ — nhưng Khoa chốt.)

### 7.3. FIGURE vs surrounding TEXT & CAPTION

- **Caption** (chú thích hình: "Hình 2.1. Đồ thị hàm số..."): `[OFFICIAL]` taxonomy **không có lớp Caption**. → 🔶 **OPEN DECISION:** caption gán là `Text` (khuyến nghị `[RECO]`) hay tạo quy ước riêng? Không được tự thêm lớp `Caption` vào COCO nếu chưa chốt. Mặc định tạm: caption = `Text`, ghi rõ trong Guideline.
- Figure **không** nuốt caption: vẽ Figure ôm phần đồ hoạ, caption là box Text riêng bên dưới.

### 7.4. FIGURE vs TABLE & Bảng có công thức

- Figure = đồ hoạ liên tục; Table = lưới hàng/cột. Khi phân vân: "Có thể mô tả bằng hàng×cột không?" Có → Table.
- **Bảng chứa công thức** (bảng biến thiên): gán cả vùng là `Table`; công thức *bên trong* bảng — 🔶 **OPEN DECISION**: có gán thêm Inline/Display-Math lồng bên trong Table không? (Khuyến nghị `[RECO]`: Phase 1 pilot **không** gán math lồng trong Table để giảm mơ hồ; ghi lại quyết định.)

### 7.5. Mixed regions & Nested visual structures

- **Vùng trộn** (ví dụ có viền chứa cả chữ + công thức + hình nhỏ): gán theo *đối tượng con* nếu tách được; nếu không tách rõ → ghi vào Disagreement log, đưa Khoa.
- **Cấu trúc lồng** (hình trong hình, công thức trong bảng): Phase 1 ưu tiên *đơn giản, nhất quán* hơn *chi tiết tối đa*. Mọi quy ước lồng nhau phải nằm trong Guideline v1.

> **Nguyên tắc chống "sáng tạo taxonomy":** Nếu bạn thấy cần một lớp/rule mới, **dừng lại**, ghi vào Disagreement log, đánh dấu `[RECO]`, và đưa nhóm quyết định. Không tự ý mở rộng taxonomy giữa chừng — bất nhất taxonomy là lỗi số 1 phá hỏng dataset.

---

## PART 8 — ANNOTATION GUIDELINE

> Đây là tài liệu **cho annotator** (Là Nguyên & Đức). Viết như quy trình thao tác chuẩn (SOP).

### 8.1. Trước khi annotation (chuẩn bị)
- [ ] Đọc Part 7 (taxonomy) và Guideline này.
- [ ] Đã cài Label Studio, mở được project pilot (Part 10).
- [ ] Có bản Golden Sample rules (Part 9) bên cạnh.
- [ ] Hiểu 3 phím tắt cơ bản (chọn lớp, vẽ box, submit).

### 8.2. Quy trình thao tác từng ảnh

1. **Mở image** kế tiếp trong hàng đợi.
2. **Quan sát toàn trang 5 giây** trước khi vẽ: nhẩm có mấy đối tượng, loại gì (tránh vẽ vội, sót).
3. **Vẽ box** ôm sát đối tượng, chừa lề ~2–3px (không cắt cụt ký tự mép, không thừa quá rộng).
4. **Chọn class** đúng theo Part 7.
5. **Xử lý overlap:** nếu Inline-Math nằm trong Text → theo quy ước đã chốt ở 7.2.
6. **Xử lý boundary:** đối tượng dính mép trang → box dừng ở mép ảnh, không tràn ra ngoài (bbox phải nằm trong ảnh).
7. **Xử lý ambiguous:** không chắc lớp nào → *không đoán bừa*; gắn cờ (dùng chức năng skip/flag hoặc ghi region_id vào Disagreement log) và tiếp tục.
8. **Xử lý formula:** Display-Math phải ôm trọn công thức (cả dấu ngoặc, chỉ số trên/dưới, đường phân số) — thiếu một chỉ số trên là hỏng GT.
9. **Text trong figure:** chữ *thuộc về hình* (nhãn trục, chú thích trong hình) → coi là phần của Figure, **không** tách thành Text.
10. **Bảng có công thức / công thức trong paragraph:** theo quyết định 7.4 / 7.2.
11. **Submit** khi chắc chắn; nếu còn nghi ngờ → flag.

### 8.3. Quy trình chất lượng: Annotate → Self-check → Peer review → Resolve → Finalize

```
[Annotate]  người A gán nhãn 1 batch (VD 10 trang)
    ↓
[Self-check] A tự rà theo checklist 8.4 (không nhờ ai)
    ↓
[Peer review] người B (khác A) soi lại, ghi điểm nghi ngờ
    ↓
[Resolve disagreement] A & B đối chiếu Guideline; nếu Guideline có rule → theo rule;
                       nếu CHƯA có rule → ghi Disagreement log → Khoa quyết định → cập nhật Guideline
    ↓
[Finalize] chốt nhãn, đánh dấu batch = reviewed
```

### 8.4. Self-check checklist (annotator tự rà trước khi submit batch)
- [ ] Mọi đối tượng "khó" trên trang đều có box (không sót công thức/bảng)?
- [ ] Không box nào tràn ra ngoài ảnh?
- [ ] Không có box "rỗng" (vẽ nhầm rồi bỏ)?
- [ ] Nhãn lớp khớp Part 7 (đặc biệt Inline vs Display, Table vs Figure)?
- [ ] Display-Math ôm trọn (không cụt chỉ số/căn/ngoặc)?
- [ ] Ca nghi ngờ đã được flag thay vì đoán bừa?

### 8.5. Disagreement protocol (giao thức xử lý bất đồng)

> **Ví dụ tình huống:** Nguyên gán một khối là `Display-Math`, Đức gán `Figure` (một công thức được vẽ cách điệu).

Quy trình bắt buộc:
1. **Không tự ý đoán** ai đúng.
2. **Ghi lại** vào Disagreement log (Phụ lục D): page, region, nhãn của từng người, mô tả.
3. **So sánh với Guideline hiện có:** đã có rule chưa?
   - Có rule → áp dụng, cập nhật cả hai bản nhãn cho nhất quán.
   - Chưa có rule → chuyển bước 4.
4. **Đưa Khoa (Technical/Research Lead) quyết định**, kèm ưu/nhược mỗi phương án.
5. **Cập nhật Guideline** (thêm rule + ví dụ) và tăng version Guideline.
6. **Rà soát lại** các trang đã gán để áp rule mới (đảm bảo nhất quán toàn tập).

> **WHY phải làm chặt vậy:** Sự **nhất quán** giữa các annotator (inter-annotator agreement) chính là *trần chất lượng* của dataset. Nếu hai người gán khác nhau một cách hệ thống, model học được sự mâu thuẫn đó → không bao giờ tốt. Mỗi disagreement được giải quyết → Guideline mạnh lên → dataset đáng tin hơn. Đây là *nghiên cứu*, không phải cãi nhau.

---

## PART 9 — GOLDEN SAMPLE

### 9.1. Golden Sample là gì? Tại sao phải có?

**Là gì.** Một tập nhỏ (~5–10 trang `[RECO]`) *đại diện các ca khó*, được **cả 3 người gán độc lập**, rồi *đối chiếu* để (a) phát hiện chỗ Guideline mơ hồ, (b) chốt rule, (c) tạo **Annotation Guideline v1**.

**Tại sao phải có / tại sao KHÔNG annotate hàng loạt ngay:**
- Nếu gán 40 trang trước khi kiểm sự đồng thuận, và phát hiện taxonomy mơ hồ ở trang 35 → phải **làm lại 34 trang**. Lãng phí khổng lồ.
- Golden Sample là "phép thử guideline" giá rẻ: sai thì chỉ mất 5–10 trang.
- Nó cũng là **bộ chuẩn vàng** để về sau kiểm tra annotator mới có gán đúng "gu" của nhóm không.

### 9.2. Quy trình Golden Sample

```
[1] Chọn 5–10 trang phủ đủ ca khó (theo Part 4.2): inline-math, bảng-có-công-thức,
    caption, figure-cạnh-text, display-math phức tạp, công thức hóa lồng text.
[2] Cả 3 người (Khoa, Nguyên, Đức) gán ĐỘC LẬP, KHÔNG bàn nhau.
[3] Xuất nhãn của 3 người, dựng Disagreement table (9.3).
[4] Họp: với mỗi bất đồng → chốt final decision + lý do.
[5] Viết Annotation Guideline v1 = taxonomy (Part 7) + mọi rule vừa chốt + ví dụ thật từ chính golden pages.
[6] Cả 3 gán lại golden pages theo v1 → phải trùng khớp gần như hoàn toàn (kiểm hội tụ).
```

### 9.3. Disagreement table (sau khi ĐÃ khớp box)

> Ba người gán box **độc lập** ⇒ không có "region chung" sẵn. Phải **khớp box trước** (9.4), rồi định danh cặp bằng `matched_id` — KHÔNG dùng `r03` như thể có sẵn.

| page | matched_id | box (hợp nhất) | Khoa | Nguyên | Đức | Final | Reason |
|------|-----------|----------------|------|--------|-----|-------|--------|
| p045 | m01 | [x,y,w,h] | Display-Math | Display-Math | Figure | **Display-Math** | rule 7.1 |
| p045 | Đức | [x,y,w,h] | (sót) | (sót) | Text | **Text** | 2 người sót ⇒ nhắc self-check |

### 9.4. Đo đồng thuận ĐÚNG cách + từ disagreement → Guideline v1

**Quy trình đo (3 bước):**
1. **Khớp vùng (region matching).** Với mỗi cặp annotator, ghép box A↔B bằng **IoU ≥ 0.5** (greedy: cặp IoU cao nhất trước). Box không ghép = *vùng lệch định vị* (một người thấy, người kia không).
2. **Tách hai loại đồng thuận:**
   - **Localization agreement** = (số vùng ghép) / (số vùng hợp — union). Có cùng thấy một vùng ở đâu không.
   - **Label agreement** = (số vùng ghép **và** cùng nhãn) / (số vùng ghép). Có gọi tên giống nhau không.
3. **Đọc số:** localization thấp ⇒ guideline **cách vẽ box** chưa rõ; label thấp ⇒ **ranh giới lớp** chưa rõ. Hai bệnh, hai thuốc.

> "% trùng nhãn" thô **không trừ may rủi** — với 3 người nên thêm **Fleiss' kappa** trên nhãn của *các vùng đã ghép*. Pilot: báo **cả** raw agreement lẫn kappa.

**Script tối thiểu:**
```python
def iou(a,b):
    ax,ay,aw,ah=a; bx,by,bw,bh=b
    x1,y1=max(ax,bx),max(ay,by); x2,y2=min(ax+aw,bx+bw),min(ay+ah,by+bh)
    inter=max(0,x2-x1)*max(0,y2-y1); union=aw*ah+bw*bh-inter
    return inter/union if union else 0.0

def match(A,B,thr=0.5):
    pairs=sorted([(i,j,iou(A[i][0],B[j][0])) for i in range(len(A)) for j in range(len(B))],
                 key=lambda t:-t[2])
    uA,uB,m=set(),set(),[]
    for i,j,v in pairs:
        if v>=thr and i not in uA and j not in uB: m.append((i,j)); uA.add(i); uB.add(j)
    return m, [i for i in range(len(A)) if i not in uA], [j for j in range(len(B)) if j not in uB]

def agreement(A,B,thr=0.5):
    m,oA,oB=match(A,B,thr); union=len(m)+len(oA)+len(oB)
    loc=len(m)/union if union else 1.0
    same=sum(1 for i,j in m if A[i][1]==B[j][1])
    lab=same/len(m) if m else 1.0
    return {"localization":round(loc,3),"label":round(lab,3),"n_union":union,"n_matched":len(m)}
```
- **Input:** hai list `(bbox=[x,y,w,h], label)` của hai annotator trên **cùng golden page**. Chạy cho mọi cặp trong {Khoa,Nguyên,Đức}, trung bình.
- **Expected (pilot):** trước v1 thường 0.6–0.8; **sau khi gán lại theo Guideline v1 mục tiêu ≥ 0.9 cả hai chỉ số**. Nếu < 0.9 ⇒ guideline còn lỗ hổng, lặp vòng.

**Cơ chế flag (làm rõ 8.2 bước 7):** tạo **một nhãn phụ `FLAG-ambiguous`** trong Label Studio; ca mơ hồ **vẽ box + gán FLAG** thay vì đoán. Cuối batch lọc mọi `FLAG-ambiguous` → đổ vào Disagreement log.

Mỗi dòng "Final + Reason" → **một rule mới** trong Guideline v1, kèm *ảnh ví dụ thật* từ golden pages.

**Definition of Done (Golden Sample):** Disagreement table (theo matched_id) + Guideline v1 (có ví dụ ảnh) + đo lại đạt localization & label ≥ 0.9 + Decision Record mọi rule đã chốt.


---

## PART 10 — LABEL STUDIO

> Công cụ gán nhãn layout `[OFFICIAL]`. Hướng dẫn cho người **chưa từng dùng**. `[IMPL]` các bước UI có thể đổi theo phiên bản — kiểm tra `labelstud.io/guide` bản hiện hành nếu lệch.

### 10.0. CANONICAL CATEGORY CONTRACT (ghim cứng category_id)

> **Vấn đề:** Label Studio đánh `category_id` theo thứ tự tạo nhãn ⇒ mỗi project một kiểu ⇒ AP/lớp, mapping model (16.5) và pipeline Team 2 **lệch âm thầm**. **Giải:** ghim một hợp đồng cố định, remap ngay sau export.

**`src/common/categories.py` — KHÔNG ai được đổi số này:**
```python
CATEGORY_MAP = {1:"Title", 2:"Text", 3:"Table", 4:"Figure", 5:"Inline-Math", 6:"Display-Math"}
NAME_TO_ID   = {v:k for k,v in CATEGORY_MAP.items()}
COCO_CATEGORIES = [{"id":i, "name":n} for i,n in CATEGORY_MAP.items()]
```

**Normalize NGAY sau export Label Studio (trước khi lưu vào `annotations/coco/`):**
```python
import json
from src.common.categories import CATEGORY_MAP, NAME_TO_ID, COCO_CATEGORIES
def normalize_categories(coco_in, coco_out):
    coco = json.load(open(coco_in, encoding="utf-8"))
    old = {c["id"]: c["name"] for c in coco["categories"]}
    for a in coco["annotations"]:
        name = old[a["category_id"]]
        if name not in NAME_TO_ID: raise ValueError(f"Nhãn '{name}' ngoài taxonomy 6 lớp")
        a["category_id"] = NAME_TO_ID[name]         # remap về id chuẩn
    coco["categories"] = COCO_CATEGORIES            # đóng đinh bảng categories
    json.dump(coco, open(coco_out,"w",encoding="utf-8"), ensure_ascii=False, indent=2)
```
> Sau bước này, **Display-Math LUÔN = 6** ở mọi file, mọi máy, mọi phiên bản. `validate_coco` (12.5) có thêm kiểm hợp đồng (id==name) — xem 12.5.

### 10.1. Cài đặt

```bash
# Cách đơn giản nhất cho nhóm: cài bằng pip trong môi trường ảo
python -m venv ls-env
source ls-env/bin/activate          # Windows: ls-env\Scripts\activate
pip install -U label-studio
label-studio start                  # mở http://localhost:8080
```
- **Ưu:** cài nhanh, chạy local, dữ liệu ở máy mình (hợp với bản quyền SGK).
- **Nhược:** mỗi người một instance → phải gộp export thủ công. `[RECO]` Với nhóm 3 người pilot, chấp nhận local + gộp tay; chưa cần dựng server chung. (Nếu về sau scale, cân nhắc Label Studio trên 1 server chung — OPEN DECISION.)

### 10.2. Mở & tạo project
1. Đăng ký user local, đăng nhập.
2. **Create Project** → đặt tên `viet12-pilot-layout`.
3. Vào **Settings → Labeling Interface**, dán cấu hình labels (10.3).
4. **Data Import** → import ảnh từ `clean/images/` (xem 10.4).

### 10.3. Tạo labels (cấu hình 6 lớp)

Dán XML sau vào Labeling Interface (dùng `RectangleLabels` cho bounding box):

```xml
<View>
  <Image name="image" value="$image"/>
  <RectangleLabels name="label" toName="image">
    <Label value="Title"        background="#e6194b"/>
    <Label value="Text"         background="#3cb44b"/>
    <Label value="Table"        background="#4363d8"/>
    <Label value="Figure"       background="#f58231"/>
    <Label value="Inline-Math"  background="#911eb4"/>
    <Label value="Display-Math" background="#42d4f4"/>
  </RectangleLabels>
</View>
```
- **WHY màu khác nhau:** dễ soi peer review bằng mắt.
- **WHY đúng 6 nhãn:** khớp taxonomy `[OFFICIAL]`; không thêm nhãn lạ.

### 10.4. Import image
- Kéo-thả ảnh, hoặc trỏ tới thư mục local. `[IMPL]` Với ảnh local, có thể cần bật biến môi trường `LOCAL_FILES_SERVING_ENABLED=1` trước khi start (theo tài liệu Label Studio hiện hành) để phục vụ file từ ổ đĩa.
- **Lưu ý quan trọng:** export của Label Studio **chỉ chứa nhãn, không kèm ảnh gốc**. Phải tự giữ ảnh song song (thư mục `clean/images/`).

### 10.5. Annotation & Export → COCO (cách ĐÚNG)

- Gán nhãn theo Part 8.
- **Export → COCO.** File này **đã ở pixel** với `bbox=[x,y,w,h]`. **KHÔNG** tự đổi toạ độ.
- Chạy `normalize_categories()` (§10.0) → lưu `annotations/coco/instances_clean.json`.
- Chạy `validate_coco()` (12.5) → phải PASS trước khi coi là xong.
- `[IMPL]` Một số bản **bỏ ảnh không có annotation** khi export; nếu pilot có trang "không đối tượng" mà vẫn muốn giữ → kiểm file export hoặc bổ sung thủ công.

### 10.6. Định dạng COCO — giải thích cho người mới

COCO là chuẩn phổ biến cho object detection. Một file COCO gồm 3 mảng chính:

```json
{
  "images": [
    {"id": 1, "file_name": "math12_kntt_ch01_p045_clean.png",
     "width": 1654, "height": 2339}
  ],
  "categories": [
    {"id": 1, "name": "Title"},
    {"id": 2, "name": "Text"},
    {"id": 6, "name": "Display-Math"}
  ],
  "annotations": [
    {"id": 101, "image_id": 1, "category_id": 6,
     "bbox": [820, 1180, 300, 90], "area": 27000, "iscrowd": 0}
  ]
}
```

Giải thích từng field:
- `images[].id`: ID ảnh; `file_name`,`width`,`height`: thông tin ảnh.
- `categories[].id/name`: bảng ánh xạ số ↔ tên lớp.
- `annotations[].image_id`: box này thuộc ảnh nào.
- `annotations[].category_id`: box này lớp nào (trỏ tới categories).
- `annotations[].bbox`: `[x, y, width, height]` — **góc trên-trái** + rộng + cao (đơn vị pixel). *(Lưu ý: COCO dùng x,y,w,h — khác một số format dùng x1,y1,x2,y2.)*
- `annotations[].area`: diện tích (= w×h với bbox chữ nhật).
- `iscrowd`: 0 cho đối tượng đơn (ta luôn 0 ở pilot).
- `segmentation`: (tuỳ chọn) đa giác/mask. Phase 1 dùng bbox → có thể để rỗng hoặc bỏ. 🔶 **OPEN DECISION:** có cần segmentation không? (Khuyến nghị: **không** ở pilot; RT-DocLayout có thể xuất mask nhưng ta đánh giá ở mức bbox cho đơn giản.)

### 10.7. Mapping Label Studio → COCO

- Mỗi ảnh Label Studio → một entry `images`.
- Mỗi rectangle → một `annotation`. ⚠️ **Dùng COCO export sẵn có (đã ra pixel), KHÔNG tự đổi toạ độ.** Chỉ khi buộc phải đọc JSON thô của Label Studio (toạ độ **phần trăm 0–100**, kèm `original_width/height`): `x_px = value.x/100 * original_width` — tương tự y,w,h, **phải chia 100 trước** (bỏ `/100` là lỗi phóng đại 100× → box văng khỏi ảnh). Sanity check: vẽ lại 3 box đầu lên ảnh gốc, phải ôm đúng.
- Mỗi `value` nhãn → một `category`.
- **Kiểm tra:** mở file COCO bằng script validate (Part 12.5) trước khi coi là xong.

**Definition of Done cho Label Studio pilot:** có `annotations/coco/instances_clean.json` hợp lệ (validate pass), số ảnh khớp `pages.csv`, mọi bbox nằm trong ảnh, categories đúng 6 lớp.

---

## PART 11 — MATHEMATICAL GROUND TRUTH

> `[OFFICIAL]` Với vùng **Display-Math**, dùng Mathpix (hoặc gõ tay) lấy **LaTeX chuẩn 100%**, đảm bảo *render khớp hoàn toàn* với SGK. Đây là phần khó và quan trọng nhất của dataset math.

### 11.1. Vì sao layout annotation CHƯA ĐỦ?

Box layout chỉ nói *"ở đây có một công thức"* — nó **không** nói công thức đó *là gì*. Để chấm điểm UniMERNet (model đọc công thức → LaTeX), ta cần biết **đáp án LaTeX đúng**. Không có LaTeX GT thì không thể tính CDM. → Display-Math cần **hai lớp GT**: (1) box (layout) + (2) chuỗi LaTeX (nội dung).

### 11.2. Vì sao OCR text ≠ Mathematical Expression Recognition?

- OCR trả **chuỗi ký tự tuyến tính**: nhìn "x²" có thể ra "x2".
- MER phải nắm **cấu trúc 2 chiều**: `x^{2}` (mũ) khác `x_{2}` (chỉ số) khác `x2` (nhân). Cùng một ảnh, ba nghĩa khác nhau — chỉ MER phân biệt được nhờ *vị trí không gian* của "2".
- Vì thế GT cho math là **LaTeX** (mã có cấu trúc), không phải text phẳng.

### 11.3. Quy trình tạo Math GT (7 bước)

```
[1] Formula image  : cắt crop Display-Math từ ảnh clean (theo box đã gán)
[2] Transcription  : Mathpix API/app HOẶC gõ tay → mã LaTeX thô
[3] LaTeX          : chuẩn hoá mã (bỏ ký tự thừa, thống nhất quy ước)
[4] Render         : biên dịch LaTeX → ảnh (KaTeX/MathJax/matplotlib mathtext)
[5] Compare        : đặt ảnh render cạnh crop gốc, so bằng mắt
[6] Correction     : nếu lệch → sửa LaTeX, quay lại [4]
[7] Accept         : khi render KHỚP hoàn toàn → chốt GT
```

- **Input:** ảnh crop công thức. **Output:** một dòng `{formula_id, image, latex}` trong `formulas/latex/*.jsonl`.
- **Tiêu chuẩn nghiệm thu `[OFFICIAL]`:** *"rendered LaTeX must visually match source formula"* — ảnh dựng lại từ LaTeX phải *trông giống* công thức trong SGK (cùng cấu trúc, cùng ký hiệu). Không yêu cầu giống *font*, nhưng phải giống *nội dung toán học*.

### 11.4. Vì sao dùng ảnh render để so, không so chuỗi?

Vì **một công thức có nhiều cách viết LaTeX** (VD `\frac{a}{b}` vs `a/b`; `\left(...\right)` vs `(...)`). So chuỗi sẽ báo "khác" dù toán học giống hệt. So *ảnh render* mới phản ánh đúng "công thức có đúng không". (Đây cũng chính là triết lý của metric CDM ở Part 16.)

### 11.5. Bảng lỗi phiên âm LaTeX hay gặp — phải soi kỹ

| Loại | Sai điển hình | Đúng | Cách phát hiện |
|------|---------------|------|----------------|
| **Superscript (mũ)** | `x2` | `x^{2}` | so vị trí "2" cao/thấp |
| **Subscript (chỉ số)** | `x2` | `x_{2}` | "2" nằm thấp |
| **Fraction (phân số)** | `a/b` khi SGK viết dạng chồng | `\frac{a}{b}` | có gạch ngang chồng tầng? |
| **Square root (căn)** | thiếu dấu căn ngoài cùng | `\sqrt{...}`, `\sqrt[n]{...}` | có dấu căn phủ hết biểu thức? |
| **Greek letters** | `a` thay cho `\alpha` | `\alpha,\beta,\pi,\Delta` | ký hiệu Hy Lạp? |
| **Operators** | `x` (chữ ex) thay `\times` | `\times,\cdot,\pm,\leq,\geq` | dấu nhân/±/≤? |
| **Matrix** | viết thành dòng | `\begin{matrix}...\end{matrix}` | có hàng×cột trong ngoặc? |
| **Limits (giới hạn)** | mất chỉ số dưới lim | `\lim_{x\to 0}` | dưới "lim" có điều kiện? |
| **Integral (tích phân)** | mất cận | `\int_{a}^{b}` | có cận trên/dưới? |
| **Summation (tổng)** | mất cận | `\sum_{i=1}^{n}` | có cận? |
| **Parentheses (ngoặc)** | ngoặc không tự co giãn | `\left( \right)` | ngoặc bao trọn phân số cao? |
| **Chemical notation** | `H2O` | `H_{2}O` / dùng `\ce{H2O}` (mhchem) | chỉ số dưới trong công thức hóa |
| **Mixed text/math** | dịch cả chữ tiếng Việt sang ký hiệu | `\text{...}` cho phần chữ | có chữ Việt trong công thức? |

> 🔶 **OPEN DECISION (hóa học):** Chuẩn hoá công thức/phương trình hóa học ra sao — dùng `\ce{}` (gói mhchem) hay LaTeX thuần với `_{}`/`^{}`? UniMERNet chủ yếu huấn luyện cho *toán*; công thức hóa có thể ngoài phân bố của nó. Cần nhóm chốt: (a) coi công thức hóa là một *lớp GT riêng*, (b) chuẩn LaTeX cho hóa, (c) có đưa vào chấm CDM không. **Đây đồng thời là một ca thử H2** (công thức hóa lồng trong văn bản mà đề tài nêu là điểm model dễ "bó tay").

### 11.6. Mathpix vs gõ tay `[IMPL]`
- **Mathpix:** nhanh, chính xác cao cho công thức in — nhưng là **dịch vụ có phí/giới hạn API**. 🔶 **OPEN DECISION:** nhóm có tài khoản/ngân sách Mathpix không? Nếu không → gõ tay cho pilot (30–50 trang, số Display-Math vừa phải, khả thi).
- **Gõ tay:** chậm hơn nhưng miễn phí và *dạy* annotator hiểu cấu trúc công thức (giá trị đào tạo cao cho Đức). `[RECO]` Với pilot: **Mathpix để phác nhanh + người sửa lại**, hoặc gõ tay hoàn toàn nếu chưa có Mathpix.
- Dù cách nào, **bước [5] compare-render là bắt buộc** — không tin tuyệt đối Mathpix.

### 11.7. Render LaTeX — engine THẬT (chạy được cho ma trận & hoá học)

> Bước [4]–[5] và validate LaTeX (12.5) đều cần render. **matplotlib mathtext KHÔNG đọc** `\ce{}`, `matrix`, `align` — đúng các ca khó đề tài nhắm ⇒ dùng **TeX thật** làm công cụ nghiệm thu; mathtext chỉ cho ca đơn giản khi máy chưa có TeX.

```python
import os, subprocess, tempfile, shutil
PREAMBLE = r"""\documentclass[preview,border=2pt]{standalone}
\usepackage{amsmath,amssymb,amsfonts}
\usepackage[version=4]{mhchem}
\begin{document}
$%s$
\end{document}"""

def render_latex(latex, out_png="/tmp/_r.png"):
    """LaTeX -> PNG bằng TeX + dvipng. True nếu render được; False nếu lỗi (KHÔNG raise)."""
    if shutil.which("latex") is None or shutil.which("dvipng") is None:
        raise EnvironmentError("Thiếu TeX/dvipng — xem 5.0(a)")
    work = tempfile.mkdtemp()
    try:
        tex = os.path.join(work, "f.tex")
        open(tex, "w", encoding="utf-8").write(PREAMBLE % latex)
        r = subprocess.run(["latex","-interaction=nonstopmode","-halt-on-error",
                            "-output-directory", work, tex], capture_output=True, text=True)
        if r.returncode != 0 or not os.path.exists(os.path.join(work,"f.dvi")): return False
        r2 = subprocess.run(["dvipng","-D","200","-T","tight","-bg","White",
                             "-o", out_png, os.path.join(work,"f.dvi")], capture_output=True, text=True)
        return r2.returncode == 0 and os.path.exists(out_png)
    finally:
        shutil.rmtree(work, ignore_errors=True)
```

**Bước [4]–[5] nay chạy được:** `render_latex(gt_latex, "tmp/render.png")` → nếu `False` thì LaTeX GT sai cú pháp (sửa); nếu `True` thì mở `formulas/crops/{formula_uid}.png` cạnh `tmp/render.png` so bằng mắt. Ràng buộc cứng: **render được** là điều kiện tối thiểu trước khi so mắt.

**Validate LaTeX hàng loạt (thay "matplotlib mathtext" ở 12.5):**
```python
def validate_all_latex(jsonl_path, fail_log):
    import json
    fails = [json.loads(l)["formula_uid"] for l in open(jsonl_path, encoding="utf-8")
             if not render_latex(json.loads(l)["latex"])]
    open(fail_log, "w").write("\n".join(fails)); return fails   # rỗng = mọi LaTeX render được
```

**Definition of Done cho một Math GT:** có crop + LaTeX + ảnh render; `render_latex(gt)==True` **và** render *khớp thị giác* với crop (người thứ hai xác nhận); ghi vào `formulas/latex/*.jsonl` mỗi dòng `{formula_uid, latex}` — khoá **`formula_uid`** (§15.2) truy ngược được về page & box.

---

## PART 12 — QUALITY CONTROL FOR DATASET

> Không có QA thì dataset "trông xong" nhưng đầy lỗi ngầm → mọi metric về sau vô nghĩa.

### 12.1. Bốn tầng QA

```
Level 1 — Self Check       (annotator tự rà, Part 8.4)
Level 2 — Peer Review      (người khác soi, Part 8.3)
Level 3 — Technical Review (Khoa + script validate tự động, 12.5)
Level 4 — Dataset Freeze   (đóng băng phiên bản, gắn tag Git, ghi VERSION)
```

**WHY 4 tầng:** lỗi lọt tầng 1 bị tầng 2 bắt; lỗi format/logic mà mắt người khó thấy thì script tầng 3 bắt; tầng 4 khoá lại để mọi experiment chạy trên *cùng một bản dataset bất biến* (nếu dataset cứ đổi thì không so sánh được kết quả).

### 12.2. Dataset Freeze là gì & tại sao cần?

**Freeze** = chốt một phiên bản dataset (VD `v0.1-pilot`), gắn Git tag, không sửa nữa. Mọi experiment ghi rõ chạy trên bản nào. **WHY:** nếu Khoa chạy baseline hôm nay trên 40 trang, mai Đức sửa 3 nhãn, thì con số hôm nay *không tái lập được* → nghiên cứu mất giá trị. Freeze bảo vệ tính so sánh được.

### 12.3. QA checklist (rà toàn tập trước freeze)

- [ ] **Missing pages** — mọi page trong `pages.csv` đều có ảnh & (nếu cần) nhãn?
- [ ] **Duplicate images** — không hai ảnh trùng nội dung/checksum?
- [ ] **Broken image** — mọi ảnh mở được, không hỏng?
- [ ] **Invalid bbox** — không bbox có w≤0 hoặc h≤0?
- [ ] **Bbox outside image** — mọi bbox nằm trong khung ảnh (x≥0, y≥0, x+w≤W, y+h≤H)?
- [ ] **Wrong class** — mọi category_id thuộc 6 lớp hợp lệ?
- [ ] **Empty annotation** — trang cần gán mà bị rỗng nhãn (do sót)?
- [ ] **Duplicate annotation** — hai box trùng khít cùng lớp (gán đúp)?
- [ ] **Missing formula** — mọi Display-Math đều có LaTeX GT?
- [ ] **Invalid LaTeX** — mọi LaTeX biên dịch được (render không lỗi)?
- [ ] **Wrong page mapping** — formula_id/region_id trỏ đúng page?
- [ ] **Inconsistent IDs** — không ID trùng hoặc lệch quy ước naming?

### 12.4. Ưu tiên tự động hoá `[RECO]`

Việc rà tay 40 trang còn được; 200 trang thì bất khả. Nên viết **script validate** sớm (dùng lại được khi scale). Đây là phần "engineering phục vụ nghiên cứu" — hợp lý, không biến đề tài thành dự án phần mềm thuần.

### 12.5. Pseudocode / Python validate COCO `[IMPL]`

```python
import json, os
from PIL import Image

VALID_CATS = {"Title","Text","Table","Figure","Inline-Math","Display-Math"}

def validate_coco(coco_path, images_dir):
    coco = json.load(open(coco_path, encoding="utf-8"))
    errors = []
    imgs = {im["id"]: im for im in coco["images"]}
    cats = {c["id"]: c["name"] for c in coco["categories"]}

    # 1. category hợp lệ
    for cid, name in cats.items():
        if name not in VALID_CATS:
            errors.append(f"[wrong-class] category {cid}='{name}' không thuộc taxonomy")

    # 2. ảnh mở được + không trùng
    seen = set()
    for im in coco["images"]:
        p = os.path.join(images_dir, im["file_name"])
        if not os.path.exists(p):
            errors.append(f"[missing-image] {im['file_name']}")
            continue
        if im["file_name"] in seen:
            errors.append(f"[duplicate-image] {im['file_name']}")
        seen.add(im["file_name"])
        try:
            Image.open(p).verify()
        except Exception:
            errors.append(f"[broken-image] {im['file_name']}")

    # 3. bbox hợp lệ + nằm trong ảnh
    for a in coco["annotations"]:
        x, y, w, h = a["bbox"]
        im = imgs.get(a["image_id"])
        if w <= 0 or h <= 0:
            errors.append(f"[invalid-bbox] ann {a['id']} w/h<=0")
        if im and (x < 0 or y < 0 or x + w > im["width"] or y + h > im["height"]):
            errors.append(f"[bbox-outside] ann {a['id']} tràn khỏi ảnh")
        if a["category_id"] not in cats:
            errors.append(f"[wrong-class] ann {a['id']} category_id lạ")

    return errors

# Dùng:
errs = validate_coco("annotations/coco/instances_clean.json", "clean/images")
print("PASS" if not errs else "\n".join(errs))
```

**Và một validate LaTeX riêng `[IMPL]`:** dùng `validate_all_latex()` (11.7) — render mỗi LaTeX bằng **TeX thật** (đọc được ma trận/hoá), ghi ra danh sách render lỗi để sửa. (Đừng dùng matplotlib mathtext làm công cụ nghiệm thu — nó báo lỗi giả trên công thức hợp lệ.)

**Bổ sung kiểm hợp đồng category vào `validate_coco` (§10.0):** thêm vào cuối hàm, trước `return errors`:
```python
    from src.common.categories import CATEGORY_MAP
    canon = {(i,n) for i,n in CATEGORY_MAP.items()}
    got   = {(c["id"], c["name"]) for c in coco["categories"]}
    if got != canon:
        errors.append(f"[category-contract] categories != chuẩn: {sorted(got)}")
```

**Definition of Done cho QA:** script validate chạy **PASS** trên `instances_clean.json`; mọi LaTeX render được; checklist 12.3 tick hết; dataset được freeze thành `v0.1-pilot` + Git tag + dòng trong VERSION.

---

## PART 13 — BASELINE MODEL CONCEPTS

> Giải thích cho người **chưa biết deep learning**. Mục tiêu: hiểu *đủ để dùng đúng và diễn giải kết quả*, không cần hiểu toán bên trong.

### 13.1. Các khái niệm nền

| Khái niệm | Giải thích đời thường | Trong đề tài |
|-----------|----------------------|--------------|
| **Model pretrained** | Một "bộ não" đã được người khác huấn luyện sẵn trên rất nhiều dữ liệu, ta *tải về dùng ngay*. | RT-DocLayout, UniMERNet tải weights có sẵn. |
| **Weights** | Các con số bên trong model, kết tinh những gì nó "học" được. | file `.pt`/`.pth`/checkpoint. |
| **Inference (suy luận)** | Cho model *dùng* để dự đoán trên dữ liệu mới. KHÔNG thay đổi model. | chạy model trên ảnh SGK để lấy box/LaTeX. |
| **Training (huấn luyện)** | *Dạy* model bằng dữ liệu có nhãn, model tự chỉnh weights. | Việc của **Team 2**, không phải Phase 1. |
| **Baseline** | Model dùng *nguyên bản*, làm mốc so sánh. | mốc để Team 2 vượt qua. |

### 13.2. Training khác Inference thế nào?
- **Training:** cần dữ liệu có nhãn, tốn GPU/thời gian, model *thay đổi*. Có rủi ro overfitting.
- **Inference:** chỉ *chạy tới*, model *bất biến*, nhanh, chỉ cần đủ RAM/VRAM để nạp model.
- Phase 1 **chỉ inference** `[OFFICIAL]` ("không cần train, chỉ gọi pre-trained weights").

### 13.3. Vì sao Phase 1 CHƯA fine-tune?
Vì câu hỏi nghiên cứu của Team 1 là: *"Model quốc tế, khi CHƯA chỉnh cho tiếng Việt, mạnh/yếu ở đâu?"* — muốn trả lời thì phải đo **bản nguyên gốc**. Fine-tune ngay sẽ *xoá mất* điểm baseline cần đo. Baseline này chính là con số Team 2 phải đánh bại → nếu không có nó, không chứng minh được Team 2 cải thiện.

### 13.4. RT-DocLayout — model làm gì?

> `[IMPL]` Thông tin dưới đây tổng hợp từ nguồn công khai; **phải kiểm lại checkpoint cụ thể** nhóm dùng (xem OPEN DECISION 13.6) và ghi version vào experiment log.

- **Nhiệm vụ:** Document Layout Analysis — phát hiện & phân loại vùng bố cục trên ảnh trang.
- **Input:** ảnh một trang tài liệu.
- **Output:** danh sách vùng, mỗi vùng có box (+ có thể mask + nhãn lớp + thứ tự đọc).
- **Bối cảnh (theo công bố công khai, arXiv 2606.23344):** RT-DocLayout là khung end-to-end xây trên bộ phát hiện **RT-DETR**, mô hình ~33M tham số, hợp nhất phân loại + phát hiện + phân đoạn mức pixel + dự đoán reading order trong một kiến trúc.
- **Model KHÔNG làm:** không đọc nội dung text (đó là OCR), không sinh LaTeX (đó là UniMERNet). Nó chỉ *khoanh vùng và dán nhãn*.
- **Vì sao chọn:** là một mô hình layout hiện đại, nhanh, đại diện cho "mô hình phương Tây/quốc tế" mà đề tài muốn đánh giá trên SGK tiếng Việt.

### 13.5. UniMERNet — model làm gì?

> Nguồn: opendatalab/UniMERNet (arXiv 2404.15254).

- **Nhiệm vụ:** Mathematical Expression Recognition — ảnh công thức → LaTeX.
- **Input:** ảnh **một công thức đã cắt** (không phải cả trang).
- **Output:** chuỗi LaTeX.
- **Bối cảnh:** UniMERNet là mạng encoder–decoder chuyên cho công thức, huấn luyện trên bộ UniMER-1M lớn, mạnh ở nhiều điều kiện thực tế (in đơn giản/phức tạp, ảnh chụp màn hình, viết tay).
- **Model KHÔNG làm:** không tự tìm công thức trên trang (ta phải *cắt sẵn* theo box Display-Math), không xử lý layout.
- **Vì sao chọn:** là baseline MER mạnh, mã nguồn mở, dùng được ngay; và chính nhóm tác giả cũng đề xuất metric CDM mà đề tài yêu cầu.

### 13.6. 🔶 OPEN DECISION — "RT-DocLayout" là bản nào?
Tài liệu nguồn chỉ ghi tên "RT-DocLayout". Trên thực tế có nhiều mô hình layout tên gần nhau (RT-DETR-based layout, PP-DocLayout của PaddleOCR, DocLayout-YOLO của opendatalab...). **Trước khi chạy baseline, nhóm phải chốt đúng repo/checkpoint**, kiểm ở trang chính thức của model, và ghi rõ: tên repo, commit/hash, file weights, tập nhãn native của model. Đây là điều kiện tiên quyết để làm Part 14 & mapping nhãn ở Part 16.

---

## PART 14 — RT-DocLayout PIPELINE

> `[IMPL]` Đây là khung thao tác chung cho một model layout; lệnh cụ thể **phụ thuộc repo được chốt ở 13.6**. Luôn đối chiếu README chính thức của checkpoint đang dùng.

### 14.1. Khung setup & inference (7 mục bắt buộc)

**Prerequisites**
- Python (kiểm bản repo yêu cầu), môi trường ảo riêng, GPU khuyến khích (CPU chạy được nhưng chậm).
- Ghi lại: phiên bản Python, CUDA, torch — vào experiment log.

**Installation** `[IMPL]`
```bash
python -m venv layout-env && source layout-env/bin/activate
# theo README chính thức của checkpoint đã chốt, ví dụ dạng:
pip install -r requirements.txt        # hoặc pip install <package>
```

**Model weights**
- Tải đúng checkpoint đã chốt (13.6). Ghi lại URL + hash. Đặt trong `baseline/rtdoclayout/weights/`.

**Input format**
- Ảnh trang từ `clean/images/` (và các variant). Kích thước theo yêu cầu model (thường model tự resize; kiểm README).

**Command (mẫu khung)** `[IMPL]`
```bash
python infer_layout.py \
  --weights baseline/rtdoclayout/weights/<ckpt> \
  --images  clean/images/ \
  --out     baseline/rtdoclayout/pred_clean.json \
  --save_vis baseline/rtdoclayout/vis_clean/
```

**Output format**
- Một JSON dạng: mỗi ảnh → list {box, score, class_native}. Lưu vào `baseline/rtdoclayout/pred_<variant>.json`.
- Ảnh visualize (box vẽ lên trang) trong `vis_<variant>/`.

**Visualization**
- Mở 5–10 ảnh vis: box có ôm đúng đối tượng không? Có phát hiện công thức/bảng không?

**Saving predictions**
- Chuẩn hoá prediction về đúng schema COCO-prediction (image_id, category_id, bbox, score) để Part 16 chấm được.

**Troubleshooting** → Part 25.

### 14.2. Khung 7Q cho bước "chạy RT-DocLayout"
- **WHY chạy:** để có *dự đoán layout của model quốc tế* trên SGK tiếng Việt — nguyên liệu so với GT.
- **Input:** ảnh trang (clean + noisy variants).
- **Output:** box + nhãn (theo tập nhãn *native* của model) + score.
- **Output dùng để làm gì:** đối chiếu với GT layout (COCO của ta) → tính IoU/mAP (Part 16).
- **Làm sao biết model chạy đúng:** (a) không crash, (b) số ảnh output = số ảnh input, (c) visualize hợp lý (box không loạn), (d) score phân bố hợp lý (không phải toàn 0 hoặc toàn 1).
- **Nếu output tệ thì kiểm gì:** sai checkpoint? ảnh đọc sai màu/kích thước? model resize làm mất công thức nhỏ? nhầm thứ tự x,y,w,h?
- **Liên kết với GT:** qua **category mapping** (Part 16.5) — vì nhãn native của model *chưa chắc* trùng tên 6 lớp của ta.

> ⚠️ **Điểm dễ sai chết người:** model có **tập nhãn riêng** (VD "text", "title", "figure", "table", "formula", "list"...). Không được giả định nó trùng 6 lớp của ta. Phải xây **bảng ánh xạ** (mapping) trước khi chấm điểm — nếu không, mAP sẽ thấp *giả tạo* chỉ vì tên lớp khác nhau, chứ không phải model dở.

---

## PART 15 — UniMERNet PIPELINE

> Dành cho Mathematical Expression Recognition. `[IMPL]` Kiểm README chính thức opendatalab/UniMERNet cho lệnh & phiên bản hiện hành.

### 15.1. Sơ đồ pipeline
```
formula crop (từ box Display-Math)
   → UniMERNet inference
   → predicted LaTeX
   → render (KaTeX/MathJax/matplotlib)
   → CDM (so với LaTeX GT)  [Part 16]
```

### 15.2. Chuẩn bị input — CROP CHUẨN (dùng chung GT & inference)

UniMERNet nhận **ảnh một công thức**. Phải cắt theo box Display-Math bằng **một hàm duy nhất** — cũng chính là crop mà Part 11 dùng để lập LaTeX GT ⇒ GT và pred **cùng ảnh, cùng `formula_uid`** ⇒ mới so được.

**`src/common/crop.py`:**
```python
import os, csv, json
from PIL import Image
from src.common.ids import page_uid_from_filename, formula_uid
from src.common.categories import NAME_TO_ID          # §10.0

PAD = 6   # lề an toàn (một con số DUY NHẤT, dùng khắp nơi): tránh cụt chỉ số/căn

def crop_display_math(coco_path, images_dir, out_crop_dir, manifest_csv):
    coco = json.load(open(coco_path, encoding="utf-8"))
    imgs = {im["id"]: im for im in coco["images"]}
    os.makedirs(out_crop_dir, exist_ok=True)
    disp = NAME_TO_ID["Display-Math"]
    per_image = {}
    for a in coco["annotations"]:
        if a["category_id"] == disp:
            per_image.setdefault(a["image_id"], []).append(a)
    rows = []
    for image_id, anns in per_image.items():
        im = imgs[image_id]; puid = page_uid_from_filename(im["file_name"])
        anns.sort(key=lambda a: (a["bbox"][1], a["bbox"][0]))     # reading order (y,x)
        img = Image.open(os.path.join(images_dir, im["file_name"])).convert("RGB")
        W, H = img.size
        for ff, a in enumerate(anns):
            x, y, w, h = map(int, a["bbox"])
            box = (max(0,x-PAD), max(0,y-PAD), min(W,x+w+PAD), min(H,y+h+PAD))
            fuid = formula_uid(puid, ff)
            img.crop(box).save(os.path.join(out_crop_dir, fuid + ".png"))
            rows.append({"formula_uid":fuid, "page_uid":puid, "image_id":image_id,
                         "file_name":im["file_name"], "ann_id":a["id"],
                         "bbox":json.dumps([x,y,w,h])})
    with open(manifest_csv, "w", newline="", encoding="utf-8") as f:
        wr = csv.DictWriter(f, fieldnames=list(rows[0].keys())); wr.writeheader(); wr.writerows(rows)
    return rows
```
- **Input:** `instances_clean.json` (đã normalize category §10.0) + thư mục ảnh.
- **Output:** `formulas/crops/{formula_uid}.png` + `formulas/formula_manifest.csv`.
- **Ràng buộc vàng:** Part 11 (GT LaTeX) chép từ **đúng** các crop này; Part 15 (inference) đọc **đúng** thư mục này. Không ai tự cắt kiểu khác.
- **VERIFY:** số crop == số box Display-Math; mở 5 crop, không cụt chỉ số/căn; mọi `formula_uid` duy nhất.

### 15.3. Inference — skeleton chạy được `[IMPL]`

> `[IMPL]` API `unimernet` **đổi theo version** — coi đây là *khung*, đối chiếu README `opendatalab/UniMERNet` bản đang cài; ghi version + weights hash vào log.
```python
import os, json, glob
# from unimernet.common.config import Config      # tên module tuỳ version
# import unimernet.tasks as tasks

def run_unimernet(crop_dir, out_jsonl, model, transform, device="cuda"):
    from PIL import Image
    import torch
    with open(out_jsonl, "w", encoding="utf-8") as f:
        for p in sorted(glob.glob(os.path.join(crop_dir, "*.png"))):
            fuid = os.path.splitext(os.path.basename(p))[0]      # = formula_uid (§15.2)
            img = transform(Image.open(p).convert("RGB")).unsqueeze(0).to(device)
            with torch.no_grad():
                pred = model.generate({"image": img})["pred_str"][0]   # API tuỳ version
            f.write(json.dumps({"formula_uid": fuid, "pred": pred}, ensure_ascii=False) + "\n")
```
- **Input:** `formulas/crops/*.png` (cùng crop với GT). **Output:** `baseline/unimernet/pred_{variant}.jsonl`, khoá **`formula_uid`** (KHÔNG dùng img_id tự chế).
- **VERIFY:** ca dễ `E=mc^2` phải đúng; `check_join` (16.10) với GT phải PASS.
- **Log version:** `pip show unimernet` + `sha1sum <weights>` → experiment log.

### 15.4. Khung 7Q
- **WHY:** đo năng lực đọc công thức của baseline trên công thức SGK tiếng Việt (đặc biệt khi có nhiễu/dấu thanh/hóa lồng).
- **Input:** crop công thức Display-Math.
- **Output:** LaTeX dự đoán.
- **Dùng để:** so với LaTeX GT bằng CDM (Part 16).
- **Biết chạy đúng:** render được LaTeX dự đoán ra ảnh không lỗi cú pháp; với vài crop dễ ($E=mc^2$) model phải đúng — nếu sai cả ca dễ → nghi cài đặt sai.
- **Output tệ thì kiểm:** crop có bị cụt/nghiêng? ảnh quá nhỏ? đúng weights? tiền xử lý ảnh (màu, chuẩn hoá) đúng như model yêu cầu?
- **Liên kết GT:** cùng `formula_id` giữa pred và GT.

> 🔶 **OPEN DECISION:** Có chạy UniMERNet cho **Inline-Math** không, hay chỉ Display-Math? `[OFFICIAL]` chỉ yêu cầu LaTeX cho Display-Math. Khuyến nghị `[RECO]`: Phase 1 chỉ chấm Display-Math; Inline-Math để pha sau. Chốt & ghi Decision Record.

---

## PART 16 — EVALUATION

> Chương sâu nhất. `[OFFICIAL]` Layout: **IoU/mAP**. Math: **CDM** (tuyệt đối không BLEU/Edit Distance). Mục tiêu: không chỉ *định nghĩa* metric mà biết *đọc* kết quả.

### 16.0. Vì sao evaluation quan trọng?
Evaluation biến câu "model có vẻ sai" thành **con số so sánh được, tái lập được, đưa vào bài báo được**. Không có evaluation đúng, mọi khẳng định của đề tài chỉ là cảm tính. Metric còn cho phép *so* Clean vs Noisy → đó chính là "độ suy giảm" mà đề tài đi tìm.

## PHẦN A — LAYOUT

### 16.1. IoU (Intersection over Union)

**Định nghĩa:** độ chồng lấn giữa box dự đoán và box GT.
$$\text{IoU} = \frac{\text{Area}(\text{pred} \cap \text{gt})}{\text{Area}(\text{pred} \cup \text{gt})}$$

**Trực giác:** hai box trùng hoàn toàn → IoU=1; không chạm nhau → IoU=0; chồng một nửa → ~0.5.

**Ví dụ bằng số.** GT box = (x=100,y=100,w=200,h=100) → vùng [100..300]×[100..200], diện tích 20000. Pred box = (150,120,200,100) → [150..350]×[120..220], diện tích 20000.
- Giao: x∈[150,300] (rộng 150), y∈[120,200] (cao 80) → 150×80 = 12000.
- Hợp: 20000 + 20000 − 12000 = 28000.
- IoU = 12000/28000 ≈ **0.43**.

**Dùng trong object detection:** một pred được coi là "trúng" (True Positive) nếu IoU với một GT cùng lớp ≥ **ngưỡng** (VD 0.5).

### 16.2. Precision, Recall, Confidence
- **Confidence (score):** độ tự tin model gán cho mỗi box (0–1).
- **Precision** = TP / (TP + FP) = "trong những gì model *nói có*, bao nhiêu đúng?" (đo *độ sạch*).
- **Recall** = TP / (TP + FN) = "trong những gì *thực sự có*, model tìm được bao nhiêu?" (đo *độ phủ*).
- **TP/FP/FN:** True Positive = pred trúng GT (đúng lớp, IoU≥ngưỡng). False Positive = pred không khớp GT nào (báo nhầm). False Negative = GT bị bỏ sót.

### 16.3. AP và mAP
- Hạ dần ngưỡng confidence → vẽ **đường Precision–Recall**. **AP** (Average Precision) = diện tích dưới đường đó cho **một lớp**.
- **mAP** (mean AP) = trung bình AP qua **tất cả các lớp**.
- **IoU threshold:** `mAP@0.5` (IoU≥0.5 là trúng) hay `mAP@[.5:.95]` (trung bình qua nhiều ngưỡng 0.5→0.95, khắt khe hơn — chuẩn COCO).
- 🔶 **OPEN DECISION:** báo cáo mAP@0.5, mAP@[.5:.95], hay cả hai? Khuyến nghị `[RECO]`: báo **cả hai** + AP *từng lớp* (để thấy lớp nào — VD Display-Math — kém nhất).

### 16.4. Cách ĐỌC kết quả layout (không chỉ định nghĩa)
- **mAP@0.5 = 0.71 nghĩa là gì?** Trung bình các lớp, model định vị+phân loại "khá" ở mức chồng lấn dễ. *Nhưng* con số gộp che giấu chi tiết → phải xem **AP từng lớp**.
- **AP(Text)=0.9 nhưng AP(Display-Math)=0.4** → model đọc layout chữ tốt, *nhưng yếu ở công thức* — đúng hướng giả thuyết H2! Đây mới là phát hiện có giá trị.
- **So Clean vs Captured:** mAP giảm từ 0.71 → 0.48 → đo được "độ suy giảm khi có nhiễu chụp". Đây chính là *evidence* cho đề tài.
- **Precision cao, Recall thấp** → model *bỏ sót* nhiều (thận trọng); ngược lại → *báo nhầm* nhiều.

### 16.5. ⚠️ Category mapping (cầu nối bắt buộc)
Nhãn native của RT-DocLayout ≠ 6 lớp của ta. **Trước khi tính mAP**, lập bảng ánh xạ:

| Nhãn model (native) | Lớp của ta |
|---------------------|-----------|
| title | Title |
| text / plain_text | Text |
| table | Table |
| figure / image | Figure |
| formula / isolate_formula | Display-Math |
| inline_formula (nếu có) | Inline-Math |
| caption / list / ... | (theo quyết định 7.3) |

🔶 **OPEN DECISION:** cách map các nhãn model *không có* tương ứng (VD "list", "abandon", "header/footer"). Bỏ qua? Gộp vào Text? Phải chốt & ghi, vì nó ảnh hưởng trực tiếp FP/FN.

## PHẦN B — MATHEMATICS

### 16.6. Vì sao KHÔNG dùng Edit Distance / BLEU cho công thức?
`[OFFICIAL]` "Tuyệt đối không dùng BLEU hay Edit Distance."
- **Vấn đề cốt lõi:** *một công thức có nhiều cách viết LaTeX đúng*. `\frac{a}{b}` và `\dfrac{a}{b}` render y hệt nhưng chuỗi khác → Edit Distance phạt oan. `(x)` vs `\left(x\right)` cũng vậy.
- **BLEU** (vốn cho dịch máy) so trùng n-gram token — không hiểu cấu trúc toán, nhạy với cách viết, sai lệch với đánh giá của con người.
- Hệ quả: hai model chất lượng như nhau có thể ra điểm Edit Distance rất khác chỉ vì *quy ước viết mã* → so sánh không công bằng.

### 16.7. CDM (Character Detection Matching) — ý tưởng
> Nguồn: "Image Over Text: Transforming Formula Recognition Evaluation with Character Detection Matching" (opendatalab, CVPR 2025); code tại github.com/opendatalab/UniMERNet/tree/main/cdm.

**Ý tưởng cốt lõi:** thay vì so *chuỗi LaTeX*, CDM **render cả LaTeX dự đoán và LaTeX GT thành ảnh**, rồi so khớp **ở mức ký tự trong không gian 2D**. "Ảnh hơn chữ" — vì ảnh phản ánh công thức *thực sự trông thế nào*, độc lập với cách viết mã.

**Bốn bước của CDM (theo bài báo):**
1. **Element Localization** — định vị từng ký tự/phần tử trên ảnh render (bounding box của mỗi ký hiệu).
2. **Element Region Matching** — ghép cặp phần tử pred↔GT bằng *bipartite matching* (trọng số theo độ giống ký tự + vị trí).
3. **Invalid Match Elimination** — loại các cặp ghép sai bằng kiểm tra nhất quán ký tự (token consistency) và quan hệ vị trí (RANSAC).
4. **Metric Calculation** — tính **F1-Score** và **ExpRate@CDM** (tỉ lệ công thức khớp *hoàn hảo*).

**Cách diễn giải score:**
- **F1 cao** → đa số ký tự khớp đúng vị trí (công thức gần đúng).
- **ExpRate@CDM** → tỉ lệ công thức *đúng hoàn toàn* — chỉ số khắt khe, sát cảm nhận con người "đúng hay sai".
- So Clean vs Captured qua CDM → đo *độ suy giảm MER* khi có nhiễu — evidence trực tiếp cho H1/H2.

**Hạn chế của CDM (phải nêu trung thực):** phụ thuộc chất lượng render (LaTeX GT phải biên dịch được — vòng lại Part 11); ký hiệu quá lạ (một số ký hiệu hóa học) có thể định vị kém; là metric ảnh nên tốn tính toán hơn so chuỗi.

### 16.8. Prototype CDM (nếu cần tự dựng/dùng) `[IMPL]`
`[RECO]` **Không viết lại CDM từ đầu.** Dùng bản chính thức của opendatalab (cùng nhóm tác giả UniMERNet). Định dạng input là JSON danh sách:
```json
[
  {"img_id": "p045_f002", "gt": "E = m c^{2}",       "pred": "E = mc^2"},
  {"img_id": "p112_f001", "gt": "\\int_{0}^{1} x\\,dx", "pred": "\\int_0^1 x dx"}
]
```
Kiến trúc dùng (không tự chế): **input** {gt, pred LaTeX} → **process** render→localize→match→eliminate → **output** F1 + ExpRate@CDM cho từng công thức và trung bình. **Validation:** với cặp gt==pred phải ra điểm tuyệt đối; với cặp render giống nhưng mã khác (`\frac{a}{b}` vs `\dfrac{a}{b}`) CDM vẫn phải *cao* (đây là bài test cho thấy vì sao CDM hơn Edit Distance).

`[IMPL]` Cài đặt CDM cần các phụ thuộc render (LaTeX/Node tuỳ bản) — theo README `cdm/` chính thức; ghi version.

### 16.9. Bảng tổng hợp metric
| Nhiệm vụ | Metric | Trả lời câu hỏi | Cấm dùng |
|----------|--------|-----------------|----------|
| Layout | IoU, mAP@0.5, mAP@[.5:.95], AP/lớp | định vị+phân loại vùng đúng đến đâu | — |
| Math | CDM (F1, ExpRate@CDM) | công thức đọc đúng đến đâu | BLEU, Edit Distance `[OFFICIAL]` |

### 16.10. Kiểm KHOÁ GHÉP + sanity check trước khi chấm

**Kiểm khoá ghép GT↔pred (bịt lỗi CDM so nhầm cặp):**
```python
def check_join(gt_jsonl, pred_jsonl):
    import json
    gt   = {json.loads(l)["formula_uid"] for l in open(gt_jsonl,  encoding="utf-8")}
    pred = {json.loads(l)["formula_uid"] for l in open(pred_jsonl, encoding="utf-8")}
    assert gt == pred, f"Lệch khoá! chỉ-GT={gt-pred} | chỉ-PRED={pred-gt}"
    return len(gt)
```
Vì GT và pred **dùng chung `formula_uid`** (§15.2), tập khoá phải **bằng nhau tuyệt đối**. Lệch = pipeline sai ⇒ **dừng, sửa, đừng tin điểm CDM**.

**Sanity check (biến 16.8 thành bước chạy) — làm TRƯỚC mọi lần chấm:**
- Layout: nạp chính GT làm prediction (score=1) → mAP ≈ 1.0. Không đạt ⇒ pipeline chấm/định dạng sai (nghi **category mapping** trước).
- CDM: cặp `gt==pred` → điểm **tối đa**; cặp render giống mã khác (`\frac{a}{b}` vs `\dfrac{a}{b}`) → CDM vẫn **cao** (đây là lý do CDM > Edit Distance).

**Glossary:** *homography* = phép phối cảnh 4 điểm (box vuông → tứ giác, lấy bbox ngoài); *bipartite matching* = ghép cặp 1–1 GT↔pred tối ưu; *RANSAC* = loại điểm ghép lạc bằng đồng thuận đa số; *reading order* = sort (y_top, x_left).

### 16.11. Đọc kết quả có kỷ luật `[RECO]`

- Tính mAP/AP bằng **pycocotools**, **không tự tính** diện tích P–R (tránh lệch nội suy 101 điểm).
- **Verify mapping model TRƯỚC khi map:** bảng 16.5 chỉ là **minh hoạ**. Chạy model 2–3 ảnh, **in tập nhãn native thật**, rồi mới lập mapping từ nhãn *thật* → 6 lớp; ghi Decision Record cho nhãn không có tương ứng (bỏ qua hay gộp Text — ảnh hưởng FP/FN, cố định trước khi chấm).
- **Ngưỡng "suy giảm nghiêm trọng" chưa chính thức** ⇒ research decision cần thầy chốt (Question Q10). Tạm `[RECO]`: *đáng chú ý* nếu giảm tương đối ≥ 20% & nhất quán ≥2 điều kiện. Mẫu câu an toàn: "trên pilot purposive 40 trang, mAP giảm X→Y, **gợi ý (chưa khẳng định)** H2." **Cấm** "chứng minh rằng". Purposive ⇒ **không** suy ra tỉ lệ lỗi toàn sách.


---

## PART 17 — EXPERIMENT DESIGN

### 17.1. Experiment matrix
`[OFFICIAL]` 3 điều kiện dữ liệu × 2 model:

| | RT-DocLayout (layout) | UniMERNet (math) |
|---|----------------------|------------------|
| **Clean** | EXP-P1-001 | EXP-P1-004 |
| **Scanned** | EXP-P1-002 | EXP-P1-005 |
| **Captured/Augmented** | EXP-P1-003 | EXP-P1-006 |

> Pilot 14 ngày: tối thiểu chạy **Clean + 1 điều kiện nhiễu** cho *mỗi* model (4 experiment); đủ thời gian thì phủ hết 6.

### 17.2. Experiment ID & những gì phải lưu
Mỗi experiment (VD `EXP-P1-003`) lưu **đủ để tái lập**:
- `dataset_version` (VD v0.1-pilot) và variant (clean/scanned/...)
- `model` + `checkpoint hash/version`
- `config` (ngưỡng score, kích thước input, mapping nhãn dùng)
- `seed`
- `timestamp`
- `metrics` (mAP@0.5, mAP@[.5:.95], AP/lớp, hoặc CDM F1/ExpRate)
- `notes` (bất thường, quan sát)

### 17.3. Reproducibility (tái lập) — vì sao sống còn
Một experiment *không tái lập được* thì **không phải bằng chứng khoa học** — chỉ là giai thoại. Để tái lập: dataset đã freeze (Part 12), seed cố định, config ghi lại, môi trường ghi lại (Python/torch/CUDA), lệnh ghi lại. Quy tắc vàng: *"Nếu Đức nghỉ, người khác đọc log có chạy lại ra đúng số không?"* Nếu không → log chưa đủ.

**Definition of Done cho một experiment:** có dòng đầy đủ trong Experiment Log (Phụ lục E) + file kết quả trong `evaluation/` + đủ thông tin để người khác tái lập.

---

## PART 18 — ERROR ANALYSIS

> `[OFFICIAL]` yêu cầu *định lượng* (giảm bao nhiêu %) **và** *định tính* (ví dụ sai dấu ngã/hỏi, "bó tay" trước công thức hóa lồng text). Đây là phần biến "số" thành "hiểu biết nghiên cứu".

### 18.1. Error analysis khác metric thế nào?
- **Metric** trả lời *bao nhiêu* (mAP=0.71, CDM F1=0.65).
- **Error analysis** trả lời *tại sao, sai ở đâu, sai kiểu gì* — nội dung làm nên đóng góp khoa học.

### 18.2. Vì sao "mAP = 0.71" chưa phải nghiên cứu sâu?
Con số 0.71 không cho biết: model *sai lớp nào*? sai vì *bỏ sót* hay *báo nhầm*? có phải công thức luôn bị nhầm thành Figure? dấu thanh có phải thủ phạm? Không mở "hộp đen" ra soi thì không rút được bài học cho Team 2, và bài báo không có gì để kể ngoài một con số.

### 18.3. Taxonomy lỗi

**Layout errors:**
- **Missed detection** (bỏ sót vùng — FN)
- **Wrong class** (định vị đúng nhưng nhầm lớp; VD Display-Math → Figure)
- **Poor localization** (đúng lớp nhưng box lệch, IoU thấp)
- **Table/Figure confusion**
- **Text/Math confusion** (đặc biệt Inline-Math bị nuốt vào Text) — **lõi H2**
- **Over-segmentation / Merge** (cắt vụn 1 vùng, hoặc gộp nhiều vùng thành 1)

**Math (MER) errors:**
- **Symbol confusion** (nhầm ký hiệu; VD `\times` ↔ `x`)
- **Superscript / Subscript error** (mũ/chỉ số sai vị trí)
- **Fraction error** (mất/nhầm phân số)
- **Structure error** (sai cấu trúc: căn, ma trận, giới hạn)
- **Vietnamese text error** (sai chữ Việt trong `\text{}`, **sai dấu thanh** — lõi H1)
- **Nested expression error** (biểu thức lồng nhiều tầng)

### 18.4. Error log (mẫu — Phụ lục F)
| exp_id | sample | task | error_type | severity | mô tả | ảnh ví dụ |
|--------|--------|------|-----------|----------|-------|-----------|
| EXP-P1-003 | p045 r03 | layout | Text/Math confusion | cao | Inline-Math bị gộp vào Text | vis_.../p045.png |
| EXP-P1-006 | p112 f001 | math | Vietnamese/dấu thanh | cao | "hỏi" đọc thành "hoi" | crops/p112_f001.png |

### 18.5. Định lượng + Định tính + Ví dụ đại diện
- **Quantitative:** đếm tần suất mỗi error_type; tính % suy giảm Clean→Noisy; bảng theo lớp.
- **Qualitative:** mô tả *bản chất* lỗi (VD "model bỏ dấu ngã khi ảnh nén mạnh").
- **Representative examples:** chọn ví dụ *đại diện cho một loại lỗi phổ biến hoặc thú vị*, không phải ví dụ ngẫu nhiên/gây sốc.

### 18.6. Cách chọn ví dụ có giá trị nghiên cứu
Một ví dụ tốt: (a) minh hoạ **một loại lỗi hệ thống** (lặp lại nhiều mẫu), (b) liên quan trực tiếp **giả thuyết** (dấu thanh/đan xen/hóa lồng text), (c) *rõ ràng* để người đọc bài báo hiểu ngay. Tránh: ví dụ lỗi do *ta gán GT sai* (phải loại trước), hay ca cực hiếm không đại diện.

> ⚠️ **Bẫy (Part 26 #7):** đừng chỉ chọn ví dụ *ủng hộ* giả thuyết. Nếu có ca model làm *tốt* bất ngờ trên tiếng Việt, cũng phải báo — đó là error analysis trung thực.

**Definition of Done cho Error Analysis v0:** taxonomy lỗi + bảng tần suất định lượng + ≥1 ví dụ đại diện mỗi loại lỗi chính + phần định tính liên hệ H1/H2 + ghi rõ ca *inconclusive*.

### 18.7. Mã lỗi cố định + tách lỗi-model vs lỗi-GT `[RECO]`

**Mã lỗi cố định (để 3 người đếm cộng được):**
- Layout: **L1** Missed · **L2** Wrong-class · **L3** Poor-localization · **L4** Table/Figure · **L5** Text/Math · **L6** Over/Merge.
- Math: **M1** Symbol · **M2** Sup/Sub · **M3** Fraction · **M4** Structure · **M5** VN-diacritics · **M6** Nested.
→ Mọi entry error log dùng đúng mã này.

**Error triage (bắt buộc trước khi tính vào thống kê):** với mỗi lỗi "cao", **mở GT của chính ca đó** hỏi "GT có đúng không?". GT sai → sửa GT, ghi vào QA, **không** tính là lỗi model. Chỉ lỗi *đã xác nhận GT đúng* mới vào bảng định lượng và mới được dùng làm ví dụ trong báo cáo. (Nếu không, ta đổ lỗi model cho lỗi mình gán → kết luận sai lệch.)


---

## PART 19 — RESEARCH NOTEBOOK

### 19.1. Vì sao ghi NGAY trong lúc làm, không đợi cuối sprint?
Trí nhớ phai nhanh: 3 ngày sau không nhớ "vì sao EXP-002 dùng ngưỡng 0.4". Ghi ngay giữ được *bối cảnh* và *lý do* — thứ quý nhất và dễ mất nhất. Cuối sprint mới ghi = bịa lại = sai.

### 19.2. Template ghi chép (mỗi experiment/ngày một entry — Phụ lục G)
```
Experiment ID   : EXP-P1-003
Date            : YYYY-MM-DD
Research Question: (câu hỏi cụ thể entry này nhắm tới)
Hypothesis      : (dự đoán)
Dataset         : v0.1-pilot / variant=captured
Model           : RT-DocLayout <ckpt-hash>
Config          : score_thr=0.3, mapping=v1, input=orig
Metrics         : mAP@0.5=..., mAP@[.5:.95]=..., AP/lớp=...
Result          : (số + bảng ngắn)
Observation     : (thấy gì bất thường)
Error           : (loại lỗi nổi bật)
Interpretation  : (nghĩa là gì với H1/H2 — thận trọng, không kết luận mạnh)
Next Action     : (làm gì tiếp)
```
Mỗi thành viên giữ notebook riêng (Đức, Nguyên) + một notebook chung của nhóm cho các entry experiment chính.

---

## PART 20 — GIT / REPOSITORY ORGANIZATION

### 20.1. Cấu trúc repo đề xuất `[RECO]`
```
viet12-team1/
├── README.md                  # điểm vào (Phụ lục K)
├── VERSION
├── config/                    # render.yaml, augment config, mapping nhãn
├── src/                       # CODE: render, augment, crop, validate, eval
│   ├── data/  augment/  eval/  utils/
├── data_meta/                 # metadata + annotations (KHÔNG chứa ảnh lớn)
│   ├── pages.csv
│   ├── annotations/coco/
│   └── formulas/latex/
├── experiments/               # log + config từng experiment (KHÔNG chứa weights)
│   └── EXP-P1-003/ (config.yaml, result.json, notes.md)
├── docs/                      # Playbook, Guideline v1, Decision Records
└── outputs/                   # bảng, biểu đồ, báo cáo (kết quả nhẹ)
```

### 20.2. Không commit dữ liệu lớn
- **KHÔNG** commit: ảnh trang, weights model, crop hàng loạt (nặng, và **vướng bản quyền SGK**).
- Lưu dữ liệu lớn ở: ổ chia sẻ (Google Drive/OneDrive) hoặc Git LFS (nếu chốt dùng). Repo chỉ giữ **metadata + annotation + code + log** (nhẹ, text, diff được).
- Thêm `.gitignore`: `*.png *.jpg *.pdf *.pt *.pth weights/ clean/images/ ...`

### 20.3. Naming & workflow Git cho nhóm 3 người (đơn giản cho người mới) `[RECO]`
- **Branch:** `main` (ổn định) + nhánh việc `feat/<tên>-<việc>` (VD `feat/duc-augment-pipeline`).
- **Commit message:** `<loại>: <mô tả ngắn>` — loại ∈ {data, code, eval, docs, fix}. VD `eval: add IoU/mAP script for clean set`.
- **Pull Request nhỏ:** mỗi PR một việc; Khoa review trước khi merge vào main.
- **Tag:** freeze dataset → tag `dataset-v0.1-pilot`; mốc quan trọng → tag.
- **WHY đơn giản:** quy trình quá phức tạp gây sợ. Nhánh + PR + review là đủ an toàn và dạy được thói quen tốt.

**Definition of Done cho Git:** repo có cấu trúc trên, `.gitignore` chặn file lớn, ≥1 PR đã review-merge, dataset freeze có tag, README chạy được theo hướng dẫn.

---

## PART 21 — 14-DAY EXECUTION PLAN

> **Cấu trúc vai trò:** Khoa = Technical/Research Lead; Nguyên = Research Pair Lead + Mentor cho Đức; Đức = Developing Researcher (có phần việc *do chính mình sở hữu*). Nguyên & Đức **cùng làm** để hiểu sâu.
>
> Mỗi ngày: **DAY OBJECTIVE · WHY · KHOA · NGUYÊN · ĐỨC · EXPECTED OUTPUT · DEFINITION OF DONE · COMMON FAILURE · END-OF-DAY CHECK.** Kế hoạch là `[RECO]` — nhóm điều chỉnh theo thực tế; các mốc lớn (Golden Sample trước annotation hàng loạt; freeze trước baseline) thì **không đảo thứ tự**.

### GIAI ĐOẠN A — HIỂU & THIẾT KẾ (Day 1–3)

#### DAY 1 — Căn chỉnh đề tài & môi trường
- **OBJECTIVE:** cả nhóm hiểu chung đề tài + dựng môi trường + chọn công cụ.
- **WHY:** hiểu sai đề tài ngày 1 → sai 14 ngày.
- **KHOA:** dẫn buổi đọc Part 0–2; chốt 13.6 (repo RT-DocLayout) & Mathpix; dựng skeleton repo + `.gitignore`.
- **NGUYÊN:** đọc Part 1–2–7; cùng Khoa dựng repo;
- **ĐỨC:** đọc Part 0–1; cài Python + venv + Git; tự tạo commit đầu tiên (thêm tên vào README).
- **OUTPUT:** repo khởi tạo; môi trường máy 3 người sẵn sàng; danh sách quyết định ngày 1.
- **DoD:** cả 3 giải thích được (bằng lời) RQ và 2 giả thuyết; `git clone` chạy được.
- **COMMON FAILURE:** nhảy vào code trước khi hiểu đề tài.
- **EOD CHECK:** mỗi người nói 1 câu "giả thuyết của đề tài là gì?" — trùng khớp nhau.

#### DAY 2 — Chọn nguồn & pipeline PDF→ảnh
- **OBJECTIVE:** chốt ~30–50 trang pilot + render clean images.
- **WHY:** dữ liệu vào đúng thì mọi thứ sau mới có nghĩa.
- **KHOA:** viết `src/data/render.py` (Part 5.4) + config DPI; review tiêu chí chọn trang.
- **NGUYÊN:** cùng Đức duyệt trang theo checklist 4.4; lập `pages.csv` với `selection_reason`.
- **ĐỨC:** chạy render.py trên vài trang; kiểm ảnh nét/đọc được; ghi checksum.
- **OUTPUT:** `clean/images/` (30–50 ảnh) + `pages.csv` phủ đủ ma trận 4.2.
- **DoD:** validate ảnh (RGB, DPI đúng); ma trận đa dạng không trống ô quan trọng.
- **COMMON FAILURE:** chọn toàn trang dễ; DPI mỗi trang mỗi khác.
- **EOD CHECK:** mở 5 ảnh phóng to công thức nhỏ nhất — đọc được.

#### DAY 3 — Taxonomy & Golden Sample (gán độc lập)
- **OBJECTIVE:** chốt hiểu taxonomy + cả 3 gán **độc lập** 5–10 golden pages.
- **WHY:** phát hiện mơ hồ *sớm*, trước khi gán hàng loạt.
- **KHOA:** cài Label Studio + cấu hình 6 nhãn (10.3); gán golden độc lập.
- **NGUYÊN:** gán golden độc lập; chuẩn bị khung Disagreement table.
- **ĐỨC:** gán golden độc lập (Nguyên hỗ trợ hướng dẫn *cách dùng công cụ*, KHÔNG gợi ý nhãn).
- **OUTPUT:** 3 bộ nhãn độc lập trên golden pages.
- **DoD:** đủ 3 bộ nhãn; chưa bàn nhau về nhãn (giữ tính độc lập).
- **COMMON FAILURE:** bắt đầu gán 40 trang luôn (bỏ qua golden) — **cấm**.
- **EOD CHECK:** có đủ 3 export golden.

### GIAI ĐOẠN B — XÂY DATASET (Day 4–9)

#### DAY 4 — Guideline v1 từ bất đồng
- **OBJECTIVE:** dựng Disagreement table → chốt rule → **Annotation Guideline v1**.
- **WHY:** guideline nhất quán = trần chất lượng dataset.
- **KHOA:** chủ trì họp resolve; ra quyết định các OPEN DECISION taxonomy (7.2/7.3/7.4); ghi Decision Records.
- **NGUYÊN:** dựng Disagreement table; soạn Guideline v1 kèm ví dụ ảnh thật.
- **ĐỨC:** đối chiếu nhãn của mình với người khác; Hiểu rõ lý do mình chọn.
- **OUTPUT:** `docs/annotation_guideline_v1.md` + Decision Records.
- **DoD:** mọi bất đồng golden đều có final decision + lý do; gán lại golden theo v1 đạt đồng thuận cao.
- **COMMON FAILURE:** "đoán bừa ai đúng" thay vì theo protocol.
- **EOD CHECK:** đo đồng thuận sau v1 > trước v1.

#### DAY 5 — Augmentation pipeline (reproducible)
- **OBJECTIVE:** dựng pipeline nhiễu tự động có seed + transform log.
- **WHY:** cần dữ liệu noisy để đo robustness; phải tái lập.
- **KHOA:** review kiến trúc augment; kiểm API seed của Albumentations bản đang cài (6.4).
- **NGUYÊN:** viết `src/augment/` với bbox đồng bộ; sinh vài ảnh mẫu.
- **ĐỨC (OWNS):** **sở hữu việc kiểm chứng reproducibility** — chạy lại cùng seed, so checksum; overlay box lên ảnh xoay/méo (Part 6.6).
- **OUTPUT:** `augmented/` + `augmented/transforms/`.
- **DoD:** cùng seed → ảnh giống hệt; box augmented ôm đúng đối tượng (5 ảnh kiểm mắt).
- **COMMON FAILURE:** augment mà không lưu seed; quên biến đổi box khi xoay.
- **EOD CHECK:** demo tái lập 1 ảnh trước cả nhóm.

#### DAY 6–7 — Annotation hàng loạt (layout) + peer review
- **OBJECTIVE:** gán layout ~30–50 trang theo Guideline v1, có peer review.
- **WHY:** đây là khối lượng chính của layout GT.
- **KHOA:** review kỹ thuật mẫu; xử lý ca khó phát sinh; KHÔNG ngồi gán hàng loạt.
- **NGUYÊN:** gán một nửa; peer-review phần của Đức; xử lý disagreement theo protocol.
- **ĐỨC (OWNS một batch):** gán batch được giao (VD 15–20 trang); self-check; nêu ca nghi ngờ (flag, không đoán).
- **OUTPUT:** nhãn layout cho toàn pilot (Label Studio).
- **DoD:** mỗi batch qua self-check + peer review; ca mơ hồ đã flag/resolve.
- **COMMON FAILURE:** gán nhanh ẩu để "cho xong"; bỏ peer review.
- **EOD CHECK (mỗi ngày):** số trang reviewed tăng; disagreement log cập nhật.

#### DAY 8 — Export COCO + validate + Math GT (bắt đầu)
- **OBJECTIVE:** export COCO sạch + khởi động Mathematical GT cho Display-Math.
- **WHY:** COCO là đầu vào chấm layout; LaTeX GT là đầu vào chấm math.
- **KHOA:** viết/chạy `src/eval/validate_coco.py` (12.5); sửa lỗi format.
- **NGUYÊN:** export COCO; bắt đầu Math GT (Mathpix/gõ tay) + render-compare (Part 11).
- **ĐỨC:** cùng làm Math GT ca dễ ($E=mc^2$...); học đọc cấu trúc LaTeX; render-compare.
- **OUTPUT:** `annotations/coco/instances_clean.json` (validate PASS) + phần LaTeX GT.
- **DoD:** validate COCO PASS; LaTeX đã làm đều render khớp ảnh.
- **COMMON FAILURE:** tin Mathpix tuyệt đối, bỏ bước compare.
- **EOD CHECK:** validate script in "PASS".

#### DAY 9 — Hoàn tất Math GT + QA + FREEZE
- **OBJECTIVE:** xong LaTeX GT + chạy QA 4 tầng + **freeze v0.1-pilot**.
- **WHY:** baseline phải chạy trên dataset *bất biến*.
- **KHOA:** QA tầng 3 (script); duyệt freeze; gắn Git tag `dataset-v0.1-pilot`.
- **NGUYÊN:** hoàn tất Math GT khó (tích phân, ma trận, hóa lồng text); QA checklist 12.3.
- **ĐỨC:** kiểm missing formula / invalid LaTeX (render toàn bộ, lọc lỗi).
- **OUTPUT:** dataset pilot hoàn chỉnh + VERSION + tag.
- **DoD:** checklist 12.3 tick hết; validate PASS; tag tồn tại.
- **COMMON FAILURE:** freeze khi còn LaTeX render lỗi.
- **EOD CHECK:** `git tag` hiện `dataset-v0.1-pilot`.

### GIAI ĐOẠN C — BASELINE & ĐÁNH GIÁ (Day 10–14)

#### DAY 10 — Baseline layout (RT-DocLayout)
- **OBJECTIVE:** inference RT-DocLayout trên Clean + ≥1 noisy; lập category mapping.
- **WHY:** đây là số baseline layout của đề tài.
- **KHOA (OWNS):** setup model, chạy inference, chuẩn hoá prediction, dựng mapping (16.5).
- **NGUYÊN:** kiểm visualize; cùng Khoa soát mapping nhãn.
- **ĐỨC:** mở ảnh vis, ghi cảm nhận định tính (model bắt trượt công thức ở đâu).
- **OUTPUT:** `baseline/rtdoclayout/pred_*.json` + vis + bảng mapping.
- **DoD:** số ảnh out = in; vis hợp lý; mapping ghi rõ.
- **COMMON FAILURE:** quên mapping → mAP thấp giả tạo.
- **EOD CHECK:** mở 5 vis — box không loạn.

#### DAY 11 — Baseline math (UniMERNet) + Evaluation layout
- **OBJECTIVE:** inference UniMERNet trên crop; tính IoU/mAP layout.
- **KHOA:** viết `src/eval/eval_layout.py` (mAP@0.5, @[.5:.95], AP/lớp); chạy.
- **NGUYÊN (OWNS math baseline):** cắt crop (15.2), chạy UniMERNet, lưu pred LaTeX.
- **ĐỨC:** render pred LaTeX để mắt thường so nhanh vài ca.
- **OUTPUT:** bảng mAP layout + `baseline/unimernet/pred_*.jsonl`.
- **DoD:** có bảng mAP theo lớp cho ≥2 điều kiện.
- **COMMON FAILURE:** crop cụt ký tự làm UniMERNet sai oan.
- **EOD CHECK:** ca dễ $E=mc^2$ model đọc đúng.

#### DAY 12 — Evaluation math (CDM) + tổng hợp số
- **OBJECTIVE:** chạy CDM; tổng hợp bảng Clean vs Noisy cho cả 2 nhiệm vụ.
- **KHOA:** cài & chạy CDM chính thức (16.8); ghép bảng tổng hợp suy giảm.
- **NGUYÊN:** chuẩn input CDM (json gt/pred); kiểm điểm bất thường.
- **ĐỨC:** đối chiếu vài ca CDM thấp — xem lỗi kiểu gì (chuẩn bị error analysis).
- **OUTPUT:** bảng CDM (F1, ExpRate@CDM) + bảng suy giảm tổng.
- **DoD:** có số layout + math cho Clean và ≥1 noisy; cặp gt==pred cho điểm tối đa (sanity check).
- **COMMON FAILURE:** LaTeX GT render lỗi làm CDM sai → phải quay lại Part 11.
- **EOD CHECK:** sanity check CDM pass.

#### DAY 13 — Error Analysis
- **OBJECTIVE:** taxonomy lỗi + định lượng + ví dụ đại diện (H1/H2).
- **KHOA:** phân tích quantitative (tần suất, % suy giảm); phân biệt lỗi model vs lỗi GT.
- **NGUYÊN (OWNS):** dựng error log + chọn ví dụ đại diện; viết phần định tính.
- **ĐỨC:** phân loại thủ công một mẫu lỗi vào taxonomy (học nhận diện loại lỗi).
- **OUTPUT:** Error Analysis v0 (bảng + ví dụ + diễn giải thận trọng).
- **DoD:** đủ định lượng + định tính; nêu cả ca inconclusive; không kết luận quá mạnh.
- **COMMON FAILURE:** chỉ chọn ví dụ ủng hộ giả thuyết.
- **EOD CHECK:** mỗi loại lỗi chính có ≥1 ví dụ ảnh.

#### DAY 14 — Report + chuẩn bị họp thầy
- **OBJECTIVE:** viết Research Report v0 + slide/nội dung họp giảng viên + Questions for Supervisor.
- **KHOA:** kiểm reproducibility toàn bộ; chốt số liệu; duyệt report.
- **NGUYÊN:** viết report (Part 28 khung); tổng hợp open questions.
- **ĐỨC:** viết phần "Đã học được gì" + tự trả lời câu hỏi tự kiểm (Part 22).
- **OUTPUT:** Research Report v0 + danh sách câu hỏi cho thầy + checklist nghiệm thu (Part 27).
- **DoD:** report có đủ RQ→Dataset→Baseline→Metric→Result→Error→Limitations→Open Questions; mọi số tái lập được.
- **COMMON FAILURE:** report chỉ khoe "làm được X trang" thay vì kể câu chuyện nghiên cứu.
- **EOD CHECK:** thử "người ngoài đọc report có hiểu nhóm đã làm gì và tìm ra gì không?"

---

## PART 22 — LEARNING PATH FOR NGUYÊN AND ĐỨC

> Không chỉ giao task — thiết kế *mục tiêu học* theo 4 giai đoạn.

### 22.1. Bốn giai đoạn học chung
| Ngày | Giai đoạn | Trọng tâm |
|------|-----------|-----------|
| 1–3 | **Understand** | hiểu đề tài, taxonomy, công cụ (đọc + quan sát) |
| 4–7 | **Practice** | làm có người kèm (gán nhãn, augment, math GT) |
| 8–10 | **Independent execution** | tự làm phần được giao, ít cần nhắc |
| 11–14 | **Analysis & explanation** | tự phân tích kết quả & *giải thích được* |

### 22.2. Đức — Developing Researcher

**Kiến thức cần học (theo tuần):**
- Tuần 1: bounding box & object detection là gì; taxonomy 6 lớp; cấu trúc LaTeX cơ bản (mũ/chỉ số/phân số/căn); Git cơ bản; reproducibility nghĩa là gì.
- Tuần 2: đọc bảng mAP & CDM (không cần dẫn toán, cần *đọc* được); phân loại lỗi; vì sao freeze dataset.

**Mục tiêu kỹ năng sau 14 ngày:** tự gán một trang đúng guideline; tự cắt crop & render LaTeX; tự chạy pipeline augment với seed; đọc được một bảng kết quả và nói "lớp nào kém".

**Dấu hiệu Đức đã HIỂU (không chỉ làm):**
- Giải thích được *vì sao* Inline-Math khác Text (không chỉ "vì guideline bảo thế").
- Tự phát hiện một ca mơ hồ và **flag** thay vì đoán.
- Nói được "con số này nghĩa là gì với giả thuyết".

**Câu hỏi tự kiểm (Đức tự trả lời cuối tuần):**
1. Vì sao Phase 1 không fine-tune?
2. IoU=0.4 nghĩa là gì bằng lời?
3. Vì sao không dùng Edit Distance cho công thức?
4. Nếu tôi xoá file transform của một ảnh augmented, hậu quả là gì?
5. Sự khác nhau giữa "evidence ủng hộ" và "inconclusive"?

> **Ràng buộc:** Đức **không** bị biến thành người chỉ làm task tay. Mỗi ngày Đức có phần *sở hữu* (Day 5 reproducibility, Day 6–7 một batch...). Nguyên hướng dẫn *cách nghĩ*.

### 22.3. Nguyên — từ Operator → Researcher

**Phát triển năng lực:**
- Chuyển từ "làm theo bước" sang "*thiết kế* bước": tự đề xuất tiêu chí chọn ví dụ lỗi, tự soạn Guideline.
- Tăng **phân tích:** đọc bảng số → rút nhận định thận trọng, phân biệt lỗi model vs lỗi GT.
- **Mentor Đức:** giải thích lại khái niệm; review chứ không làm thay.
- **Tham gia quyết định nghiên cứu:** có tiếng nói trong các OPEN DECISION; đề xuất phương án cho Khoa duyệt.

**Dấu hiệu Nguyên đã lên researcher:**
- Tự viết được một mục Error Analysis có định lượng + định tính.
- Biết khi nào nói "chưa đủ dữ liệu để kết luận".
- Đề xuất được một OPEN DECISION kèm ưu/nhược.

### 22.4. Khoa — Technical/Research Lead (ghi chú vận hành)
- Tập trung: kiến trúc pipeline, category mapping, evaluation, reproducibility, ra quyết định kỹ thuật.
- Vai trò mentor: đặt câu hỏi để Nguyên/Đức tự tìm ra, thay vì đưa đáp án ngay.

---

## PART 23 — DAILY RESEARCH MEETING

> Họp **15–20 phút**, đứng, mỗi ngày. **Không** biến thành báo cáo hành chính.

### 23.1. Mỗi người trả lời 6 câu
1. Hôm qua tôi làm gì? (kết quả, không phải "đã bận")
2. Tôi **hiểu thêm** điều gì? (1 insight)
3. Tôi gặp **vấn đề** gì? (blocker cụ thể)
4. Tôi có **evidence** gì? (số/quan sát, dù nhỏ)
5. Hôm nay tôi sẽ làm gì?
6. Tôi cần **ai** hỗ trợ?

### 23.2. Nguyên tắc
- Câu 2 & 4 là *linh hồn* — ép mỗi người *nghĩ như nhà nghiên cứu*, không chỉ liệt kê việc.
- Blocker quá 10 phút → hẹn "parking lot" sau họp, không kéo dài cả nhóm.
- Ghi **Meeting minutes** ngắn (Phụ lục H): quyết định + việc giao + blocker.
- Nếu ai nói câu 2/4 là "không có" nhiều ngày liền → dấu hiệu đang làm máy móc, cần điều chỉnh.

---

## PART 24 — DEFINITION OF DONE

> Một task chỉ **Done** khi có đủ **3 thành phần: Artifact + Validation + Record.** Thiếu một → chưa xong.

| Loại task | Artifact | Validation | Record |
|-----------|----------|-----------|--------|
| **Dataset page** | ảnh clean trong repo | RGB/DPI đúng, đọc được | dòng pages.csv + checksum |
| **Annotation** | box trong Label Studio/COCO | self-check + peer review pass | reviewer + ngày |
| **Math formula** | crop + LaTeX + ảnh render | render khớp ảnh gốc (người 2 xác nhận) | dòng .jsonl truy ngược page |
| **Augmentation** | ảnh augmented | cùng seed → giống hệt; box đúng | transform.json |
| **Baseline run** | file prediction | số out=in; visualize hợp lý | experiment log + ckpt hash |
| **Evaluation run** | bảng metric | sanity check (gt==pred→điểm max) | experiment log + config |
| **Error analysis** | bảng lỗi + ví dụ | phân biệt lỗi model/GT; có ca inconclusive | ai phân tích + ngày |
| **Research decision** | dòng Decision Record | có ưu/nhược + người quyết | Phụ lục I |

> **WHY khắt khe vậy:** "Done" mơ hồ là nguồn gốc mọi nợ kỹ thuật nghiên cứu. Một annotation "xong" mà không ai review = quả bom hẹn giờ cho metric.

---

## PART 25 — TROUBLESHOOTING

> Mục tiêu: thành viên tự xử lý được đa số sự cố nhỏ.

### 25.1. Label Studio không mở
- **Nguyên nhân:** port 8080 bận; env chưa activate; version lỗi.
- **Kiểm:** `label-studio --version`; xem log terminal; thử `label-studio start -p 8081`.
- **Xử lý:** đổi port; tạo lại venv; xem local file serving (`LOCAL_FILES_SERVING_ENABLED=1`) nếu ảnh không hiện.

### 25.2. COCO invalid
- **Nguyên nhân:** bbox âm/tràn ảnh; category lạ; ảnh không có annotation bị drop.
- **Validate:** chạy `validate_coco.py` (12.5) → đọc danh sách lỗi theo mã ([invalid-bbox], [bbox-outside]...).
- **Xử lý:** sửa trong Label Studio rồi export lại; hoặc vá bằng script (ghi lại việc vá).

### 25.3. Model không chạy
- **Dependency:** thiếu/lệch version → tạo venv sạch, cài đúng theo README checkpoint; ghi version.
- **Memory (VRAM/RAM):** hết bộ nhớ → giảm batch, chạy CPU, hoặc xử lý từng ảnh.
- **Path:** sai đường dẫn weights/ảnh → dùng đường tuyệt đối; kiểm tồn tại file.
- **Weights:** tải thiếu/hỏng → so hash; tải lại từ nguồn chính thức.

### 25.4. LaTeX render sai
- **Nguyên nhân:** thiếu gói (mhchem cho hóa), ký tự đặc biệt, thiếu `$...$`/môi trường.
- **Kiểm:** render từng công thức, xem thông báo lỗi; thử render tối giản.
- **Xử lý:** thêm gói; escape ký tự; với hóa dùng quy ước đã chốt (11.5 OPEN DECISION).

### 25.5. Metric bất thường (VD mAP≈0 hoặc CDM≈0 dù nhìn ổn)
- **Kiểm ground truth:** GT có đúng format? bbox x,y,w,h đúng thứ tự?
- **Kiểm prediction:** đã chuẩn hoá về cùng schema? toạ độ cùng hệ (pixel)?
- **Kiểm matching:** **category mapping** đúng chưa (16.5)? (nguyên nhân #1 của mAP thấp giả).
- **Kiểm configuration:** ngưỡng IoU/score hợp lý? ảnh pred và GT cùng kích thước?
- **Nguyên tắc:** metric "quá đẹp" hay "quá tệ" bất thường → **nghi pipeline trước, nghi model sau**. Luôn chạy *sanity check* (gt==pred phải cho điểm tối đa).

---

## PART 26 — COMMON RESEARCH MISTAKES

> Mỗi lỗi: **tại sao sai · hậu quả · cách phòng tránh.**

1. **Thu thập dữ liệu trước khi định nghĩa taxonomy.**
   - *Sai vì:* không biết mình đang gán *cái gì*. *Hậu quả:* gán lại từ đầu khi taxonomy đổi. *Phòng:* Golden Sample + Guideline v1 *trước* annotation hàng loạt (Day 3–4).

2. **Annotation không thống nhất giữa người.**
   - *Sai vì:* mỗi người một "gu". *Hậu quả:* model học mâu thuẫn → không bao giờ tốt. *Phòng:* peer review + disagreement protocol (Part 8).

3. **Không có Golden Samples.**
   - *Sai vì:* không có chuẩn để kiểm nhất quán. *Hậu quả:* phát hiện lỗi guideline quá muộn. *Phòng:* Part 9.

4. **Augmentation không reproducible.**
   - *Sai vì:* không lưu seed/params. *Hậu quả:* không tái tạo được ảnh nhiễu → mất tính khoa học. *Phòng:* seed + transform.json (Part 6.4).

5. **Không lưu model/version/config.**
   - *Sai vì:* nghĩ "sẽ nhớ". *Hậu quả:* số liệu không tái lập, không so được với Team 2. *Phòng:* experiment log + ckpt hash (Part 17).

6. **Chỉ báo cáo metric mà không error analysis.**
   - *Sai vì:* con số không giải thích *tại sao*. *Hậu quả:* báo cáo nông, bài báo yếu. *Phòng:* Part 18 bắt buộc.

7. **Chọn ví dụ có lợi cho hypothesis (cherry-picking).**
   - *Sai vì:* thiên lệch xác nhận. *Hậu quả:* kết luận sai, mất uy tín khoa học. *Phòng:* chọn ví dụ *đại diện*, báo cả ca ngược chiều (18.6).

8. **Thay đổi experiment nhưng không ghi log.**
   - *Sai vì:* đổi ngưỡng/mapping âm thầm. *Hậu quả:* không biết số nào ứng cấu hình nào. *Phòng:* mỗi thay đổi → một entry log.

9. **Nhầm correlation với evidence.**
   - *Sai vì:* "trang tiếng Việt điểm thấp" ≠ "vì tiếng Việt". Có thể do trang đó nhiều công thức khó. *Hậu quả:* quy sai nguyên nhân. *Phòng:* kiểm biến gây nhiễu; so *cùng loại trang* Clean vs Noisy.

10. **Kết luận quá mạnh so với dữ liệu.**
    - *Sai vì:* pilot 40 trang không đủ khái quát toàn SGK. *Hậu quả:* claim vượt evidence → phản biện đánh sập. *Phòng:* ngôn ngữ thận trọng ("trên pilot của chúng tôi...", "cần xác nhận trên tập lớn hơn"); nêu Limitations (Part 28).

---

## PART 27 — FINAL OUTPUT AFTER 14 DAYS

> Checklist nghiệm thu + *output này dùng cho Phase sau thế nào*.

| # | Output | Nghiệm thu khi | Dùng cho Phase sau |
|---|--------|----------------|--------------------|
| 1 | **Dataset Pilot** (~30–50 trang, đủ variant) | freeze v0.1-pilot + tag | nguyên liệu Team 2 train; mẫu để scale lên 150–200 trang |
| 2 | **Dataset metadata** | pages.csv đầy đủ, checksum | reproducibility cho mọi Phase |
| 3 | **Annotation Guideline v1** | có rule + ví dụ ảnh | chuẩn để scale annotation nhất quán |
| 4 | **Golden Sample** | disagreement table + đồng thuận cao | bộ kiểm chất lượng annotator mới |
| 5 | **COCO annotations** | validate PASS | đầu vào train layout (Team 2) |
| 6 | **Mathematical Ground Truth** | render khớp | đầu vào train MER (Team 2) & chấm CDM |
| 7 | **Augmented dataset** | reproducible | Team 2 nhân bản dữ liệu train |
| 8 | **RT-DocLayout baseline** | pred + mapping + số | mốc để Team 2 vượt (Delta) |
| 9 | **UniMERNet baseline** | pred + số CDM | mốc MER để Team 2 vượt |
| 10 | **Evaluation prototype** | IoU/mAP + CDM chạy được | công cụ đo dùng lại xuyên đề tài |
| 11 | **Error Analysis** | định lượng + định tính | định hướng Team 2 fine-tune cái gì; nội dung bài báo |
| 12 | **Experiment Log** | đủ để tái lập | minh bạch khoa học |
| 13 | **Research Report v0** | kể được câu chuyện nghiên cứu | báo cáo thầy + khung bài báo Q4 |
| 14 | **Questions for next meeting** | danh sách rõ ràng | chốt hướng trước khi scale |

> **Nhắc lại điểm giao bóng 1 `[OFFICIAL]`:** đầu ra #1, #5, #6, #7 chính là thứ Team 2 nhận để bắt đầu — nên chất lượng & tính nhất quán của chúng quyết định thành bại Phase 2.

---

## PART 28 — MEETING WITH SUPERVISOR

> Sau 14 ngày trình bày với giảng viên. **Không** chỉ "chúng em làm được bao nhiêu trang".

### 28.1. Khung trình bày (kể chuyện nghiên cứu)
1. **Research Question** — câu hỏi Team 1 nhắm tới.
2. **Dataset** — pilot: quy mô, phủ đa dạng, các variant, cách đảm bảo reproducibility.
3. **Annotation Protocol** — taxonomy, Guideline v1, cách đo đồng thuận, disagreement protocol.
4. **Baseline** — model nào, checkpoint nào, chạy điều kiện nào.
5. **Metrics** — IoU/mAP (layout), CDM (math); vì sao không BLEU/Edit Distance.
6. **Initial Results** — bảng Clean vs Noisy; AP theo lớp; điểm CDM.
7. **Error Analysis** — loại lỗi chính; ví dụ đại diện (dấu thanh, hóa lồng text).
8. **Unexpected Findings** — điều bất ngờ (VD model tốt/kém ngoài dự đoán).
9. **Limitations** — pilot nhỏ, chưa phủ hết chương, một số OPEN DECISION chưa chốt.
10. **Open Questions** — cần thầy quyết (xem dưới).
11. **Recommendation for next phase** — nên scale thế nào, ưu tiên gì.

### 28.2. Bắt buộc nói rõ 4 nhóm
- ✅ **Cái gì đã được xác nhận** (VD "pipeline end-to-end chạy được và tái lập").
- ⏳ **Cái gì chưa xác nhận** (VD "chưa đủ dữ liệu để khẳng định mức suy giảm tổng quát").
- ❌ **Cái gì thất bại** (VD "công thức hóa lồng text: cả GT lẫn model đều khó — cần hướng riêng").
- 🔶 **Quyết định nào cần thầy duyệt** (bản quyền SGK, checkpoint RT-DocLayout, xử lý caption/hóa, tỉ lệ augmented trong test...).

> **WHY:** trình bày trung thực cả cái *chưa xong/thất bại* làm nhóm đáng tin hơn và giúp thầy định hướng đúng — đó là mục tiêu thật của buổi họp, không phải "khoe thành tích".

---

## PART 29 — APPENDICES

> Template copy-dùng-ngay.

### Phụ lục A — Dataset metadata template (`pages.csv` header)
```csv
page_uid,book_id,subject,grade,chapter,page_book,pdf_page_no,variant,aug_index,source_pdf,render_dpi,width,height,color_mode,transform_json,selection_reason,annotator,created_by,created_at,checksum_sha1
```

### Phụ lục B — Annotation checklist (in ra dán cạnh màn hình)
```
[ ] Quan sát cả trang trước khi vẽ
[ ] Mọi đối tượng khó đều có box (không sót)
[ ] Box ôm sát, không cụt/không thừa
[ ] Nhãn đúng Part 7 (Inline vs Display, Table vs Figure)
[ ] Display-Math ôm trọn chỉ số/căn/ngoặc
[ ] Không box tràn ngoài ảnh
[ ] Ca nghi ngờ đã FLAG (không đoán)
[ ] Đã self-check trước khi submit batch
```

### Phụ lục C — Golden Sample table
```
| page | region | Khoa | Nguyên | Đức | Final decision | Reason |
|------|--------|------|--------|-----|----------------|--------|
```

### Phụ lục D — Disagreement log
```
| id | date | page | region | người A / nhãn | người B / nhãn | mô tả | guideline có rule? | quyết định | rule mới thêm |
```

### Phụ lục E — Experiment log
```
| exp_id | date | dataset_version | variant | model | ckpt_hash | config | seed | metric_name | metric_value | notes |
```

### Phụ lục F — Error analysis table
```
| exp_id | sample | task(layout/math) | error_type | severity(low/mid/high) | mô tả | ảnh ví dụ | liên hệ H1/H2 |
```

### Phụ lục G — Daily research log (cá nhân)
```
Date:
Hôm qua làm:        
Hiểu thêm (insight):
Vấn đề (blocker):
Evidence có được:
Hôm nay sẽ làm:
Cần hỗ trợ từ:
```

### Phụ lục H — Meeting minutes
```
Date / Có mặt:
Quyết định hôm nay:
Việc giao (ai - gì - hạn):
Blocker cần giải:
Parking lot (bàn sau):
```

### Phụ lục I — Research Decision Record
```
[YYYY-MM-DD] DECISION #NNN
Chủ đề:
Bối cảnh/vấn đề:
Các phương án (ưu/nhược):
Quyết định:
Người quyết định:
Ảnh hưởng tới phần nào của pipeline:
```

### Phụ lục J — Dataset release checklist (trước freeze)
```
[ ] pages.csv đầy đủ, checksum khớp
[ ] validate_coco PASS
[ ] mọi Display-Math có LaTeX GT, render khớp
[ ] mọi LaTeX render không lỗi
[ ] augmented có transform.json, tái lập được
[ ] naming nhất quán, không ID trùng
[ ] VERSION cập nhật + Git tag
[ ] dataset_card.md (Phụ lục K con) mô tả rõ
```

### Phụ lục K — README template
```markdown
# Viet12-Team1 — Dataset & Baseline (Phase 1A)
## Mục tiêu
## Cấu trúc thư mục
## Cách tái lập
1. Môi trường (Python, torch, CUDA)
2. Render ảnh: python src/data/render.py --config config/render.yaml
3. Augment:    python src/augment/run.py --seed 42
4. Validate:   python src/eval/validate_coco.py
5. Baseline:   (RT-DocLayout / UniMERNet — theo docs/)
6. Evaluate:   python src/eval/eval_layout.py ; cdm ...
## Phiên bản dataset
## Giới hạn & lưu ý bản quyền
## Liên hệ / vai trò
```

---

## MASTER CHECKLIST — PHASE 1A (14 NGÀY)

**Hiểu & thiết kế**
- [ ] Cả 3 người giải thích được RQ + 2 giả thuyết (H1 dấu thanh, H2 đan xen)
- [ ] Repo khởi tạo + `.gitignore` chặn file lớn
- [ ] Chốt checkpoint RT-DocLayout (13.6) + phương án Mathpix
- [ ] ~30–50 trang pilot chọn theo checklist, phủ đủ ma trận đa dạng
- [ ] `clean/images/` render nhất quán (1 config) + pages.csv + checksum

**Annotation & Golden**
- [ ] 3 người gán Golden độc lập → Disagreement table
- [ ] Annotation Guideline v1 (rule + ví dụ ảnh) + Decision Records
- [ ] Đồng thuận sau v1 cao (>~90%)
- [ ] Annotation layout toàn pilot, mỗi batch self-check + peer review
- [ ] Export COCO + validate PASS

**Augmentation & Math GT**
- [ ] Pipeline augment reproducible (seed + transform.json), box đúng
- [ ] Math GT cho mọi Display-Math, render khớp ảnh gốc
- [ ] Quyết định xử lý hóa học / caption / inline được ghi Decision Record

**QA & Freeze**
- [ ] QA 4 tầng; checklist 12.3 tick hết
- [ ] Freeze `v0.1-pilot` + Git tag + VERSION

**Baseline & Evaluation**
- [ ] RT-DocLayout inference (Clean + ≥1 noisy) + category mapping
- [ ] UniMERNet inference trên crop Display-Math
- [ ] IoU/mAP (mAP@0.5, @[.5:.95], AP/lớp) tính được
- [ ] CDM (F1, ExpRate@CDM) chạy được; sanity check pass
- [ ] Bảng suy giảm Clean vs Noisy (layout + math)

**Phân tích & Bàn giao**
- [ ] Error Analysis v0 (định lượng + định tính + ví dụ đại diện, có ca inconclusive)
- [ ] Experiment Log đầy đủ, tái lập được
- [ ] Research Report v0 (khung Part 28)
- [ ] Questions for Supervisor
- [ ] Đức hoàn thành câu hỏi tự kiểm (22.2)

---

## QUESTIONS FOR SUPERVISOR

> Những điểm cần giảng viên xác nhận **trước khi scale dataset** lên 150–200 trang.

1. **Bản quyền SGK:** Phạm vi được phép sử dụng ảnh trang SGK trong dataset và bài báo Q4? Được công bố ảnh trang, hay chỉ công bố annotation + số liệu (không kèm ảnh gốc)?
2. **Checkpoint "RT-DocLayout":** Thầy muốn dùng đúng mô hình/repo/checkpoint nào? (có nhiều biến thể layout tên gần nhau — xem 13.6). Có model layout tham chiếu thầy chỉ định không?
3. **Mathpix:** Nhóm có tài khoản/ngân sách Mathpix không, hay Phase 1 gõ tay LaTeX?
4. **Caption:** Gán caption là `Text` (mặc định đề xuất) hay có quy ước riêng? (taxonomy chính thức không có lớp Caption.)
5. **Công thức hóa học:** Chuẩn LaTeX cho hóa (mhchem `\ce{}` hay `_{}`/`^{}`)? Có chấm CDM cho công thức hóa không (có thể ngoài phân bố UniMERNet)?
6. **Inline-Math:** Phase 1 có cần Mathematical GT (LaTeX) cho Inline-Math, hay chỉ Display-Math như tài liệu nêu?
7. **mAP variant:** Báo cáo mAP@0.5, mAP@[.5:.95], hay cả hai? Ngưỡng IoU chuẩn của đề tài?
8. **Augmented trong tập test:** Ảnh augmented dùng để *đánh giá baseline* hay chỉ để Team 2 *train*? Tỉ lệ thật/augmented mong muốn?
9. **Quy mô pilot:** Thầy đồng ý mốc vận hành ~30–50 trang cho 14 ngày (rồi mới scale 150–200) chứ?
10. **Tiêu chí "suy giảm nghiêm trọng":** Ngưỡng định lượng nào (VD giảm ≥X% CDM/mAP) đủ để gọi là bằng chứng ủng hộ giả thuyết?
11. **Segmentation/reading order:** Phase 1 có cần đánh giá mask/reading order (RT-DocLayout xuất được), hay chỉ bbox?
12. **Bộ SGK cụ thể:** Dùng bộ sách nào (Kết nối tri thức / Cánh Diều / Chân trời sáng tạo) cho từng môn, để nhất quán `book_id`?

---

## OPEN RESEARCH QUESTIONS / DECISIONS REQUIRED

> Tổng hợp mọi điểm 🔶 **chưa được tài liệu nguồn quy định** — cần nhóm (hoặc thầy) quyết định. Mỗi mục ghi: vấn đề · phương án · ưu/nhược · khuyến nghị · ai quyết.

| # | Vấn đề | Phương án | Khuyến nghị `[RECO]` | Ai quyết |
|---|--------|-----------|----------------------|----------|
| O1 | Inline-Math nằm trong câu: box riêng lồng trong Text, hay cắt Text? | (a) box lồng, overlap hợp lệ; (b) cắt Text | (a) box lồng — giữ Text mạch lạc | Khoa |
| O2 | Caption có lớp riêng? | Text / quy ước riêng / thêm lớp | gán `Text`, không thêm lớp | Khoa + thầy |
| O3 | Math lồng trong Table có gán không? | có / không (Phase 1) | không, để giảm mơ hồ | Khoa |
| O4 | Ký hiệu toán đơn lẻ ($x$) có là Inline-Math? | có / bỏ qua | chốt trong Golden Sample | nhóm |
| O5 | Hệ nhiều phương trình: 1 box hay nhiều? | 1 / nhiều | 1 box nếu là đơn vị ngữ nghĩa | Khoa |
| O6 | Chuẩn LaTeX cho hóa học | mhchem `\ce{}` / LaTeX thuần | thử `\ce{}`; kiểm CDM có đọc được | Khoa + thầy |
| O7 | Có chấm CDM cho công thức hóa? | có / không | Phase 1 tách riêng, báo cáo định tính | nhóm + thầy |
| O8 | Mapping nhãn model không có tương ứng (list, header...) | bỏ qua / gộp Text | bỏ qua ở pilot, ghi rõ | Khoa |
| O9 | segmentation trong COCO | có / không | không (bbox-only) ở pilot | Khoa |
| O10 | augmented vào test đánh giá baseline? | có / chỉ train | ưu tiên đo trên thật; augmented để scale | thầy |
| O11 | Bản quyền/chia sẻ ảnh SGK | công bố ảnh / chỉ annotation | chỉ annotation + số liệu tới khi thầy duyệt | thầy |
| O12 | Label Studio local vs server chung | local + gộp tay / server | local ở pilot; server khi scale | Khoa |
| O13 | DPI chuẩn render | 150 / 200 / 300 | thử, chốt 200 nếu công thức nhỏ vẫn nét | Khoa |

---

### GHI CHÚ CUỐI

Playbook này là **tài liệu sống** (Part 0.4). Mọi quyết định mới → cập nhật vào đây + Decision Record + tăng version. Nội dung `[OFFICIAL]` bám sát tài liệu hướng dẫn của giảng viên; `[RECO]`/`[IMPL]` là đề xuất/kỹ thuật của Playbook, nhóm được phép điều chỉnh. Không mục nào trong tài liệu này khẳng định giả thuyết của đề tài là đúng — điều đó chỉ được quyết bởi **thực nghiệm** mà 14 ngày này khởi động.

*Thông tin về các model/công cụ (RT-DocLayout, UniMERNet, CDM, Label Studio, Albumentations) tổng hợp từ nguồn công khai của chính công cụ/model tại thời điểm soạn; vì các dự án này cập nhật thường xuyên, luôn đối chiếu tài liệu chính thức của phiên bản đang dùng và ghi version vào experiment log.*
