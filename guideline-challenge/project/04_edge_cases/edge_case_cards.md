# Edge-case library

Tối thiểu **8 card**, khuyến nghị 10–12. Một edge case tốt là case mà hai annotator hợp lý có thể làm khác nhau nếu
guideline chưa rõ. Tám ảnh dễ có label rõ ràng không được tính là edge-case library.

Cần có đủ độ đa dạng: occlusion / truncation / small-far · ambiguous semantics · conflicting road elements · **một case
critical-risk** · **một case guideline cho phép escalation**.

File này là kho nội bộ của nhóm, **không gửi cho peer**. Card dùng ảnh example/calibration thì chép rule + ví dụ sang
`02_guideline.md` (mục 7 và 9) để peer đọc được. Card về ảnh blind chỉ nằm ở đây, và decision của nó phải có trong
`gold_decisions.csv` trước `make freeze`.

Các card dưới đây đều dùng ảnh LISA và quyết định theo guideline v2. Tọa độ cụ thể được chốt trong CVAT; mô tả vị trí giúp reviewer đối chiếu đúng đầu đèn.

---

CASE ID: EC01
Sample: LISA01
Scene: Giao lộ có nhiều đầu đèn, làn xe camera không có mũi tên chỉ hướng rõ.
Observation: Có đầu đèn mũi tên trái màu đỏ và các đầu đèn tròn màu đỏ thuộc giao lộ hiện tại.
Decision: LABEL
Expected: Mỗi vỏ đèn đủ lớn là một box `traffic_light`; `state=red`; đèn mũi tên `direction=left`, đèn tròn `direction=non_directional`; các đèn thuộc giao lộ hiện tại có `relevance=relevant`.
Rationale: Model cần giữ riêng từng đầu đèn và quy tắc v2 yêu cầu gán relevant cho toàn bộ đèn cùng giao lộ khi chưa rõ hướng làn.
Common mistake: Gộp đầu đèn mũi tên và đầu đèn tròn thành một box hoặc chỉ chọn một đèn relevant.
Diversity: ambiguity, multiple_objects, critical

---

CASE ID: EC02
Sample: LISA05
Scene: Nhiều đầu đèn gắn trên cùng hệ thống cột và thanh treo.
Observation: Hai đầu đèn ở gần nhau nhưng có vỏ riêng; cột và thanh treo chiếm vùng lớn quanh đèn.
Decision: LABEL
Expected: Vẽ một box cho từng vỏ, ôm sát phần đầu đèn và không bao cột, thanh treo hay biển báo.
Rationale: Geometry sạch giúp model học hình dạng đầu đèn thay vì cấu trúc lắp đặt.
Common mistake: Vẽ một box lớn quanh cả cụm đèn và thanh ngang.
Diversity: geometry, multiple_objects

---

CASE ID: EC03
Sample: LISA10
Scene: Ánh sáng nền mạnh và các đầu đèn nhỏ ở xa.
Observation: Một số vùng màu đỏ/xanh rất nhỏ có thể là đèn hoặc chỉ là điểm sáng; không phải vùng sáng nào cũng đủ bằng chứng.
Decision: UNKNOWN
Expected: Chỉ label đối tượng xác nhận được là đầu đèn và có cả hai cạnh box >5 px; thuộc tính không đọc được chọn `unknown`. Bỏ qua đốm sáng không xác nhận được.
Rationale: Giảm false positive nhưng vẫn giữ đầu đèn hợp lệ cho bài toán phát hiện.
Common mistake: Khoanh mọi điểm sáng màu hoặc dùng `unknown` cho một object chưa xác nhận được.
Diversity: small_far, low_visibility, ambiguity

---

CASE ID: EC04
Sample: LISA14
Scene: Frame ngay trước khi trạng thái đèn tròn thay đổi.
Observation: Đèn tròn vẫn đỏ và mũi tên trái cũng đỏ; các frame sau của chuỗi không được dùng để suy ngược.
Decision: LABEL
Expected: Gán theo riêng ảnh LISA14: các tín hiệu đọc rõ là `state=red`; direction theo biểu tượng đang thấy; `review=none` nếu không có xung đột trong ảnh.
Rationale: Mỗi ảnh là một mẫu độc lập trong downstream contract.
Common mistake: Xem frame sau có đèn xanh rồi đổi state của frame hiện tại.
Diversity: temporal, critical

---

