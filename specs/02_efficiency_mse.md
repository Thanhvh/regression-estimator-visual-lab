# BẢN ĐẶC TẢ MODULE — Lesson 2
## Efficiency, the bias–variance grid, and MSE
**Dự án:** Regression Estimator Visual Lab — module thứ hai
**Tệp đầu ra:** `02_efficiency_mse.html` (ở gốc repo)

> Tái dùng engine đã kiểm chứng của Lesson 1 (Mục 10). Làm phần LÕI trước (Mục 1–6, 8–10); lớp hiệu ứng (Mục 7), khung chùm đường (Mục 7b) và phần mở rộng co rút (Mục 11) làm SAU khi lõi đã kiểm chứng đạt.

---

## 0. GHI CHÚ THIẾT KẾ QUAN TRỌNG (đã kiểm chứng bằng mô phỏng)

Các quyết định then chốt và lý do, để người/agent đọc sau không vô tình phá vỡ:
1. **`m_min = 10`** và **R kiểm chứng = 20.000**: ở mẫu con rất nhỏ, hệ số dốc OLS có đuôi nặng (mômen bậc 4 phân kỳ khi m ≤ 5) ⇒ ước lượng SD dao động mạnh giữa các lần chạy ⇒ panel báo "✗" giả dù code đúng. Mô phỏng: m=5 lệch tới ~6.4%; m≥8 mới an toàn; chọn m=10 cho biên an toàn.
2. **Thuật ngữ:** "efficiency" (Gauss–Markov) chỉ định nghĩa trong nhóm KHÔNG chệch. Trục độ phân tán gọi là **"precision / variance"**; "efficient/inefficient" chỉ dùng khi không chệch; có dòng ghi chú phân biệt trên giao diện.
3. **Hiển thị:** thang thanh MSE **cố định** (không autoscale); hai histogram chồng dùng **tô bán trong suốt + cùng bin + cùng chuẩn hóa** để cả hai đều thấy.
4. Hiển thị thêm số **relative efficiency** (khái niệm kinh điển).
5. Mẫu con = **m quan sát đầu** (xác định, tái lập được); tên hiển thị mô tả; màu A/B thân thiện người mù màu.

---

## 1. RÀNG BUỘC CỨNG (giống Lesson 1)

### 1.1. Ký hiệu — ĐÃ KHÓA
- Sai số tổng thể = `ε`; phần dư mẫu = `e`; tham số = `β₀, β₁`; ước lượng = `b₀, b₁`. KHÔNG dùng `u` cho sai số; KHÔNG hoán đổi `ε`/`e`.
- Hai ước lượng so sánh hiển thị tên mô tả: **"Full-sample OLS"** (gọi tắt A) và **"Estimator B"** (gọi tắt B); hệ số dốc ghi `b₁(A)`, `b₁(B)`.
- Màu phân biệt A vs B phải **thân thiện người mù màu** (ví dụ xanh dương đậm cho A, cam cho B); dùng nhất quán cho histogram, vạch trung bình, thanh MSE.

### 1.2. Kỹ thuật (giống Lesson 1)
- MỘT tệp `.html` tự chứa, chạy offline (`file://`), CSS + JS nội tuyến.
- KHÔNG thư viện ngoài/CDN/import ngoài/gọi mạng/build. Vẽ bằng SVG/Canvas thuần.
- PRNG có seed (mulberry32). KHÔNG dùng `Math.random()`.
- Số: 3 chữ số thập phân, dấu chấm. Giao diện **tiếng Anh**; ký hiệu toán giữ Latin/Hy Lạp.
- Tách code: `RNG → DGP → OLS → SAMPLING → ESTIMATORS → CHARTS → MSE → UI`.

---

## 2. MỤC TIÊU SƯ PHẠM

1. **Chệch** = tâm phân phối lấy mẫu lệch khỏi `β₁`. **Phương sai/độ chính xác** = phân phối hẹp hay rộng. Hai chiều độc lập.
2. Một ước lượng rơi vào bất kỳ ô nào của lưới 2×2: {không chệch, chệch} × {phương sai nhỏ, phương sai lớn}.
3. **Hiệu quả là so sánh tương đối** — chỉ thấy khi đặt hai phân phối lên cùng một trục. (Định nghĩa chặt: trong nhóm KHÔNG chệch, ước lượng *hiệu quả* là cái phương sai nhỏ nhất — BLUE.)
4. **MSE = Var + Bias²** gộp hai chiều thành một số; cả chệch lẫn phương sai đều làm MSE tăng.
5. (Mở rộng, Mục 11) Một chút chệch đôi khi HẠ được MSE — đánh đổi chệch–phương sai.

