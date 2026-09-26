# Annotation guideline — Traffic-Light State & Ego Relevance

**Version:** v1

## 1. Objective + scope

Mục tiêu của annotation là xác định các **vehicle traffic signal head** nhìn thấy trong ảnh road scene, trạng thái tín hiệu của chúng và mức độ liên quan với hướng di chuyển của ego vehicle.

Guideline phục vụ bài toán perception cho hệ thống hỗ trợ lái/xe tự hành, trong đó model không chỉ cần phát hiện traffic light mà còn phải phân biệt tín hiệu nào có khả năng điều khiển ego vehicle.

### Trong scope

Label một object khi:

- có đủ bằng chứng thị giác để xác nhận đó là **vehicle traffic signal head**;
- object nhìn thấy trực tiếp trong road scene;
- có thể tạo bounding box cho phần signal head nhìn thấy được.

### Ngoài scope

Không label:

- pedestrian traffic signals;
- reflection/phản chiếu của traffic light;
- traffic light xuất hiện trong billboard, màn hình hoặc hình ảnh khác;
- pole, mast, gantry hoặc dây treo nếu không phải signal head;
- object quá nhỏ/mờ đến mức không thể xác nhận đó là traffic light;
- tín hiệu giao thông khác không thuộc vehicle traffic light.

Không suy đoán object chỉ vì vị trí của nó “có vẻ giống nơi đặt đèn giao thông”.

---

## 2. Annotation unit

**Annotation unit:** một ảnh tĩnh.

**Object unit:** một **traffic signal head/housing độc lập**.

Một housing chứa nhiều bóng đèn đỏ–vàng–xanh theo chiều dọc hoặc ngang được tính là **một object**, không tạo một bounding box riêng cho từng bóng đèn.

Hai signal head nằm cạnh nhau nhưng có housing riêng phải được annotation thành **hai object riêng**.

Ví dụ:

- một housing đỏ–vàng–xanh → 1 object;
- một housing tín hiệu chung + một housing mũi tên rẽ trái riêng → 2 objects;
- nhiều signal head treo trên cùng một gantry → mỗi head là 1 object.

Task sử dụng **Shape**, không sử dụng Track.

---

## 3. Geometry rule

Geometry sử dụng **Rectangle / Bounding Box**.

Bounding box phải:

1. ôm sát **phần traffic signal head/housing nhìn thấy được**;
2. bao gồm phần visor/bezel gắn trực tiếp với signal head nếu nhìn thấy rõ;
3. không bao gồm pole, mast, gantry, dây treo hoặc background;
4. không mở rộng box để “ước lượng” phần object đang bị che.

### Partial occlusion

Nếu traffic light bị che một phần nhưng vẫn xác nhận được object:

- vẫn label;
- box chỉ bao phần object **thực sự nhìn thấy được**;
- không dùng amodal box để đoán phần bị che.

### Object ở mép ảnh

Nếu traffic light bị cắt bởi mép ảnh nhưng vẫn nhận diện chắc chắn:

- vẫn label;
- box kết thúc tại mép ảnh.

### Object nhỏ/xa

Nếu đủ bằng chứng để xác nhận là traffic light:

- vẫn label;
- các attribute không xác định được dùng `unknown`.

Nếu không đủ bằng chứng để xác nhận object là traffic light:

- không tạo speculative bounding box.

Geometry tolerance chính thức sẽ được chốt trong QA plan sau calibration; v1 ưu tiên quy tắc **tight visible box** thay vì một ngưỡng pixel tùy ý chưa được kiểm nghiệm.

---

## 4. Taxonomy

Chỉ sử dụng một class:

`traffic_light`

Mỗi object có 4 attributes.

### `state`

Allowed values:

- `red`
- `yellow`
- `green`
- `unknown`

Chọn màu của tín hiệu đang phát sáng rõ ràng.

Không suy đoán state dựa vào vị trí bóng trong housing khi màu không nhìn rõ.

Nếu glare, blur, occlusion, khoảng cách hoặc điều kiện ánh sáng khiến state không xác định chắc chắn:

