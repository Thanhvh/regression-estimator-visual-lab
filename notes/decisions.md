# Nhật ký quyết định — Regression Estimator Visual Lab

Ghi lại CÁC QUYẾT ĐỊNH và LÝ DO, để không lặp lại tranh luận cũ và để các module sau nhất quán. Thêm dòng mới ở cuối khi có quyết định mới.

## Công nghệ & đóng gói
- **Web tĩnh, một tệp HTML tự chứa, chạy offline; không thư viện ngoài.** Lý do: câu hỏi "đóng gói vs cloud" tự tan biến (mở offline bằng bấm đúp + host GitHub Pages miễn phí, vĩnh viễn, không "ngủ"); khớp bản chất animation; gánh bảo trì ~0 cho người không phải dev. Khi nào cân nhắc Streamlit: chỉ khi có module cần tính toán nặng khó làm bằng JS, hoặc cần tái dùng code R/Python sẵn có.
- **Mỗi module một file; engine (RNG/DGP/OLS/SAMPLING/CHARTS/UI) tách thành khối trong file.** Chưa tạo `shared/` trước khi có module thứ hai — tránh trừu tượng hóa sớm (chọn abstraction trước khi có đủ ca cụ thể = đoán sai).

## Quy ước ký hiệu (đã khóa — theo Greene)
- Sai số tổng thể = `ε`; phần dư mẫu = `e`; tham số tổng thể = `β₀, β₁`; giá trị ước lượng = `b₀, b₁`.
- Lý do: khớp cách vẽ tay (`β`/`b`), là một hệ quy ước kinh điển nhất quán. **ĐẢO so với bản nháp cũ** (trước để sai số = `e`, phần dư = `u`).
- Cảnh báo lâu dài: quy ước này ngược với việc dùng `u` cho sai số (Wooldridge), nên AI agent dễ tự lật → phải khóa cứng trong `CLAUDE.md` và lặp lại trong mỗi spec.

## Kinh tế lượng (đã kiểm chứng bằng mô phỏng)
- Độ chệch do nội sinh: `E(b₁) = β₁ + ρ·σ_ε/σ_x` — chính xác cả trong mẫu hữu hạn (không chỉ tiệm cận).
- Độ lệch chuẩn: `SD(b₁) = √(1−ρ²)·σ_ε/(σ_x·√(n−3))`. Đã thay công thức sai ở v1 (`σ_ε/(σ_x·√n)`, thiếu cả thừa số `√(1−ρ²)` lẫn hiệu chỉnh mẫu hữu hạn `n−3`).
- SD vẫn là tiêu chí đạt/bỏ cho **mọi** ρ (không hạ xuống mức tham khảo), vì công thức tổng quát khớp mô phỏng < 0.5%.

## Phạm vi & quy trình
- **Lát cắt dọc đầu tiên: chỉ bài học "Không chệch".** Không dựng tab rỗng "đang phát triển" (đi ngược nguyên tắc lát cắt mỏng). Yêu cầu thực chất là code tách thành engine để tái dùng — đã có trong spec.
- Quy trình: spec → HTML → kiểm chứng trong app → chạy offline → GitHub Pages → dùng thử → MỚI viết spec kế tiếp.
- Cấu trúc dự án rút ra từ thực tế **sau** module thứ hai, không thiết kế kiến trúc lớn trước.

## Quản trị tệp
- Nguồn chân lý cho yêu cầu: `SPEC.md` (trong `specs/`). Nguyên tắc lâu dài: `CLAUDE.md` (Claude Code tự đọc mỗi phiên). Lịch sử phiên bản: dùng **git** (không tạo thư mục lưu trữ thủ công).
