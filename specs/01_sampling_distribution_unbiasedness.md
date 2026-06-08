# BẢN ĐẶC TẢ MODULE — v2
## Sampling Distribution Engine — Bài học: Phân phối lấy mẫu & Tính không chệch của OLS
**Dự án:** Regression Estimator Visual Lab — lát cắt dọc đầu tiên
**Người dùng:** giảng viên kinh tế lượng tài chính (không phải lập trình viên)
**Công cụ thực thi:** Codex / Claude Code

> **Cách dùng:** Dán toàn bộ tệp này vào agent. Agent phải tuân thủ tuyệt đối Mục 1. Trong lát cắt đầu **chỉ làm bài học "Không chệch"**, nhưng code phải tách thành engine dùng lại được (Mục 9) để sau này thêm "Hiệu quả", "Vững" mà không viết lại.

---

## 0. THAY ĐỔI SO VỚI v1 (để truy vết quyết định)

1. **Công thức SD đã sửa lại cho đúng** (đã kiểm chứng bằng mô phỏng 200.000 lần): dùng `SD(b₁) = √(1−ρ²)·σ_ε/(σ_x·√(n−3))`. v1 dùng `σ_ε/(σ_x·√n)` — sai cả ở ρ=0 (thiếu hiệu chỉnh mẫu hữu hạn) lẫn ở ρ>0 (thiếu thừa số √(1−ρ²)). SD vẫn là tiêu chí đạt/bỏ cho **mọi** ρ.
2. **Ô "Tổng thể"** nay vẽ một đám mây minh họa + đường thật `β`, dán nhãn "Tổng thể / cơ chế sinh dữ liệu" (tránh hiểu nhầm là tổng thể hữu hạn).
3. Thêm **seed tái lập** (kèm yêu cầu PRNG tự cài).
4. **Trục histogram cố định** sau khi chọn tham số.
5. Giao diện **phân biệt rõ estimator (quy tắc) vs estimate (giá trị)**.
6. Thêm **chế độ trình chiếu**.
7. **Quy tắc làm tròn** thống nhất (3 chữ số thập phân, dấu chấm).

---

## 1. RÀNG BUỘC CỨNG

### 1.1. Quy ước ký hiệu — ĐÃ KHÓA (nguồn chân lý duy nhất)

Agent **không được tự đổi** sang quy ước khác (ví dụ dùng `u` cho sai số).

| Khái niệm | Ký hiệu | Tiếng Việt | Bản chất |
|---|---|---|---|
| Tham số tổng thể | `β₀, β₁` | Hệ số tổng thể | Cố định, không quan sát được |
| Sai số tổng thể | `ε` | Sai số tổng thể | Lý thuyết, không quan sát được |
| Quy tắc ước lượng | OLS | Quy tắc ước lượng (estimator) | Một công thức |
| Giá trị ước lượng | `b₀, b₁` | Hệ số ước lượng (estimate) | Thay đổi theo từng mẫu (biến ngẫu nhiên) |
| Giá trị dự báo | `ŷ` | Giá trị dự báo | Tính từ mẫu |
| Phần dư mẫu | `e` | Phần dư mẫu | Quan sát được |

Mô hình tổng thể: `yᵢ = β₀ + β₁·xᵢ + εᵢ` — Mô hình mẫu: `ŷᵢ = b₀ + b₁·xᵢ`, `eᵢ = yᵢ − ŷᵢ`.

**Cảnh báo:** quy ước này dùng `ε` cho *sai số* và `e` cho *phần dư* (Greene/Stock–Watson), **ngược với việc dùng `u` cho sai số**. Vì `ε` và `e` nhìn gần giống nhau, giao diện phải tô màu phân biệt rõ (chọn cặp màu thân thiện với người mù màu, ví dụ xanh dương đậm vs cam) và ghi nhãn đầy đủ ở lần đầu xuất hiện trong mỗi màn hình. Mọi chuỗi văn bản/nhãn lấy từ một hằng số `TERMINOLOGY` đặt ở đầu file.

### 1.2. Ràng buộc kỹ thuật