`state = unknown`

### `relevance`

Allowed values:

- `relevant`
- `not_relevant`
- `unknown`

`relevant` chỉ dùng khi có bằng chứng hình ảnh hợp lý cho thấy signal head có khả năng điều khiển movement của ego vehicle.

`not_relevant` chỉ dùng khi có bằng chứng rõ ràng rằng signal head phục vụ traffic flow khác.

Khi lane association hoặc hướng điều khiển không đủ rõ:

`relevance = unknown`

Không suy relevance từ màu đèn.

### `direction`

Allowed values:

- `straight`
- `left`
- `right`
- `unknown`

Dùng `left` hoặc `right` khi có directional arrow hoặc bằng chứng hình ảnh rõ về movement tương ứng.

Dùng `straight` khi tín hiệu và lane association cho movement đi thẳng được xác định rõ.

Nếu không đủ bằng chứng:

`direction = unknown`

Không suy đoán direction chỉ dựa trên vị trí trái/phải của signal head trong ảnh.

### `review`

Allowed values:

- `none`
- `escalate`

Dùng `review = escalate` khi case cần QA owner xem lại theo rule ở mục 7.

---

## 5. Inclusion / exclusion

### LABEL

Label traffic light khi:

- traffic signal head nhìn thấy trực tiếp;
- xác nhận được object class;
- object thuộc vehicle traffic control;
- kể cả khi state hoặc relevance chưa xác định được.

Một object có thể:

`traffic_light + state=unknown`

và vẫn là annotation hợp lệ.

### IGNORE

Không tạo annotation khi:

- không chắc object có thực sự là traffic light;
- chỉ nhìn thấy reflection;
- pedestrian signal;
- billboard/display;
- phần object quá nhỏ hoặc mờ để xác nhận class;
- object nằm ngoài scope.

### Nguyên tắc

**Không dùng `unknown` để thay thế cho việc không xác định được object class.**

`unknown` chỉ áp dụng cho **attribute của một traffic_light đã được xác nhận**.

---

## 6. Visibility / occlusion

### Clear visibility

Nếu signal head và tín hiệu nhìn rõ:

- label bình thường;
- gán các attribute theo bằng chứng ảnh.

### Partial occlusion

Nếu housing bị che một phần nhưng vẫn xác định được:

- label phần nhìn thấy;
- state/relevance/direction chỉ gán khi có đủ bằng chứng.

### Severe occlusion

Nếu vẫn chắc chắn đó là traffic light nhưng không đọc được state:

`state = unknown`

Nếu không xác định được association với ego:

`relevance = unknown`

Nếu ambiguity có thể ảnh hưởng quyết định downstream:

`review = escalate`

### Small / far object

Không dùng kích thước pixel cố định ở v1.

Quyết định dựa trên hai câu hỏi:

1. Có xác nhận chắc chắn đây là traffic light không?
2. Có đủ bằng chứng để gán từng attribute không?

Có thể label object nhưng để một hoặc nhiều attribute là `unknown`.

### Glare / night / reflection

Không suy state từ vùng sáng hoặc màu phản chiếu nếu không xác định được lamp đang active.

Khi không chắc chắn:

`state = unknown`

---

## 7. Ambiguity / escalation

Mỗi case phải rơi vào một trong bốn hành vi:

### LABEL

Object và các attribute cần thiết đủ bằng chứng.

→ Tạo bounding box và điền attributes.

### IGNORE

Không đủ bằng chứng xác nhận object thuộc class `traffic_light`, hoặc object ngoài scope.

→ Không tạo annotation.

### UNKNOWN

Object chắc chắn là traffic light nhưng một attribute không xác định được.

Ví dụ:

`state = unknown`

hoặc:

`relevance = unknown`

### ESCALATE

Tạo traffic-light annotation và đặt:

`review = escalate`

khi xảy ra ít nhất một trường hợp:

- có từ hai signal head trở lên và không đủ bằng chứng xác định head nào liên quan ego vehicle;
- lane association mơ hồ và quyết định relevance có thể ảnh hưởng downstream;
- state có bằng chứng xung đột;
- rule hiện tại không bao phủ được case;
- annotator có thể đưa ra từ hai interpretation hợp lý khác nhau.

