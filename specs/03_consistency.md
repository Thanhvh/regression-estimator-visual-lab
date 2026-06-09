# BẢN ĐẶC TẢ MODULE — Lesson 3
## Consistency: collapse of the sampling distribution as n → ∞
**Dự án:** Regression Estimator Visual Lab — module thứ ba
**Tệp đầu ra:** `03_consistency.html` (ở gốc repo)

> Tái dùng engine đã kiểm chứng của Lesson 1/2 (Mục 10). Làm phần LÕI trước (Mục 1–6, 8–10); khung phễu hội tụ (Mục 7) và đường cong MSE(n) (Mục 7b) làm SAU khi lõi đã kiểm chứng đạt.

---

## 0. GHI CHÚ THIẾT KẾ QUAN TRỌNG (đã kiểm chứng bằng mô phỏng)

1. **Bốn ca ước lượng** lập thành lưới {chệch}×{vững} hoàn chỉnh, KHÔNG cần engine mới — chỉ tổ hợp DGP/OLS + nội sinh ρ (Lesson 1) và mẫu con (Lesson 2). Đây là lý do Lesson 3 tồn tại: phá vỡ ngộ nhận "vững = không chệch" theo *cả hai* chiều.
2. **Khung lõi = thanh trượt n + histogram co sập** (liền mạch L1/L2; thể hiện ca C "trượt tâm về β₁" rõ nhất). Phễu hội tụ là khung điểm nhấn (Mục 7), làm sau.
3. **Phủ đường cong Normal giải tích lên histogram (BẮT BUỘC ở lõi).** Trục x cố định + bin cố định sẽ khiến ở n lớn (≳1000) mọi mẫu dồn vào 1–2 bin → histogram thoái hóa thành cột thô, mất hình dạng. Đường cong mật độ `N(mean, SD²)` (vẽ từ công thức, mượt) hiển thị spike sạch ở MỌI n và là phép đối chứng lý thuyết. **Phải lấy mẫu đường cong ở độ phân giải mỗi-pixel (≥400 điểm trên trục)** — nếu không, spike hẹp ở n lớn lọt giữa các điểm mẫu và bị vẽ thành ~0 (bản vá tự vô hiệu). Ghi chú: đây là xấp xỉ Normal tiệm cận; ở n nhỏ histogram có thể đuôi nặng hơn đường cong.
4. **Tương tác thanh n:** khi RÊ thanh, cập nhật NGAY đường cong Normal giải tích (rẻ, tức thì); histogram lô chỉ vẽ lại khi NHẢ chuột (debounce). Hết lag, hết mâu thuẫn nút/thanh.
5. `n_min = 10`, `k_min = 10`; **ρ của ca B min = 0.1** (để B luôn nội sinh, nhãn không tự mâu thuẫn). Tránh đuôi nặng gây kiểm chứng SD dao động (rút ra từ Lesson 2).
6. **Ca D chỉ sinh k quan sát mỗi mẫu** (phần còn lại không dùng) — chính xác tuyệt đối, và không phụ thuộc n ⇒ không phí tính toán ở n lớn.

---

## 1. RÀNG BUỘC CỨNG (giống Lesson 1/2)

### 1.1. Ký hiệu — ĐÃ KHÓA
- Sai số tổng thể = `ε`; phần dư mẫu = `e`; tham số = `β₀, β₁`; ước lượng = `b₀, b₁`. KHÔNG dùng `u` cho sai số; KHÔNG hoán đổi `ε`/`e`.
- Ước lượng dốc của ca đang chọn ghi là `b₁`; vạch `β₁` là giá trị thật (đích).
- Màu nhất quán với L1/L2; cặp màu thân thiện người mù màu. Đường cong Normal dùng cùng màu với histogram (đậm hơn).

### 1.2. Kỹ thuật (giống Lesson 1/2)
- MỘT tệp `.html` tự chứa, chạy offline (`file://`), CSS + JS nội tuyến.
- KHÔNG thư viện ngoài/CDN/import ngoài/gọi mạng/build. Vẽ bằng SVG/Canvas thuần.
- PRNG có seed (mulberry32). KHÔNG dùng `Math.random()`.
- Số: 3 chữ số thập phân, dấu chấm. Giao diện **tiếng Anh**; ký hiệu toán giữ Latin/Hy Lạp.
- Tách code: `RNG → DGP → OLS → SAMPLING → ESTIMATORS → CHARTS → MSE → UI`.