Câu hỏi học tập: *"Giữa một ước lượng không chệch nhưng phân tán rộng và một ước lượng chệch nhẹ nhưng rất hẹp, cái nào 'tốt hơn'?"*

---

## 3. MÔ HÌNH TOÁN HỌC (ĐÃ KIỂM CHỨNG bằng mô phỏng)

### 3.1. DGP — giống Lesson 1, lõi cố định ngoại sinh (ρ = 0)
```
xᵢ ~ Normal(μ_x, σ_x²);  εᵢ ~ Normal(0, σ_ε²);  yᵢ = β₀ + β₁·xᵢ + εᵢ
```
Mặc định: `μ_x=5, σ_x=2, β₀=1, β₁=0.5, σ_ε=1, n=30`. (Giữ ρ=0 để cô lập khái niệm hiệu quả; chệch ở đây do người dùng đặt qua δ.)

### 3.2. Hai ước lượng trên CÙNG một mẫu
- **A = OLS trên toàn bộ n quan sát** (mốc — không chệch, BLUE). Công thức OLS như Lesson 1 Mục 3.3.
- **B = OLS trên `m` quan sát ĐẦU của mẫu (m ≤ n), cộng độ lệch cố định `δ`.** Lấy "m quan sát đầu" cho xác định/tái lập (các quan sát vốn iid nên đây là mẫu con hợp lệ). Hai núm độc lập:
  - **Núm phương sai = `m`**: m nhỏ ⇒ B *phương sai lớn hơn* (rộng hơn) vì **vứt bớt thông tin**. Minh họa Gauss–Markov: OLS-đầy-đủ là ước lượng tuyến tính không chệch có phương sai nhỏ nhất, nên thắng B (B cũng tuyến tính & không chệch khi δ=0).
  - **Núm chệch = `δ`** (*độ lệch cộng thêm, cố ý, để minh họa*): dịch tâm B đi `δ`, KHÔNG đổi phương sai. Ghi rõ trên UI đây là độ lệch minh họa nhằm tách trục chệch; nguồn chệch *thật* (nội sinh) đã trình bày ở Lesson 1.

### 3.3. Giá trị tham chiếu lý thuyết (cho panel; với `m > 3`, `x` chuẩn)
- **A:** `E(b₁(A)) = β₁`; `SD(b₁(A)) = σ_ε/(σ_x·√(n−3))`; `Bias_A = 0`; `MSE_A = Var_A = σ_ε²/(σ_x²(n−3))`.
- **B:** `E(b₁(B)) = β₁ + δ`; `SD(b₁(B)) = σ_ε/(σ_x·√(m−3))`; `Bias_B = δ`; `Var_B = σ_ε²/(σ_x²(m−3))`; **`MSE_B = Var_B + δ²`**.
- Đẳng thức luôn đúng: `MSE_emp = Var_emp + Bias_emp²`.
- **Hiệu quả tương đối (relative efficiency):** khi δ=0, `RE = Var_A/Var_B = (m−3)/(n−3)`; tổng quát (mọi δ) `RE = MSE_A/MSE_B`.

### 3.4. Bốn ô của lưới 2×2 (đối chiếu sơ đồ tay)
| δ | m | Vị trí của B so với A |
|---|---|---|
| 0 | = n | Trùng A — không chệch, phương sai nhỏ (= hiệu quả) |
| 0 | < n | Không chệch, **phương sai lớn** (kém hiệu quả) |
| ≠ 0 | = n | **Chệch**, phương sai nhỏ |
| ≠ 0 | < n | **Chệch**, phương sai lớn |

---

## 4. BỐ CỤC MÀN HÌNH

**(A) Vùng phân phối chồng — trung tâm.** Hai histogram của `b₁(A)` và `b₁(B)` trên **cùng một trục `b₁` CỐ ĐỊNH** (Mục 6.3). Bắt buộc: **cùng bề rộng bin, cùng chuẩn hóa, tô bán trong suốt (alpha ~0.5) hoặc dùng viền** để cả hai đều thấy khi chồng nhau (histogram hẹp sẽ cao, rộng sẽ thấp — đó là điều cần thấy). Vạch đứng: `β₁` thật (đậm, "true β₁"), `mean b₁(A)`, `mean b₁(B)`.

**(B) Nhãn ô 2×2 + ghi chú thuật ngữ.** Hiển thị động vị trí B, ví dụ "Estimator B: **Unbiased · Higher variance**". Lưới 2×2 nhỏ tô sáng ô hiện hành. Một dòng ghi chú cố định: *"Efficiency (Gauss–Markov) is defined among unbiased estimators; for biased estimators compare variance or MSE."*

