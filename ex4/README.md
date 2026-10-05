# Báo Cáo

## 1. Cơ chế hoạt động của `nohup` và các tín hiệu Kill

- **Lệnh `nohup` (No Hangup):** Khi đóng terminal hoặc mất kết nối SSH, hệ điều hành sẽ gửi tín hiệu `SIGHUP` (Signal 1) đến tất cả tiến trình con của session đó để dọn dẹp. `nohup` chặn tín hiệu này, giúp tiến trình tiếp tục chạy nền mà không bị phụ thuộc vào phiên làm việc.
- **Tín hiệu `SIGTERM (15)`:** Tín hiệu yêu cầu kết thúc an toàn (Graceful shutdown). Tiến trình nhận được tín hiệu sẽ kịp giải phóng tài nguyên, đóng file log/kết nối mạng trước khi thoát.
- **Tín hiệu `SIGKILL (9)`:** Tín hiệu chấm dứt bắt buộc do nhân hệ điều hành (Kernel) can thiệp trực tiếp. Tiến trình bị hủy ngay lập tức mà không có cơ hội dọn dẹp bộ nhớ hay lưu trạng thái.

---

## 2. Các bước thực hiện

### Bước 1: Khởi chạy tiến trình nền
```bash
nohup ./loop-monitor.sh > /dev/null 2>&1 &
```

### Bước 2: Kiểm tra PID và log hoạt động
- Lệnh tìm PID:
```bash
pgrep -f loop-monitor.sh
# Kết quả PID: 2180749
```
- Kiểm tra nội dung log liên tục:
```bash
tail -n 5 /tmp/monitor.log
```
*Kết quả mẫu:*
```text
System time: Tue Oct 6 01:20:00 +07 2026
System time: Tue Oct 6 01:20:05 +07 2026
System time: Tue Oct 6 01:20:10 +07 2026
System time: Tue Oct 6 01:20:15 +07 2026
System time: Tue Oct 6 01:20:20 +07 2026
```

### Bước 3: Dừng tiến trình
Thực hiện dừng tiến trình bằng tín hiệu `SIGTERM (15)`:
```bash
kill -15 12345
```

Kiểm tra lại bằng `ps aux`:
```bash
ps aux | grep loop-monitor.sh
# Không còn tiến trình loop-monitor.sh nào đang chạy.
```