---

## 2. MỤC TIÊU SƯ PHẠM

1. **Tính vững** là mệnh đề TIỆM CẬN: khi n→∞, phân phối lấy mẫu **co sập về một điểm tại β₁** (plim b₁ = β₁). Khác hẳn không chệch (mệnh đề mẫu hữu hạn về *tâm* tại một n cố định).
2. **Vững và không chệch độc lập nhau** (cả hai chiều): có ước lượng *chệch nhưng vững* (ca C) và *không chệch nhưng không vững* (ca D).
3. **MSE(n) → 0 ⇒ vững** (điều kiện ĐỦ: cả Var(n)→0 *lẫn* Bias(n)→0; qua Chebyshev). Nói chung chiều ngược KHÔNG đúng — ước lượng vững có thể có phương sai vô hạn/MSE không xác định. Nhưng với bốn ca phương sai hữu hạn ở module này, MSE→0 đặc trưng đúng nhóm vững (A,C) vs không vững (B,D). Nối thẳng phân rã MSE của Lesson 2.
4. Bài học thực tiễn: **thêm dữ liệu KHÔNG sửa được nội sinh** (ca B co sập về giá trị SAI).

Câu hỏi học tập: *"Nếu cứ thu thêm dữ liệu mãi, ước lượng của tôi có chắc tiến về sự thật không — và 'tiến về' nghĩa là gì?"*

---

## 3. MÔ HÌNH TOÁN HỌC (ĐÃ KIỂM CHỨNG bằng mô phỏng)

### 3.1. DGP — giống Lesson 1
```
xᵢ ~ Normal(μ_x, σ_x²);  εᵢ ~ Normal(0, σ_ε²) (ca B: có tương quan ρ với x);  yᵢ = β₀ + β₁·xᵢ + εᵢ
```
Ca B dùng đúng cơ chế nội sinh của Lesson 1: `εᵢ = ρ·((xᵢ−μ_x)/σ_x)·σ_ε + √(1−ρ²)·σ_ε·zᵢ`.
Mặc định: `μ_x=5, σ_x=2, β₀=1, β₁=0.5, σ_ε=1`, `n=30` (điểm bắt đầu của thanh trượt).

### 3.2. Bốn ca ước lượng (bộ chọn) — đều của β₁, theo n
| Ca | Định nghĩa | Mean (lý thuyết) | SD (lý thuyết) | Khi n→∞ |
|---|---|---|---|---|
| **A** OLS, ngoại sinh | `fitOLS(x,y).b1`, ρ=0 | `β₁` | `σ_ε/(σ_x·√(n−3))` | → **β₁** (vững) |
| **B** OLS, nội sinh | `fitOLS(x,y).b1`, ρ≥0.1 | `β₁ + ρ·σ_ε/σ_x` | `√(1−ρ²)·σ_ε/(σ_x·√(n−3))` | → **β₁+ρσ_ε/σ_x ≠ β₁** (KHÔNG vững: co về chỗ SAI) |
| **C** OLS + c/n | `fitOLS(x,y).b1 + c/n`, ρ=0 | `β₁ + c/n` | `σ_ε/(σ_x·√(n−3))` | → **β₁** (vững, nhưng chệch ở n hữu hạn) |
| **D** chỉ k quan sát | `fitOLS(x[0:k], y[0:k]).b1`, k cố định, ρ=0 | `β₁` | `σ_ε/(σ_x·√(k−3))` (KHÔNG phụ thuộc n) | SD **không → 0** (KHÔNG vững: không co) |

MSE(n) cho mỗi ca = `Var + Bias²`: A → 0; B → sàn `(ρσ_ε/σ_x)²`; C → 0; D → sàn `σ_ε²/(σ_x²(k−3))`.

### 3.3. Số đã kiểm chứng (σ_x=2, β₁=0.5, σ_ε=1; để đối chứng nhanh)
- A: n=10→SD 0.189; n=200→0.035; n=5000→0.007 (mean luôn 0.500).
- B (ρ=0.5): mean luôn 0.750; SD co như A → co về 0.750 (sai).
- C (c=5): mean 1.00→0.53→0.50 khi n 10→200→5000 (trượt về β₁); SD co như A.
- D (k=10): mean luôn 0.500; SD ≈ 0.189 ở MỌI n (không co).