**(C) Đồng hồ MSE — phải.** Cho A và B: `Var`, `Bias²`, `MSE = Var + Bias²`, `RMSE`, và **Relative efficiency** (Mục 3.3). Mỗi ước lượng là **một thanh xếp chồng** (`Var` + `Bias²`) trên **thang Y CỐ ĐỊNH** (không autoscale — chọn trần đủ lớn theo toàn dải núm, ví dụ theo MSE_B lớn nhất khi m=m_min và |δ|=0.5), để thấy MSE/thành phần lớn dần khi chỉnh núm.

---

## 5. THAM SỐ & ĐIỀU KHIỂN (nhãn tiếng Anh)

Thanh trượt:
- `δ` ("B bias offset"): −0.5 … 0.5 (mặc định 0).
- `m` ("B subsample size"): **10 … n** (mặc định = n; ràng buộc 10 ≤ m ≤ n).
- `n` ("sample size"): 10 … 500 (mặc định 30).
- `σ_ε` ("noise"): 0.2 … 5 (mặc định 1).

Nút: "Draw one sample", "Draw 500", "Reset", "Run verification (20,000)".
Hiệu ứng (Mục 7): "Animation speed" + "Pause".
Quy tắc: đổi `δ, m, n, σ_ε`, seed ⇒ tự Reset hai histogram + tính lại trục cố định (6.3). Khi `n` đổi, kẹp `m` về [10, n] (nếu n=10 thì m=10).

---

## 6. HÀNH VI & TƯƠNG TÁC

### 6.1. Mỗi lần rút mẫu: tính `b₁(A)` trên toàn mẫu, `b₁(B)` trên m quan sát đầu của CHÍNH mẫu đó (+δ). Thêm vào histogram tương ứng; cập nhật vạch trung bình, đồng hồ MSE, số relative efficiency.

### 6.2. Thông điệp sư phạm (tiếng Anh, động):
- "Efficiency is a *comparison*: the narrower distribution has smaller sampling variance."
- δ≠0: "Estimator B is biased: its center sits `δ` from true β₁ — more samples will not fix this."
- m<n: "Estimator B uses only `m` of `n` observations, so it is unbiased but has larger variance than full-sample OLS."

### 6.3. Trục `b₁` CỐ ĐỊNH:
```
SD_A = σ_ε/(σ_x·√(n−3));  SD_B = σ_ε/(σ_x·√(m−3))
center_min = min(β₁, β₁+δ);  center_max = max(β₁, β₁+δ)
range = [ center_min − 4·max(SD_A,SD_B), center_max + 4·max(SD_A,SD_B) ]
```
Trục và số bin không đổi trong một kịch bản.

### 6.4. Hành vi đúng kỳ vọng: m giảm ⇒ B rộng ra (A đứng yên), RE giảm; δ tăng ⇒ tâm B dịch, độ rộng B không đổi; cả hai làm MSE_B tăng (thấy thành phần nào tăng); δ=0, m=n ⇒ hai phân phối trùng, RE=1.

---

## 7. LỚP HIỆU ỨNG (làm SAU khi lõi đạt — lớp tách biệt)
- **"Draw one sample":** điểm `b₁(A)` và `b₁(B)` **rơi** xuống đáp vào trục (ease-out); cột histogram tương ứng **nảy nhẹ**. Hai điểm rơi đồng thời để so sánh.
- **"Draw 500":** TẮT hoạt cảnh, dồn nhanh.
- **"Animation speed" + "Pause"** để dừng đúng lúc giảng.
- Tôn trọng `prefers-reduced-motion`: nếu bật ⇒ hiện ngay.
- `requestAnimationFrame`, JS thuần. Lớp hiệu ứng KHÔNG đụng giá trị tính toán.

## 7b. KHUNG CHÙM ĐƯỜNG HỒI QUY (tùy chọn — hình ảnh kinh điển về hiệu quả; làm sau lõi)
Một khung phụ bật/tắt: vẽ chồng `N` (ví dụ 50) đường hồi quy mẫu của A (màu A, mảnh, trong suốt) và của B (màu B), cùng đường tổng thể thật `β₀+β₁x`. Đường của A bó sát quanh đường thật; đường của B (m nhỏ) xòe rộng; nếu δ≠0, chùm B lệch hệ thống. Đây là cách "thấy" hiệu quả qua độ "lắc" của đường hồi quy, bổ trợ histogram.

---

