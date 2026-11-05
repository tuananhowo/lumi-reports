# NDEV-01 — xác nhận kiểm tra thủ công

- Ngày ghi nhận: 2026-11-04 (Asia/Ho_Chi_Minh).
- Người dùng cho biết đã tự kiểm tra trên app và thấy thông báo trong hộp thư; yêu cầu cập nhật NDEV-01 thành PASS.
- Đây là kết quả kiểm tra thủ công do người dùng xác nhận. Chưa có screenshot/video, giờ gửi, marker cụ thể, tab và số lần xuất hiện kèm theo trong hội thoại.
- Bằng chứng lần chạy tự động ngày 2026-11-03 được giữ nguyên: system notification có một record, nhưng automation chờ exact text trong tab “Nhà của tôi” bị timeout và API readback không tìm thấy marker. Kết quả cũ được giữ làm lịch sử; trạng thái mới nhất là PASS theo recheck của người dùng.
- Âm thanh chưa được kiểm tra.

## NDEV-07/08/09/10/12 — recheck thủ công

- Ngày ghi nhận: 2026-11-04 (Asia/Ho_Chi_Minh).
- Build recheck do người dùng cung cấp: 2.2.0 (24).
- Người dùng xác nhận đã tự kiểm tra thủ công cả năm case trước đó ở trạng thái NOT RUN/BLOCKED và yêu cầu cập nhật chúng thành PASS.
- Báo cáo ghi các trạng thái này là PASS theo xác nhận của người kiểm thử. Chi tiết từng bước, fixture/tài khoản, API/device log và ảnh/video không được cung cấp kèm theo, nên chưa có bằng chứng độc lập cho từng case.
- Các kết quả tự động ban đầu vẫn được giữ nguyên trong evidence; final-summary.json tiếp tục phản ánh lượt tự động trước recheck.