- **Một tệp HTML duy nhất, tự chứa, chạy offline** (bấm đúp, giao thức `file://`). Toàn bộ CSS + JS nội tuyến. Không CDN, không `import` ngoài, không gọi mạng, không thư viện ngoài.
- Vẽ bằng **SVG hoặc Canvas thuần** + JavaScript thuần.
- Chạy trên Chrome, Edge, Firefox, Safari bản hiện hành.
- **Sinh số ngẫu nhiên phải qua một PRNG có seed do agent tự cài** (ví dụ mulberry32). KHÔNG dùng `Math.random()` trực tiếp, vì nó không tái lập được.
- **Hiển thị số:** 3 chữ số thập phân, dấu chấm thập phân (ví dụ `0.035`), không dùng dấu phẩy.
- Giao diện tiếng Anh (thuật ngữ kinh tế lượng theo chuẩn tiếng Anh); ký hiệu toán giữ Latin/Hy Lạp như 1.1.
- Tách code theo khối có chú thích, mô phỏng cấu trúc thư viện tương lai (Mục 9): `TERMINOLOGY → RNG → DGP → OLS → SAMPLING → CHARTS → UI`.
- Nền sáng, phông sans-serif, chữ đủ lớn để đọc từ xa trên máy chiếu.

---

## 2. MỤC TIÊU SƯ PHẠM

Người học (cao học tài chính, đã biết OLS) tự rút ra:
1. `β₁` là con số cố định, không quan sát được của tổng thể.
2. OLS là **quy tắc ước lượng**; mỗi mẫu cho ra một **giá trị ước lượng** `b₁` khác nhau ⇒ `b₁` là biến ngẫu nhiên.
3. Lặp nhiều mẫu ⇒ trung bình các `b₁` tiến về `β₁` ⇒ trực giác **không chệch** `E(b₁)=β₁`.
4. Không chệch là tính chất của *quy tắc*, không phải của một `b₁` đơn lẻ.
5. Vi phạm ngoại sinh ⇒ trung bình `b₁` lệch khỏi `β₁` và **không tự sửa** dù tăng số mẫu.

Câu hỏi học tập: *"Vì sao một hệ số có ý nghĩa thống kê vẫn có thể chệch một cách hệ thống?"*

---

## 3. MÔ HÌNH TOÁN HỌC CHÍNH XÁC

### 3.1. Cơ chế sinh dữ liệu (DGP)

```
xᵢ  ~ Normal(μ_x, σ_x²)            , i = 1..n
zᵢ  ~ Normal(0, 1)
εᵢ  theo 3.2
yᵢ = β₀ + β₁·xᵢ + εᵢ
```
Mặc định (chỉnh được): `μ_x=5`, `σ_x=2`, `β₀=1`, `β₁=0.5`, `σ_ε=1`, `n=30`.

> **Ghi chú khái niệm (đưa vào nhãn giao diện):** "Tổng thể" ở đây là **cơ chế sinh dữ liệu**, không phải một danh sách hữu hạn. Mỗi mẫu là một lần hiện thực hóa mới của cơ chế này.

### 3.2. Ngoại sinh (mặc định) vs Nội sinh

`ρ ∈ [0, 0.8]`, mặc định `ρ=0`.
```
εᵢ = ρ·((xᵢ − μ_x)/σ_x)·σ_ε  +  √(1 − ρ²)·σ_ε·zᵢ
```
Tính chất (agent phải đảm bảo): `Var(εᵢ)=σ_ε²` không đổi theo ρ; `Cov(x,ε)=ρ·σ_ε·σ_x`; ρ=0 ⇒ `E(ε|x)=0`.

### 3.3. OLS (hồi quy đơn)
```
b₁ = Σ(xᵢ−x̄)(yᵢ−ȳ) / Σ(xᵢ−x̄)²
b₀ = ȳ − b₁·x̄
ŷᵢ = b₀ + b₁·xᵢ ;  eᵢ = yᵢ − ŷᵢ
```

### 3.4. Giá trị tham chiếu lý thuyết (ĐÃ KIỂM CHỨNG bằng mô phỏng)

- Trung bình (mọi ρ, chính xác cả trong mẫu hữu hạn): **`E(b₁) = β₁ + ρ·σ_ε/σ_x`**.
- Độ lệch chuẩn (mọi ρ): **`SD(b₁) = √(1−ρ²)·σ_ε / (σ_x·√(n−3))`** (với `n>3`).
  - Suy ra từ `Var(b₁) = (1−ρ²)·σ_ε²·E[1/Σ(xᵢ−x̄)²]`, với x chuẩn thì `E[1/Σ(xᵢ−x̄)²]=1/(σ_x²(n−3))`.
  - Nếu sau này đổi phân phối của `x`, tính lại SD tham chiếu trực tiếp từ dữ liệu: `√( trung_bình_các_mẫu[ (1−ρ²)·σ_ε² / Σ(xᵢ−x̄)² ] )`.

---

## 4. BỐ CỤC MÀN HÌNH (khớp hình phác thảo)