CASE ID: EC05
Sample: LISA15
Scene: Frame sát thời điểm chuyển pha, có thể xuất hiện lóa hoặc màu khó đọc.
Observation: Nếu chỉ từ ảnh hiện tại không xác định chắc màu của một đầu đèn, bằng chứng thời gian không được dùng bổ sung.
Decision: UNKNOWN
Expected: Vẫn vẽ box nếu nhận diện được đầu đèn và đủ kích thước; dùng `state=unknown` cho đầu đèn không đọc chắc màu. Nếu thấy nhiều màu xung đột, thêm `review=escalate`.
Rationale: Nhầm đỏ/xanh là lỗi Critical nên không được đoán ở thời điểm chuyển pha.
Common mistake: Chọn trạng thái theo frame trước hoặc frame sau.
Diversity: temporal, ambiguity, escalation, critical

---

CASE ID: EC06
Sample: LISA16
Scene: Đèn tròn chuyển xanh trong khi đầu đèn mũi tên trái vẫn đỏ.
Observation: Hai vỏ đèn gần nhau có state khác nhau trong cùng một ảnh.
Decision: LABEL
Expected: Đầu đèn tròn: `state=green`, `direction=non_directional`; đầu đèn mũi tên: `state=red`, `direction=left`; mỗi đầu đèn một box.
Rationale: State và direction phải gắn với từng vỏ đèn, không gán một trạng thái chung cho cả giao lộ.
Common mistake: Gán tất cả đèn cùng màu xanh hoặc gộp hai đầu đèn.
Diversity: conflict, multiple_objects, critical

---

CASE ID: EC07
Sample: LISA20
Scene: Nhiều đầu đèn ở các vị trí và khoảng cách khác nhau trong cùng giao lộ.
Observation: Các đầu đèn lớn đọc được màu xanh; một số đầu đèn xa chỉ đọc được class hoặc không đạt ngưỡng kích thước.
Decision: LABEL
Expected: Label từng đầu đèn nhận diện được với hai cạnh >5 px; đầu đèn đọc rõ dùng `state=green`, đầu đèn không đọc rõ dùng `state=unknown`; bỏ qua object không đạt ngưỡng.
Rationale: Tách quyết định tồn tại object khỏi khả năng đọc thuộc tính.
Common mistake: Bỏ hết đèn xa hoặc gán xanh cho mọi đốm sáng nhờ các đèn lớn đang xanh.
Diversity: small_far, ambiguity

---

CASE ID: EC08
Sample: LISA22
Scene: Đầu đèn nhỏ gần nền cây và công trình, độ tương phản thấp.
Observation: Có thể nhận ra vỏ đèn nhưng không chắc biểu tượng là hình tròn hay mũi tên.
Decision: UNKNOWN
Expected: Nếu box đủ kích thước và class chắc chắn, label `traffic_light`, đặt `direction=unknown`; không suy direction từ vị trí của đèn trong ảnh.
Rationale: Direction dùng để mô tả biểu tượng, không phải hướng đường hay vị trí trái/phải.
Common mistake: Đèn ở bên trái ảnh thì gán `left`.
Diversity: low_visibility, semantic_ambiguity

---

CASE ID: EC09
Sample: LISA27
Scene: Đầu đèn xa có thể thuộc giao lộ hiện tại hoặc nằm sâu hơn trên đường.
Observation: Bố cục ảnh không đủ rõ để xác định một đầu đèn xa thuộc giao lộ nào.
Decision: ESCALATE
Expected: Nếu đầu đèn đủ điều kiện label, chọn `relevance=unknown`, `review=escalate`; không áp dụng quy tắc relevant cho cùng giao lộ khi chưa xác định được chính giao lộ.
Rationale: Relevance sai có thể khiến downstream liên kết nhầm tín hiệu với xe camera.
Common mistake: Gán relevant cho mọi đèn nhìn thấy, kể cả khi chưa biết có thuộc giao lộ hiện tại không.
Diversity: ambiguity, escalation, critical

---

CASE ID: EC10
Sample: LISA30
Scene: Cuối chuỗi, có nhiều điểm sáng rất nhỏ ở xa.
Observation: Một số điểm sáng có ít nhất một cạnh box không lớn hơn 5 px hoặc không đủ hình dạng để xác nhận là đầu đèn.
Decision: IGNORE
Expected: Không tạo box cho object không vượt ngưỡng ở cả hai cạnh hoặc không xác nhận được class; các đầu đèn lớn, rõ vẫn label bình thường.
Rationale: Ngưỡng hình học giúp các annotator xử lý nhất quán object cực nhỏ.
Common mistake: Nới box lấy thêm nền để vượt 5 px hoặc tạo box `unknown` cho đốm sáng.
Diversity: small_far, negative, geometry

---
