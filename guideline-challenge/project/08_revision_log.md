# Nhật ký điều chỉnh guideline

Mỗi phiên bản của `02_guideline.md` được ghi đúng một dòng. Nhật ký này chỉ mô tả thay đổi của guideline.

| Version | Đổi gì | Vì sao | Bằng chứng |
|---|---|---|---|
| v1 | Bản hướng dẫn đầu tiên: xác định phạm vi đèn giao thông dành cho xe, cách vẽ box, các thuộc tính `state`, `direction`, `relevance`, `review` và cách dùng `unknown`/`escalate`. Chưa có ngưỡng kích thước cố định; khi không rõ đèn nào liên quan đến xe thì dùng `relevance=unknown`. | Tạo quy tắc ban đầu để nhóm thực hiện annotation ảnh tĩnh. | Bản `02_guideline.md` v1 trong lịch sử Git. |
| v2 | Bổ sung `state=off` và `direction=non_directional`; đặt ngưỡng LABEL là cả hai cạnh box >5 px; quy định mọi đèn thuộc giao lộ hiện tại là `relevant` khi chưa rõ hướng làn; làm rõ quy tắc instance, tight visible box, inclusion/exclusion, visibility/occlusion, bốn quyết định LABEL–IGNORE–UNKNOWN–ESCALATE trong CVAT, xử lý độc lập từng frame LISA và expected output cho các edge case. | Giảm bất đồng ở object nhỏ/mờ, nhiều vỏ đèn, chuyển pha tín hiệu và thiếu bằng chứng về hướng làn; bảo đảm mọi quyết định cần review đều nhìn thấy được trong CVAT export. | Các mục 2–10 trong `02_guideline.md` v2. |
| v3 | Bổ sung tiêu chí phân biệt giao lộ hiện tại với giao lộ phía xa; làm rõ quy tắc Temporal Context; quy định bỏ qua (IGNORE) đèn vàng cảnh báo nhấp nháy đơn lẻ; nhấn mạnh kiểm tra cờ `review=escalate`. | Phản hồi của Team02 trong `peer_feedback.md` và câu hỏi clarification trong `clarification_log.csv` về nhận diện giao lộ và đèn cảnh báo đơn lẻ. | `peer_feedback.md`, `clarification_log.csv`, `gts_summary.md` (GTS 97.0). |
