# Bàn giao kết quả blind test của Team02

Team02 đã đọc guideline v2 của Team05 và gán nhãn độc lập bốn ảnh `LISA14`, `LISA16`, `LISA22`, `LISA27`. Kết quả được lưu trong `team02-blind.zip`; file `annotations.xml` được giữ lại bên cạnh để hai nhóm tiện kiểm tra nhanh khi cần.

Chúng tôi dùng đúng class `traffic_light`, vẽ Rectangle ở chế độ Shape và điền đủ bốn thuộc tính `state`, `relevance`, `direction`, `review`. Tổng cộng có 20 box. Tất cả box đều vượt ngưỡng 5 px ở cả hai chiều và file export mở được theo cấu trúc CVAT for images 1.1.

Trong quá trình làm, Team02 không cần hỏi thêm Team05. Quy tắc “mỗi vỏ đèn là một object”, ngưỡng kích thước và cách phân biệt đèn tròn với đèn mũi tên đủ rõ để áp dụng trực tiếp. Điểm còn khiến chúng tôi cân nhắc lâu nhất là cách xác định một đầu đèn nhỏ có thuộc giao lộ hiện tại hay không; chi tiết này đã được ghi trong `peer_feedback.md`.

## Xác nhận của Team02

**Blind handoff: PASS.**

Team02 xác nhận có thể dùng guideline và ontology của Team05 để hoàn thành bộ ảnh được giao mà không cần người viết guideline đứng cạnh giải thích. Xác nhận này dựa trên kết quả annotation và quá trình thao tác thực tế của Team02; không phụ thuộc vào `sample_pack.csv`.

Các file bàn giao:

- `team02-blind.zip`: file gửi lại Team05.
- `annotations.xml`: dữ liệu annotation gốc.
- `peer_feedback.md`: nhận xét sau khi hoàn thành.
- `clarification_log.csv`: không có câu hỏi phát sinh nên chỉ có dòng tiêu đề.
