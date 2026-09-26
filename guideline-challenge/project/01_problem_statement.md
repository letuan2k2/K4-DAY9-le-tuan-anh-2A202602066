# Problem statement + downstream contract

## Bài toán

Thiết kế annotation cho **vehicle traffic light trong ảnh giao thông**, tập trung vào các cảnh có nhiều cụm đèn hoặc khó xác định đèn nào liên quan đến hướng di chuyển của **ego vehicle**. Với mỗi traffic light đủ điều kiện quan sát, annotator cần xác định vị trí, trạng thái tín hiệu và mức độ liên quan với ego vehicle theo một guideline thống nhất.

## Downstream contract

1. **Downstream task / model / user là ai?**  
   Dữ liệu annotation phục vụ huấn luyện hoặc đánh giá perception model cho hệ thống hỗ trợ lái/xe tự hành, nhằm nhận biết traffic light và xác định tín hiệu nào có khả năng điều khiển hướng di chuyển của ego vehicle.

2. **Output annotation nào thực sự cần?**  
   - Geometry: bounding box cho từng vehicle traffic light nhìn thấy được.  
   - Class: `traffic_light`.  
   - Attribute `state`: `red`, `yellow`, `green`, `unknown`.  
   - Attribute `relevance`: `relevant`, `not_relevant`, `unknown`.  
   - Attribute `direction`: `straight`, `left`, `right`, `unknown`.

3. **Failure nào gây hậu quả lớn nhất?**  
   Critical failure là **gán một traffic light không điều khiển ego vehicle thành `relevant`, hoặc bỏ sót/đánh sai traffic light thực sự liên quan**, vì lỗi này có thể làm downstream system sử dụng sai tín hiệu giao thông.

4. **Khi ambiguity không resolve được, ai / ở đâu là escalation path?**  
   Khi ảnh không cung cấp đủ bằng chứng để xác định state, relevance hoặc direction, annotator phải dùng giá trị `unknown` theo guideline và đưa trường hợp vào danh sách review/escalation của QA owner thay vì tự suy đoán.

## Scope

- **Trong scope (bắt buộc label):** vehicle traffic light nhìn thấy đủ để xác nhận là traffic light trong road scene và nằm trong vùng cảnh có thể liên quan đến giao thông của ego vehicle.
- **Ngoài scope (ignore):** pedestrian signal, reflection, traffic light xuất hiện trong biển quảng cáo/hình ảnh khác, hoặc vật thể quá mờ/nhỏ để xác nhận là traffic light.
- **Geometry tolerance:** bounding box ôm sát phần traffic-light housing nhìn thấy được; nhóm sẽ chốt tolerance cụ thể trong Guideline v1 và QA plan trước calibration.

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

Sử dụng **chỉ dữ liệu có sẵn trong `guideline-challenge/data/`**. Nguồn chính là BDD100K; README của bài cho biết bộ BDD trong repo có các ảnh traffic light, bao gồm cả cảnh ban ngày, ban đêm và chạng vạng. LISA có 30 frame liên tiếp của một clip traffic light ban ngày nên không nên dùng các frame gần nhau giữa calibration và blind test vì mức độ độc lập của blind set sẽ thấp.