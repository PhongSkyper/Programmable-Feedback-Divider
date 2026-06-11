# Programmable Frequency Divider

Dưới đây là tóm tắt chi tiết cho báo cáo dự án "Thiết kế bộ chia tần số lập trình được" (Programmable Frequency Divider):

### 1. Thông tin chung về dự án

* **Tên dự án:** Thiết kế bộ chia tần số lập trình được (Programmable Frequency Divider).
* **Môn học:** Thiết kế vi mạch tương tự (EE3207) tại Đại học Bách Khoa TP.HCM.
* **Mục tiêu chính:** Thiết kế một bộ chia tần số trên đường hồi tiếp (Feedback Divider) cho hệ thống Vòng khóa pha (PLL), có khả năng lập trình và hoạt động ở tần số cực cao (5 GHz).

### 2. Yêu cầu và thông số kỹ thuật

* **Tần số ngõ vào:** 5 GHz.
* **Công nghệ:** Sử dụng thư viện công nghệ gpdk045/gsclib045 (chuẩn CMOS 45nm).
* **Điều kiện hoạt động:** Mạch phải được kiểm tra qua các điều kiện PVT (Process, Voltage, Temperature) ở 3 góc (corners) chính: FFHH (Nhanh), TTNN (Điển hình), và SSLL (Chậm).
* **Ràng buộc thiết kế:** Chỉ được phép sử dụng cấu trúc logic CMOS tĩnh (không dùng linh kiện lý tưởng hoặc kiến trúc TSPC) và phải đảm bảo các yêu cầu khắt khe về thời gian chuyển mạch (Slew rate < 10% UI) cũng như chu kỳ nhiệm vụ (Duty cycle 50%) ở tải 20 fF.

### 3. Nội dung thực hiện (Gồm 3 nhiệm vụ trọng tâm)

Dự án áp dụng kiến trúc Modular Multi-Modulus Divider (MMD) để giải quyết nút thắt về tốc độ thay vì dùng bộ đếm đồng bộ truyền thống.

**Nhiệm vụ 1: Thiết kế khối chia tần số cơ bản 2/3 (Task 1)**

* Khối này bao gồm 2 D-Flip Flop (DFF) loại chốt cạnh xuống và mạng logic tổ hợp tạo thành vòng lặp hồi tiếp.
* Mạch hoạt động chia 2 ở chế độ mặc định và có khả năng "nuốt xung" để thực hiện chia 3 thông qua các bit điều khiển.
* Nhóm đã tiến hành tinh chỉnh kích thước transistor (Sizing) theo nguyên lý nỗ lực logic để đảm bảo mạch hoạt động đúng ở 5 GHz.
* Kết quả cho thấy mạch hoạt động ổn định ở các góc FFHH và TTNN, tuy nhiên tại góc chậm SSLL, mạch đã tiến sát tới giới hạn vận hành của công nghệ 45nm (phát hiện lỗi vi phạm Setup Time cực nhỏ).

**Nhiệm vụ 2: Thiết kế bộ chia tần số 4/5/6/7 (Task 2)**

* Hệ thống được mở rộng bằng cách ghép nối tiếp (cascade) 2 khối chia 2/3 từ Nhiệm vụ 1.
* Bộ chia được điều khiển thông qua 2 bit ($P_0, P_1$) để cung cấp các hệ số chia: 4, 5, 6, và 7.
* Tín hiệu hoạt động dựa trên đường truyền xung nhịp đi tới (Forward Path) và tín hiệu điều khiển hồi tiếp ngược lại (Backward Path).
* Mô phỏng Transient cho thấy mạch tạo ra chu kỳ đầu ra hoàn toàn chính xác với lý thuyết. Nhóm cũng đã phân tích chi tiết giới hạn thời gian (Timing Margin) và chỉ ra rằng độ trễ của đường hồi tiếp chính là điểm nghẽn của hệ thống.

**Nhiệm vụ 3: Thiết kế bộ chia toàn dải 2/3/4/5/6/7 (Task 3)**

* Để khắc phục giới hạn của bộ chia nối tiếp (không thể chia các hệ số nhỏ như 2, 3), nhóm đã đề xuất bổ sung một khối hợp kênh (Multiplexer 3-to-1) ở ngõ ra và một cổng logic OR ở đường hồi tiếp.
* Sự cải tiến này giúp đi tắt tín hiệu ngõ ra (bypass) và cô lập mạch vòng hồi tiếp, cho phép mạch linh hoạt chuyển đổi số tầng hoạt động.
* Kết quả thu được: Mạch mô phỏng đáp ứng mượt mà toàn bộ dải chia từ 2 đến 7 mà không bị mất xung ở tần số 5 GHz. Tuy nhiên, việc đi qua MUX gây ra sự suy hao hiệu năng nhất định (làm tăng độ trễ lan truyền $t_{pd}$).

### 4. Kết luận và Hướng phát triển

* **Thành tựu:** Nhóm đã hoàn thành trọn vẹn cả 3 nhiệm vụ, kiểm chứng được sự hoạt động của kiến trúc MMD từ cơ bản đến nâng cao dưới tần số 5 GHz khắt khe, đồng thời chứng minh được sự hiểu biết sâu sắc về phân tích thời gian tĩnh (STA).
* **Hạn chế:** Vi phạm Setup Time ở góc SSLL, MUX tích lũy thêm độ trễ truyền dẫn, và công suất tiêu thụ năng lượng chuyển mạch còn lớn.
* **Hướng tối ưu trong tương lai:** 
  * Thực hiện Layout vật lý và tinh chỉnh Sizing chuyên sâu để triệt tiêu lỗi timing.
  * Mở rộng dải chia linh hoạt hơn (ví dụ 1 đến 15) thông qua kiến trúc cây MUX.
  * Thay thế CMOS tĩnh bằng kiến trúc CML (Current Mode Logic) để đạt tần số cao hơn và làm sắc sườn tín hiệu.