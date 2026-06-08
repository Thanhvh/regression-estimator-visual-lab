# Dự án: Regression Estimator Visual Lab

Công cụ trực quan dạy kinh tế lượng tài chính. **Nguồn chân lý cho yêu cầu là `SPEC.md` — đọc nó trước khi làm bất cứ việc gì.**

## Quy tắc bắt buộc (KHÔNG vi phạm)

- **Ký hiệu (đã khóa):** sai số tổng thể = `ε`; phần dư mẫu = `e`; tham số tổng thể = `β₀, β₁`; giá trị ước lượng = `b₀, b₁`. KHÔNG dùng `u` cho sai số. KHÔNG hoán đổi `ε` và `e`. Tô màu phân biệt `ε` và `e` (cặp màu thân thiện người mù màu).
- Mỗi module là **MỘT tệp `.html` tự chứa**, chạy offline khi bấm đúp (`file://`). Toàn bộ CSS + JS nội tuyến.
- **KHÔNG** thư viện ngoài, **KHÔNG** CDN, **KHÔNG** `import` ngoài, **KHÔNG** gọi mạng. Vẽ bằng **SVG/Canvas thuần + JS thuần**.
- Sinh ngẫu nhiên qua **PRNG có seed** (ví dụ mulberry32). KHÔNG dùng `Math.random()` trực tiếp.
- Hiển thị số: **3 chữ số thập phân, dấu chấm** (ví dụ `0.035`).
- Toàn bộ giao diện **tiếng Anh** (thuật ngữ kinh tế lượng theo chuẩn tiếng Anh); ký hiệu toán giữ nguyên Latin/Hy Lạp.
- Mọi công thức kinh tế lượng phải theo đúng **Mục 3 của `SPEC.md`**.

## Phạm vi hiện tại (lát cắt dọc đầu tiên)

- **Chỉ làm bài học "Phân phối lấy mẫu & Tính không chệch"** trong một tệp HTML.
- KHÔNG mở rộng sang module khác. KHÔNG dựng tab rỗng "đang phát triển".
- Vẫn tách code thành các khối rõ ràng để sau này tái dùng: `RNG → DGP → OLS → SAMPLING → CHARTS → UI`.

## Cách làm việc

- Trước khi viết code: đọc `SPEC.md`, trình bày **kế hoạch ngắn gọn** để người dùng duyệt.
- Sau khi xong: **tự chạy panel kiểm chứng** (Mục 7 của `SPEC.md`) với `ρ = 0, 0.3, 0.6`; báo lại bảng kết quả thực nghiệm vs lý thuyết. Nếu có dòng "✗" (lệch > 3%), tự sửa rồi chạy lại đến khi đạt.
- **Commit git** ở mỗi mốc hoàn thành để có thể quay lui.
