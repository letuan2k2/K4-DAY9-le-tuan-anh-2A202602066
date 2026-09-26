# Nhật ký điều chỉnh guideline

Mỗi phiên bản của `02_guideline.md` được ghi đúng một dòng. Nhật ký này chỉ mô tả thay đổi của guideline.

| Version | Đổi gì | Vì sao | Bằng chứng |
|---|---|---|---|
| v1 | Bản hướng dẫn đầu tiên: xác định phạm vi đèn giao thông dành cho xe, cách vẽ box, các thuộc tính `state`, `direction`, `relevance`, `review` và cách dùng `unknown`/`escalate`. Chưa có ngưỡng kích thước cố định; khi không rõ đèn nào liên quan đến xe thì dùng `relevance=unknown`. | Tạo quy tắc ban đầu để nhóm thực hiện annotation ảnh tĩnh. | Bản `02_guideline.md` v1 trong lịch sử Git. |
| v2 | Bổ sung `state=off` và `direction=non_directional`; chỉ label đầu đèn nhận diện được khi cả hai cạnh box >5 px; quy định tất cả đèn thuộc giao lộ hiện tại là `relevant` khi không rõ hướng đi của làn; làm rõ đèn tròn không đồng nghĩa `straight`; giữ quyết định độc lập cho từng frame LISA và bổ sung rule cho object nhỏ/mờ, nhiều đầu đèn, chuyển pha tín hiệu và trường hợp cần escalate. | Giảm bất đồng ở các case ảnh nhỏ/mờ, nhiều tín hiệu cùng giao lộ và thiếu bằng chứng về hướng làn; tránh suy đoán state, direction hoặc relevance ngoài thông tin có trong ảnh hiện tại. | Các mục 3–6 và phần ví dụ trong `02_guideline.md` v2. |