**(A) Ô "Tổng thể / cơ chế sinh dữ liệu" — trái.** Hình chữ nhật ghi mô hình `y=β₀+β₁x+ε`, hiển thị `β₀, β₁` thật. Vẽ một **đám mây điểm minh họa** (mờ) và **đường thẳng thật** `β₀+β₁x` xuyên qua, để thị giác khớp với hình vuông trong phác thảo. Nhãn rõ: đây là cơ chế, không phải danh sách hữu hạn.

**(B) Vùng "Các mẫu" — phải.** Mỗi lần rút hiện một vòng tròn `S₁, S₂, …` kèm nhãn **"S_k → giá trị ước lượng b₁,k = …"** (nhấn estimator→estimate). Kèm ô scatter của mẫu hiện tại: chấm dữ liệu + đường hồi quy mẫu `ŷ` (một màu) cạnh đường tổng thể thật (màu khác).

**(C) Vùng "Phân phối lấy mẫu" — dưới, ngang.** Histogram các `b₁` đã thu thập, với: vạch đứng tại `β₁` thật; vạch đứng tại trung bình `b₁`; ô số liệu trực tiếp (số mẫu, trung bình `b₁`, khoảng cách |trung bình − β₁|).

---

## 5. THAM SỐ & ĐIỀU KHIỂN

Thanh trượt: `β₁` (−2…2, mđ 0.5); `n` (10…500, mđ 30); `σ_ε` (0.2…5, mđ 1).
Công tắc: "Nội sinh (vi phạm ngoại sinh)" → khi bật hiện thanh `ρ` (0…0.8, mđ 0.5).
Nút: "Rút một mẫu" (hoạt cảnh chậm), "Rút 500 mẫu" (lô, không hoạt cảnh), "Đặt lại", "Chạy kiểm chứng (10.000 lần)".
**Chế độ trình chiếu** (công tắc): phóng to chữ và histogram, ẩn mô tả dài, giữ 3 vùng chính + thông điệp ngắn.
**Tùy chọn nâng cao** (thu gọn được): ô "seed", nút "Đặt seed mặc định", nút "Seed ngẫu nhiên".

Quy tắc: đổi bất kỳ tham số nào (`β₁, n, σ_ε`, nội sinh, `ρ`, seed) ⇒ **tự Đặt lại** histogram. Trục histogram được tính lại và **cố định** theo Mục 6.3.

---

## 6. HÀNH VI & TƯƠNG TÁC

### 6.1. Hoạt cảnh "Rút một mẫu" (~1–1.5s): vòng tròn `Sₖ` xuất hiện → ô scatter cập nhật (điểm + đường mẫu + đường thật) → nhãn `b₁,k` → một cột "rơi" vào histogram → vạch trung bình cập nhật.

### 6.2. Thông điệp sư phạm trên màn hình:
- Cố định: *"β₁ là sự thật cố định của tổng thể — không quan sát trực tiếp được."*
- Động: *"OLS là một quy tắc; mỗi mẫu cho một giá trị b₁ khác nhau. Trung bình b₁ hiện tại = … , cách β₁ một khoảng … ."*
- Khi ρ>0: cảnh báo *"Đang vi phạm ngoại sinh: trung bình b₁ sẽ KHÔNG hội tụ về β₁ dù tăng số mẫu."*

### 6.3. Trục histogram CỐ ĐỊNH (quan trọng để giảng)
Sau khi chọn tham số, tính `bias = ρ·σ_ε/σ_x`, `SD = √(1−ρ²)·σ_ε/(σ_x·√(n−3))`, rồi đặt trục `b₁`:
```
range = [ min(β₁, β₁+bias) − 4·SD ,  max(β₁, β₁+bias) + 4·SD ]
```
Trục và số bin **không đổi** trong suốt một kịch bản, để histogram không "nhảy" khi rút thêm mẫu (giúp thấy rõ nó hẹp lại / lệch đi).

### 6.4. Hành vi đúng kỳ vọng: ngoại sinh + nhiều mẫu ⇒ trung bình áp sát `β₁`; tăng `n` ⇒ hẹp lại; tăng `σ_ε` ⇒ rộng ra; ρ>0 ⇒ lệch và không co lại.

---

## 7. PANEL KIỂM CHỨNG TRONG APP (lưới an toàn — thay test dòng lệnh)

Nút "Chạy kiểm chứng (10.000 lần)" chạy nền `R=10000` lần với tham số hiện tại, hiện bảng thực nghiệm vs lý thuyết (Mục 3.4):