---

## 4. BỐ CỤC MÀN HÌNH

**(A) Bộ chọn ca + lưới {chệch}×{vững}.** 4 nút chọn ca (A/B/C/D). Lưới 2×2 với hai trục đặt tên rõ: **"Finite-sample bias (at this n)"** × **"Consistency (n→∞)"**. Hiện CẢ BỐN ca trong ô tương ứng (A: unbiased·consistent; B: biased·inconsistent; C: biased·consistent; D: unbiased·inconsistent), tô sáng ca đang chọn, kèm dòng phán định, ví dụ "Case C: **Biased at finite n · Consistent** (collapses to β₁)". Hiện cả bốn để thấy hai trục độc lập.

**(B) Khung lõi — thanh trượt n + histogram co sập (trung tâm).** Histogram phân phối lấy mẫu của ca đang chọn tại n hiện tại, trên **trục b₁ CỐ ĐỊNH** (Mục 6.3), **PHỦ đường cong mật độ Normal giải tích `N(mean_lý_thuyết, SD_lý_thuyết²)`** (mượt; vẽ spike sạch ở mọi n; là phép đối chứng — histogram phải bám đường cong). Vạch đứng: `β₁` thật (đậm, "true β₁"), `mean b₁` hiện tại; ca B/C thêm vạch mờ tại **giới hạn**. Tùy chọn: "bóng mờ" của phân phối tại n=n_min để luôn thấy mức đã hẹp đi. Ghi chú nhỏ: đường cong là xấp xỉ Normal tiệm cận (n nhỏ histogram có thể đuôi nặng hơn).

**(C) Panel kiểm chứng + phán định giới hạn.** (Mục 8.)

---

## 5. THAM SỐ & ĐIỀU KHIỂN (nhãn tiếng Anh)

- **Bộ chọn ca:** A "OLS (exogenous)", B "OLS (endogenous)", C "OLS + c/n", D "First k obs only".
- **Thanh trượt n** ("sample size n"): **thang LOG, 10 → 10.000** (mốc 10, 30, 100, 300, 1000, 3000, 10000), mặc định 30; **n làm tròn về số nguyên**. Tương tác trung tâm. Rê thanh ⇒ đường cong Normal cập nhật tức thì; histogram lô vẽ lại khi NHẢ (debounce).
- Núm theo ca: `ρ` (**0.1**…0.8, mặc định 0.5; chỉ hiện B); `c` (0…10, mặc định 5; chỉ hiện C); `k` (10…100, mặc định 10; chỉ hiện D; kẹp k ≤ n).
- `σ_ε` ("noise"): 0.2…5 (mặc định 1).
- Nút: "Show distribution at this n" (ép vẽ lô ~2.000 mẫu ngay), "Draw more" (cộng thêm cho mượt), "Reset", "Run verification (20,000)" (hiện chỉ báo "computing…" lúc chạy 1–2 giây).
- Quy tắc: đổi **ca, ρ, c, k, σ_ε**, seed ⇒ tính lại trục cố định (6.3) + vẽ lại; đổi ca GIỮ NGUYÊN n (để so cùng n). Đổi **n** ⇒ đường cong cập nhật ngay, histogram vẽ lại khi nhả, NHƯNG **giữ nguyên trục**.

---

## 6. HÀNH VI & TƯƠNG TÁC

### 6.1. Rê thanh n (tương tác chính): đường cong Normal giải tích cập nhật tức thì theo n mới; khi nhả, histogram lô vẽ lại tại n mới trên trục cố định. Ánh xạ sự kiện: `input` (đang rê) → cập nhật đường cong; `change` (nhả) → vẽ lại histogram. Người học THẤY: ca A hẹp lại + bám β₁; ca B hẹp lại nhưng bám **chỗ sai**; ca C **vừa hẹp vừa trượt tâm về β₁**; ca D **giữ nguyên độ rộng** (không co). Cập nhật mean, SD, MSE, dòng phán định.

### 6.2. Thông điệp sư phạm (tiếng Anh, động):
- "Consistency means the whole sampling distribution collapses onto β₁ as n grows."
- Ca B: "More data does NOT fix endogeneity: the distribution collapses onto the wrong value (β₁ + ρσ_ε/σ_x)."
- Ca C: "Biased at small n, but the bias (c/n) vanishes as n grows — biased yet consistent. (Illustrative; real cases: small-sample IV/MLE bias, dynamic-panel bias ~1/T.)"
- Ca D: "Using a fixed k observations: the center is right but the spread never shrinks — unbiased yet inconsistent."