Annotator **không được tự tạo rule mới**.

### Evidence hierarchy cho ego relevance

Ưu tiên bằng chứng theo thứ tự:

1. lane/movement association nhìn thấy rõ;
2. directional arrow hoặc lane arrow;
3. vị trí signal head tương ứng với carriageway/lane của ego;
4. orientation/facing direction của signal head;
5. road geometry.

Không dùng:

- màu tín hiệu;
- cảm giác;
- giả định luật giao thông không nhìn thấy trong ảnh

để quyết định relevance.

Nếu evidence xung đột:

`relevance = unknown`

và:

`review = escalate`

---

## 8. Temporal rule

**Không áp dụng — task sử dụng ảnh tĩnh.**

Mỗi image được annotation độc lập.

Không sử dụng thông tin từ frame trước hoặc frame sau để:

- xác định state;
- suy relevance;
- suy direction;
- quyết định có label object hay không.

Ngay cả khi ảnh đến từ sequence LISA, annotator phải đưa ra quyết định dựa trên **frame hiện tại**.

---

## 9. Examples

Các ví dụ chính thức trong guideline v2 sẽ dùng `sample_id` thuộc split `example` hoặc `calibration`; tuyệt đối không dùng ảnh blind.

| Case | Thấy gì | Expected output | Rule |
|---|---|---|---|
| E1 | Một vehicle traffic light nhìn rõ, tín hiệu đỏ rõ, association với ego rõ | `traffic_light`; `state=red`; `relevance=relevant`; direction theo evidence; `review=none` | Mục 4 |
| E2 | Traffic light rõ nhưng ở xa, không đọc chắc màu | Label box; `state=unknown` | Mục 6 |
| E3 | Hai signal head tại giao lộ, không đủ bằng chứng head nào điều khiển ego | Label các head xác nhận được; `relevance=unknown`; `review=escalate` | Mục 7 |
| E4 | Housing bị che một phần nhưng vẫn nhận diện được | Bounding box phần visible; attributes theo evidence | Mục 3, 6 |
| E5 | Vùng sáng/phản chiếu giống đèn nhưng không xác nhận được traffic signal head | IGNORE | Mục 5 |
| E6 | Signal head rõ ràng hướng sang traffic flow khác | `relevance=not_relevant` | Mục 4, 7 |

Trước calibration, E1–E6 phải được thay/mapping bằng sample_id thật từ `sample_pack.csv`.

---

## 10. Common mistakes

### 1. Vẽ từng bóng đèn thành một object

Sai.

Một signal head/housing = một object.

### 2. Box cả pole hoặc gantry

Sai.

Bounding box chỉ bao signal head/housing nhìn thấy được.

### 3. Đoán state từ vị trí bóng

Sai.

Không nhìn chắc màu → `state=unknown`.

### 4. Mặc định traffic light phía trước xe là relevant

Sai.

Phải có evidence về lane/movement association.

### 5. Dùng màu đèn để suy relevance

Sai.

Một đèn xanh không có nghĩa đó là đèn của ego vehicle.

### 6. Dùng `unknown` cho object không xác định được

Sai.

Nếu chưa xác nhận được object là traffic light → IGNORE.

### 7. Gặp ambiguity nhưng tự chọn đáp án “có vẻ hợp lý nhất”

Sai.

Nếu nhiều interpretation hợp lý và ảnh không resolve được:

`unknown + review=escalate`

### 8. Dùng frame trước/sau của LISA để suy frame hiện tại

Sai.

Task này annotation từng image độc lập.

### 9. Tự tạo thêm class hoặc attribute

Sai.

Ontology đã khóa. Case mới phải được ghi nhận để review guideline trước khi thay schema.

### 10. Giải thích rule bằng miệng nhưng guideline không có

Không hợp lệ.

Rule annotator cần sử dụng phải tồn tại trong guideline và có thể được nhóm peer đọc độc lập.