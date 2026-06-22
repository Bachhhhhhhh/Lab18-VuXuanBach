# Reflection: Lakehouse Anti-Patterns

Theo tôi, anti-pattern mà các team dữ liệu dễ vướng phải nhất trong thực tế là **"Vấn đề file nhỏ" (The Small-File Problem)**. 

Khi xây dựng các pipeline dữ liệu near real-time hoặc streaming (như CDC hay ghi log sự kiện từ ứng dụng), dữ liệu thường được ghi xuống liên tục thành những batch cực nhỏ. Mặc dù kiến trúc Lakehouse hỗ trợ ghi nối (append) rất tốt, nhưng nếu bỏ quên việc bảo trì định kỳ, Data Lake sẽ nhanh chóng bị ngập trong hàng triệu file nhỏ lẻ chỉ vài KB.

Việc này gây ra hai hệ luỵ lớn:
1. **Thắt nút cổ chai Metadata:** Các truy vấn tốn quá nhiều thời gian chỉ để mở file và quét metadata thay vì thực sự xử lý nội dung dữ liệu.
2. **Suy giảm hiệu năng nghiêm trọng:** Các bảng tổng hợp chậm đi gấp nhiều lần, gây ảnh hưởng đến báo cáo của bộ phận BI/Analytics.

**Cách khắc phục:** Cần coi việc tối ưu hoá lưu trữ là bắt buộc. Luôn phải lên lịch (schedule) chạy tự động lệnh `OPTIMIZE` (kết hợp `ZORDER` trên các cột hay query) vào giờ thấp điểm để gom các file nhỏ thành file lớn, giúp truy vấn luôn duy trì tốc độ $\ge 3\times$ như trong bài Lab 02.