### 6.3. Trục b₁ CỐ ĐỊNH (theo ca + núm, KHÔNG đổi khi kéo n):
```
SD0 = SD của ca tại n = n_min (ca D: tại k)         // độ rộng lớn nhất trong dải n
center0 = mean của ca tại n = n_min                  // tâm xa nhất (ca C ở n nhỏ)
limit  = giới hạn khi n→∞ (A,C: β₁; B: β₁+ρσ_ε/σ_x; D: β₁)
lo = min(β₁, center0, limit) − 4·SD0
hi = max(β₁, center0, limit) + 4·SD0
```
- Chuẩn hóa **mật độ** (diện tích = 1 ⇒ hẹp thì cao). Trục y trần CỐ ĐỊNH ≈ 1.3 × đỉnh mật độ tại n_min = `1.3 / (SD0·√(2π))`. Ở n lớn, spike chạm trần — đó là biểu hiện co sập.
- Trục x và bin cố định khi kéo n. Tính lại lo/hi/trần/bin khi đổi ca/ρ/c/k/σ_ε.
- **Vẽ đường cong Normal ở độ phân giải mỗi-pixel (≥400 điểm)** để spike hẹp ở n lớn không bị nhảy qua.
- **Hạt giống:** khi vẽ lại histogram (nhả thanh, hoặc đổi ca/núm), gieo lại seed gốc ⇒ lô tại mỗi n là tất định (kéo qua lại không nhấp nháy). "Draw more" tiếp tục dòng PRNG để mịn thêm.

### 6.4. Hành vi đúng kỳ vọng: kéo n lên ⇒ A,B,C hẹp lại (D không); A,C tâm về β₁; B tâm giữ chỗ sai; C tâm trượt từ lệch về β₁ (rõ nhất ở n nhỏ); MSE giảm về 0 (A,C) hoặc về sàn dương (B,D).

---

## 7. KHUNG PHỄU HỘI TỤ (điểm nhấn — làm SAU lõi)
Khung phụ bật/tắt: vẽ "ước lượng theo n" — lấy một mẫu lớn dần, vẽ b₁ tính trên n quan sát đầu theo n (trục hoành n, thang log); nhiều đường (30–50) tạo thành **phễu** bó dần về β₁ (ca A,C), về chỗ sai (ca B), hoặc một **dải không co** (ca D). Vẽ đường β₁ thật và dải ±2·SE(n) lý thuyết co theo 1/√n. Hình ảnh kinh điển nhất của tính vững; cho thấy *tốc độ* √n. Mỗi đường là ước lượng tích lũy trên mẫu LỒNG NHAU lớn dần (khác histogram dùng mẫu độc lập tại mỗi n — ghi chú rõ). **Tính b₁ dọc đường bằng tổng tích lũy (running sums) để mượt và nhanh**, không chạy lại OLS từ đầu ở mỗi n.

## 7b. ĐƯỜNG CONG MSE(n) (cầu nối Lesson 2 — làm SAU)
Khung phụ: vẽ MSE(n) của ca đang chọn theo n (thang log), tách thành `Var(n)` + `Bias(n)²`. Ca A,C: MSE→0. Ca B: → sàn `(ρσ_ε/σ_x)²`. Ca D: → sàn `σ_ε²/(σ_x²(k−3))`. Định lượng "vững ⇔ MSE→0" và nối thẳng phân rã của Lesson 2.

---

## 8. PANEL KIỂM CHỨNG (lưới an toàn)
Nút "Run verification (20,000)" chạy nền `R=20000` tại n hiện tại cho **ca đang chọn** (hiện "computing…"), bảng thực nghiệm vs lý thuyết (3.2):

| Quantity | Empirical | Theoretical | Pass? |
|---|---|---|---|
| Mean b₁ | … | (theo ca: `β₁` / `β₁+ρσ_ε/σ_x` / `β₁+c/n` / `β₁`) | ✓/✗ |
| SD b₁ | … | (theo ca: `σ_ε/(σ_x√(n−3))` … / ca D: `σ_ε/(σ_x√(k−3))`) | ✓/✗ |
| MSE b₁ | … | `Var + Bias²` (theo ca) | ✓/✗ |

