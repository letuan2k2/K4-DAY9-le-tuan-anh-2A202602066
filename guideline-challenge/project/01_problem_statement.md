# Problem statement + downstream contract

## Bài toán

Thiết kế bộ gán nhãn cho **đèn giao thông dành cho xe** trong chuỗi ảnh LISA. Với mỗi đầu đèn đủ điều kiện quan sát, người gán nhãn cần khoanh vùng phần vỏ đèn nhìn thấy và xác định trạng thái, hướng biểu tượng, mức độ liên quan với xe mang camera và trường hợp cần kiểm tra lại.

## Downstream contract

1. **Downstream task / model / user là ai?**  
   Dữ liệu phục vụ huấn luyện hoặc đánh giá mô hình nhận thức cho hệ thống hỗ trợ lái/xe tự hành. Mô hình cần phát hiện đầu đèn, đọc trạng thái và cung cấp thông tin để hệ thống phía sau xác định tín hiệu cần theo dõi.

2. **Output annotation nào thực sự cần?**  
   - Geometry: bounding box ôm sát từng đầu đèn nhìn thấy được.
   - Class: `traffic_light`.
   - Attribute `state`: `red`, `yellow`, `green`, `off`, `unknown`.
   - Attribute `relevance`: `relevant`, `not_relevant`, `unknown`.
   - Attribute `direction`: `non_directional`, `left`, `right`, `straight`, `unknown`.
   - Attribute `review`: `none`, `escalate`.

3. **Failure nào gây hậu quả lớn nhất?**  
   Lỗi nghiêm trọng nhất là bỏ sót đầu đèn đủ điều kiện, đọc sai `state`, hoặc gán sai `relevance` theo quy ước dự án. Các lỗi này có thể khiến hệ thống phía sau dùng nhầm tín hiệu. Do v2 quy định gán `relevant` cho mọi đèn thuộc giao lộ hiện tại khi chưa rõ hướng làn, `relevance` không được dùng một mình để ra quyết định lái xe.

4. **Khi ambiguity không resolve được, ai / ở đâu là escalation path?**  
   Nếu đã xác nhận được đầu đèn nhưng không đọc được một thuộc tính, người gán nhãn chọn `unknown`. Nếu dấu hiệu xung đột, không rõ đèn thuộc giao lộ nào hoặc guideline chưa bao phủ trường hợp đó, chọn `review=escalate` để QA owner xử lý.

## Scope

- **Trong scope:** đầu đèn giao thông dành cho xe, nhận diện được trong ảnh và có cả chiều rộng lẫn chiều cao của box lớn hơn 5 px trên ảnh gốc.
- **Ngoài scope:** đèn cho người đi bộ, phản chiếu, hình đèn trên màn hình/biển quảng cáo, cột hoặc thanh treo, vật thể không thể xác nhận là đèn, hoặc box có ít nhất một cạnh không lớn hơn 5 px.
- **Geometry:** box ôm sát phần đầu đèn thực sự nhìn thấy; không bao cột, thanh treo, nền thừa hoặc phần bị che được suy đoán.

## Output chấm được

Blind test sẽ kiểm các quyết định có thể quan sát lại từ CVAT export, gồm:

- LABEL / IGNORE;
- `state`;
- `relevance`;
- `direction`;
- `unknown` khi thiếu bằng chứng;
- geometry của bounding box;
- các trường hợp cần escalation phải được thể hiện bằng rule/attribute có thể truy vết trong output hoặc QA log.

## Dữ liệu và giới hạn

Chỉ sử dụng 30 ảnh trong `data/lisa/`. Đây là các frame liên tiếp của cùng một clip và cùng giao lộ, vì vậy dữ liệu có ít biến thiên về thời tiết, ánh sáng và bố cục. Khi chia example, calibration và blind, cần chọn các frame cách xa nhau nhất có thể. Dù vậy, blind set LISA vẫn có nguy cơ rò rỉ theo thời gian; kết quả chỉ đánh giá khả năng áp dụng guideline trong phạm vi chuỗi này, chưa chứng minh khả năng tổng quát sang giao lộ khác.