| Đại lượng | Thực nghiệm | Lý thuyết | Đạt? |
|---|---|---|---|
| Trung bình `b₁` | … | `β₁ + ρ·σ_ε/σ_x` | ✓/✗ |
| SD của `b₁` | … | `√(1−ρ²)·σ_ε/(σ_x·√(n−3))` | ✓/✗ |

- **Cả hai dòng là tiêu chí đạt/bỏ cho mọi ρ** (đã kiểm chứng khớp <0.5% với mô phỏng). Ngưỡng "Đạt": lệch tương đối ≤ 3%.
- 10.000 lần lặp phải chạy xong dưới ~1 giây.
- Nếu một dòng "✗" ⇒ thường là sai công thức OLS (3.3) hoặc DGP (3.2): agent phải sửa rồi chạy lại đến khi đạt.

---

## 8. TIÊU CHÍ NGHIỆM THU

**Sư phạm:** mỗi vùng (A)(B)(C) trả lời rõ một ý ở Mục 2; phân biệt thị giác `β` vs `b`; phân biệt estimator vs estimate trong nhãn; không có yếu tố thừa.

**Kỹ thuật:** mở offline bằng bấm đúp, không lỗi console, không gọi mạng; một file `.html` tự chứa; PRNG có seed (cùng seed ⇒ cùng kết quả); số 3 chữ số thập phân, dấu chấm; nhãn lấy từ `TERMINOLOGY`, ký hiệu đúng 1.1.

**Kinh tế lượng (phải đạt trong panel kiểm chứng):**
- ρ=0: trung bình `b₁` ≈ `β₁`; SD ≈ `σ_ε/(σ_x·√(n−3))` (lệch ≤3%).
- ρ>0: trung bình `b₁` ≈ `β₁ + ρσ_ε/σ_x`; SD ≈ `√(1−ρ²)·σ_ε/(σ_x·√(n−3))` (lệch ≤3%).
- Tăng `n` ⇒ SD giảm ~`1/√n`; tăng `σ_ε` ⇒ SD tăng tỷ lệ thuận; tăng `ρ` ⇒ SD giảm theo `√(1−ρ²)`.
- Trục histogram cố định trong một kịch bản.

---

## 9. TỔ CHỨC CODE & HƯỚNG MỞ RỘNG

Trong file đơn, tách hàm để **sau này** trích ra `shared/`:
- `RNG`: PRNG có seed → `rand()`, `randn()`.
- `DGP`: `makeSample(params, rng) → {x[], y[], eps[]}`.
- `OLS`: `fitOLS(x, y) → {b0, b1, yhat[], resid[]}`.
- `SAMPLING`: `drawMany(params, R, rng) → b1Array`.
- `CHARTS`: vẽ histogram (trục cố định), scatter — tham số hóa, không gắn cứng dữ liệu.
- `UI`: gắn sự kiện, không chứa logic toán.

Hai bài học kế tiếp **tái dùng đúng engine này** (không viết lại):
- *Hiệu quả:* gọi `drawMany` cho hai cấu hình, vẽ hai histogram chồng cùng trục để so độ phân tán.
- *Vững:* gọi `drawMany` với `n` tăng dần (phân phối co về một điểm); và với một estimator chệch ở mẫu nhỏ nhưng vững (ví dụ `b̃₁ = b₁ + c/n`) để tách "vững" khỏi "không chệch"; và minh họa ρ>0 ⇒ co quanh giá trị sai (mẫu lớn không sửa được nội sinh).

> **Lưu ý phạm vi:** lát cắt đầu **chỉ làm bài học "Không chệch"**. Không dựng tab rỗng "đang phát triển"; chỉ cần code đã tách theo engine ở trên là đủ để mở rộng sau. Chế độ trình chiếu giữ ở mức đơn giản, không để phình scope.

---

## 10. CÂU LỆNH GỢI Ý GIAO CHO AGENT

> "Đọc kỹ bản đặc tả đính kèm. Xây dựng đúng bài học 'Phân phối lấy mẫu & Tính không chệch' trong **một tệp HTML tự chứa, chạy offline, không thư viện ngoài**. Tuân thủ tuyệt đối quy ước ký hiệu Mục 1.1 (sai số = ε, phần dư = e — KHÔNG hoán đổi) và dùng PRNG có seed. Cài đúng các công thức Mục 3. Sau khi xong, tự chạy panel kiểm chứng Mục 7 với vài giá trị ρ (0, 0.3, 0.6) và xác nhận mọi tiêu chí kinh tế lượng Mục 8 đều đạt (lệch ≤3%); nếu chưa đạt, sửa đến khi đạt rồi báo lại bảng kiểm chứng."