- Kèm dòng **"As n → ∞"**: ca đang chọn co về đâu ("collapses to β₁ — consistent" / "collapses to 0.750 ≠ β₁ — inconsistent" / "spread does not shrink — inconsistent").
- **Để chỉ báo "computing…" hiện được:** sau khi đặt chữ, NHƯỜNG luồng sự kiện một nhịp (setTimeout/rAF) rồi mới chạy vòng lặp; lý tưởng là chia nhỏ vòng lặp theo lô để UI không đơ. (JS đơn luồng: vòng lặp đồng bộ ngay sau khi đặt chữ sẽ chặn việc vẽ chữ đó.)
- **Tối ưu ca D:** chỉ sinh k quan sát mỗi lần lặp (phần còn lại không dùng) ⇒ chính xác và không phụ thuộc n.
- Ngưỡng đạt: lệch tương đối ≤ 3%. Ở n lớn (A/B/C) có thể mất ~1–2 giây. Có "✗" ⇒ sai công thức estimator/DGP ⇒ tự sửa rồi chạy lại.

---

## 9. TIÊU CHÍ NGHIỆM THU
**Sư phạm:** kéo n thấy rõ co sập (hoặc không) theo từng ca; đường cong Normal bám histogram ở mọi n; lưới {chệch}×{vững} + phán định đúng cho cả 4 ca; ca C thấy rõ "trượt tâm về β₁"; ca D thấy rõ "không co".
**Kỹ thuật:** mở offline, không lỗi console, không gọi mạng; một file `.html`; PRNG có seed; số 3 chữ số thập phân; ký hiệu đúng 1.1; UI tiếng Anh; trục x cố định khi kéo n; rê n mượt (đường cong tức thì, histogram khi nhả).
**Kinh tế lượng (panel cho cả 4 ca, ít nhất 2 giá trị n):** Mean/SD/MSE khớp lý thuyết (≤3%); ca A,C: SD và MSE giảm khi n tăng; ca B: mean giữ `β₁+ρσ_ε/σ_x` mọi n; ca D: SD không đổi theo n.

---

## 10. TÁI DÙNG ENGINE (tái dùng L1/L2 — không viết mới)
- Sao chép **nguyên văn** từ các module trước: `RNG`, `DGP` (gồm cả nhánh nội sinh ρ của Lesson 1), `OLS` (`fitOLS`). Giữ y hệt.
- Khối `ESTIMATORS` (đều dùng fitOLS):
  - A: `fitOLS(x,y).b1` (DGP ρ=0).
  - B: `fitOLS(x,y).b1` (DGP với ρ≥0.1).
  - C: `fitOLS(x,y).b1 + c/n` (DGP ρ=0).
  - D: `fitOLS(x.slice(0,k), y.slice(0,k)).b1` (DGP ρ=0; **chỉ sinh k quan sát mỗi mẫu** — phần còn lại không dùng, n không ảnh hưởng phân phối lẫn chi phí của D).
- Khối `MSE`: từ mảng b₁ → `{mean, var, bias=mean−β₁, mse=var+bias², rmse}`.
- **Kiến trúc:** ràng buộc "một file offline" cấm import, nên engine lặp lại giữa các file — CHẤP NHẬN ĐƯỢC; giữ đồng bộ bằng cách copy đúng bản đã kiểm chứng.

---

## 11. GHI CHÚ TRUNG THỰC & NỐI VỚI GIÁO TRÌNH
- **Tính vững không nói gì về n hữu hạn của bạn:** một ước lượng vững vẫn có thể tệ ở cỡ mẫu đang có; một ước lượng không vững có thể tạm ổn nếu chệch tiệm cận nhỏ. Vững là lời trấn an về *giới hạn*. (Vì thế Lesson 1–2 vẫn quan trọng.) Đưa câu này thành ghi chú cố định trên giao diện.
- **plim cư xử đẹp** (Slutsky: plim của hàm = hàm của plim), khác với kỳ vọng — đó là vì sao IV, GMM, MLE và **dynamic panel** thường được biện minh bằng *tính vững* chứ không phải không chệch. Ca B (nội sinh) và ca C (chệch-nhưng-vững) là cửa ngõ dẫn tới IV/GMM ở các tập sau của giáo trình. Ghi chú ngắn móc nối này (tiếng Anh).