## 8. PANEL KIỂM CHỨNG (lưới an toàn)
Nút "Run verification (20,000)" chạy nền `R=20000`, hiện bảng thực nghiệm vs lý thuyết (3.3) cho CẢ HAI ước lượng:

| Quantity | Empirical | Theoretical | Pass? |
|---|---|---|---|
| Mean b₁(A) | … | `β₁` | ✓/✗ |
| SD b₁(A) | … | `σ_ε/(σ_x·√(n−3))` | ✓/✗ |
| Mean b₁(B) | … | `β₁ + δ` | ✓/✗ |
| SD b₁(B) | … | `σ_ε/(σ_x·√(m−3))` | ✓/✗ |
| MSE identity (B) | `MSE_emp` | `Var_emp + Bias_emp²` | ✓/✗ |
| MSE b₁(B) | … | `σ_ε²/(σ_x²(m−3)) + δ²` | ✓/✗ |
| Relative efficiency | `Var_A/Var_B` (δ=0) | `(m−3)/(n−3)` | ✓/✗ |

Ngưỡng đạt: lệch tương đối ≤ 3%. Chạy < ~1–2 giây. Có "✗" ⇒ sai công thức estimator/DGP ⇒ tự sửa rồi chạy lại. (Ghi chú: nhờ `m_min=10` và `R=20.000`, dao động Monte Carlo của SD ở mẫu con đã nằm an toàn dưới ngưỡng.)

---

## 9. TIÊU CHÍ NGHIỆM THU
**Sư phạm:** chệch (tâm) tách rõ khỏi phương sai (độ rộng); nhãn ô 2×2 đúng theo δ, m; ghi chú thuật ngữ hiệu quả có mặt; đồng hồ MSE phân rã Var + Bias²; số relative efficiency đúng.
**Kỹ thuật:** mở offline, không lỗi console, không gọi mạng; một file `.html`; PRNG có seed; số 3 chữ số thập phân; ký hiệu đúng 1.1; UI tiếng Anh; hai histogram đều thấy khi chồng; thang thanh MSE cố định.
**Kinh tế lượng (phải đạt trong panel):** cả 7 dòng bảng Mục 8 đạt (≤3%); m giảm ⇒ SD_B tăng theo `1/√(m−3)`; δ chỉ dịch tâm B; trục histogram cố định.

---

## 10. TÁI DÙNG ENGINE (module thứ hai — engine lộ diện)
- Sao chép **nguyên văn** từ `01_sampling_distribution_unbiasedness.html`: `RNG`, `DGP`, `OLS` (`fitOLS`). Giữ y hệt.
- Khối `ESTIMATORS`: `estimatorA(s) = fitOLS(s.x, s.y).b1`; `estimatorB(s, m, delta) = fitOLS(s.x.slice(0,m), s.y.slice(0,m)).b1 + delta`.
- Khối `MSE`: từ mảng b₁ → `{mean, var, bias=mean−β₁, mse=var+bias², rmse}`; và `relEff(varA,varB)` / `relEffMSE(mseA,mseB)`.
- **Kiến trúc:** ràng buộc "một file offline" cấm `import`, nên engine **lặp lại** giữa các file — CHẤP NHẬN ĐƯỢC; đừng tạo `shared/` với import (phá chạy-offline). Giữ đồng bộ bằng cách copy đúng bản đã kiểm chứng.

---

## 11. PHẦN MỞ RỘNG TÙY CHỌN — "Bias–variance tradeoff" (làm SAU)
Công tắc "Shrinkage demo" đổi B thành **co rút** `b₁(B) = c · b₁(A)`, thanh `c` (0 … 1.2, mặc định 1).
- `E(b₁(B)) = c·β₁` ⇒ `Bias = (c−1)·β₁`; `SD = c·σ_ε/(σ_x·√(n−3))`; `MSE = c²·Var_A + (1−c)²·β₁²`.
- **c tối ưu** `c* = β₁²/(β₁² + Var_A)` cho `MSE_B < MSE_A` — minh họa "chệch một chút có thể HẠ MSE". (Đã kiểm chứng: tại c≈0.96, MSE_B=0.0089 < MSE_A=0.0093 với mặc định.)
- Để cú "dip" thấy rõ, chế độ này nên mặc định `σ_ε≈3, n≈20` (Var_A lớn so với β₁²). Panel kiểm chứng dùng các công thức trên.
- Khái niệm khác (không chệch KHÔNG tự động tốt hơn) ⇒ để TÁCH khỏi lưới 2×2; làm sau khi lõi Mục 1–10 đạt.
