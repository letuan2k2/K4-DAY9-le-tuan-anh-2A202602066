# Annotation guideline — Trạng thái và mức độ liên quan của đèn giao thông

**Version:** v2

## 1. Mục tiêu và phạm vi

Tài liệu này hướng dẫn cách khoanh vùng đèn giao thông dành cho xe và điền các thông tin đi kèm: đèn đang sáng màu gì, có mũi tên chỉ hướng nào và có liên quan đến xe mang camera hay không.

- **Xe mang camera:** xe chụp ảnh đang được gán nhãn.
- **Làn xe:** phần đường dành cho một dòng xe di chuyển.
- **Đầu đèn:** một cụm đèn có thân/vỏ bao quanh riêng, có thể chứa nhiều bóng đỏ, vàng và xanh.
- **Giao lộ hiện tại:** giao lộ mà xe mang camera đang tiếp cận hoặc đang đi qua; không bao gồm giao lộ tiếp theo ở phía xa.

Chỉ gán nhãn đèn dành cho xe nhìn thấy trực tiếp. Bỏ qua đèn cho người đi bộ, phản chiếu, hình đèn trên biển quảng cáo/màn hình, cột, thanh treo và dây điện.

## 2. Annotation unit — Đơn vị gán nhãn

Mỗi ảnh LISA là một đơn vị gán nhãn độc lập. Trong CVAT, chọn chế độ **Shape** để gán nhãn riêng trên từng ảnh, không dùng **Track** để theo dõi một đèn qua nhiều ảnh. Dùng công cụ **Rectangle** để vẽ khung chữ nhật (bounding box) quanh từng đầu đèn.

**Mỗi cụm đèn có thân/vỏ bao quanh riêng được tính là một đối tượng `traffic_light`.**

- Một thân đèn chứa ba bóng đỏ–vàng–xanh: vẽ một box.
- Nếu đèn tròn và đèn mũi tên có hai thân tách biệt: vẽ hai box.
- Nhiều đầu đèn gắn trên cùng thanh treo: mỗi thân đèn có một box riêng.

Các bóng đỏ–vàng–xanh nằm trong cùng một thân đèn chỉ tạo thành một đối tượng. Hai thân đèn tách biệt đặt cạnh nhau vẫn là hai đối tượng, kể cả khi một bên là đèn tròn và bên còn lại là đèn mũi tên.

Không liên kết đối tượng giữa các ảnh. Cùng một đầu đèn xuất hiện ở hai ảnh LISA vẫn được vẽ và điền thuộc tính độc lập trong từng ảnh.

## 3. Geometry rule — Quy tắc vẽ box

Sử dụng khung chữ nhật và vẽ sát phần đầu đèn đang nhìn thấy:

- Box ôm sát phần đầu đèn thực sự nhìn thấy, bao gồm mái che hoặc viền gắn trực tiếp nếu nhìn rõ.
- Không bao cột, thanh treo, dây điện, biển báo hoặc nền thừa.
- Không mở rộng box để ước lượng phần đầu đèn đang bị che.
- Đèn bị cắt ở mép ảnh: box kết thúc tại mép ảnh.
- Đèn bị che một phần: box chỉ bao phần còn nhìn thấy.

**Gán nhãn khi bạn nhận ra chắc chắn đó là đèn giao thông dành cho xe và khung bao quanh phần đèn nhìn thấy có cả chiều rộng lẫn chiều cao lớn hơn 5 pixel (px).**

Bỏ qua nếu đèn quá nhỏ, có một chiều bằng hoặc nhỏ hơn 5 px. Cũng bỏ qua những vùng quá mờ, chỉ thấy một đốm sáng mà không chắc đó là đèn giao thông, dù vùng đó lớn hơn 5 px.

Ví dụ: khung 6 × 12 px có thể gán nhãn nếu nhận ra được đầu đèn; khung 5 × 12 px thì bỏ qua.

Nếu nhận ra đầu đèn và kích thước đủ lớn nhưng không nhìn rõ màu hoặc mũi tên, vẫn gán nhãn. Chọn `unknown` cho thông tin chưa xác định được.

Kích thước được tính trên ảnh gốc. Bạn có thể phóng to để nhìn rõ hơn nhưng không lấy kích thước hiển thị sau khi phóng to, cũng không nới khung cho đủ 5 px. Với đèn bị che hoặc nằm sát mép ảnh, chỉ đo phần còn nhìn thấy.

Khi kiểm tra chất lượng, box đạt yêu cầu nếu ôm sát phần nhìn thấy, không chứa cột hoặc thanh treo và không cắt mất phần thân đèn đang hiện rõ. Sai lệch nhỏ quanh viền là lỗi nhẹ (Minor); box gộp nhiều thân đèn hoặc bao cả cột/thanh treo là lỗi lớn (Major).

## 4. Taxonomy — Hệ thống nhãn và thuộc tính

Chỉ dùng nhãn `traffic_light`, với bốn thuộc tính khớp `03_cvat_labels.json`:

| Thuộc tính | Giá trị hợp lệ | Mặc định |
|---|---|---|
| `state` | `red`, `yellow`, `green`, `off`, `unknown` | `unknown` |
| `direction` | `non_directional`, `left`, `right`, `straight`, `unknown` | `unknown` |
| `relevance` | `relevant`, `not_relevant`, `unknown` | `unknown` |
| `review` | `none`, `escalate` | `none` |

### 4.1. Trạng thái `state`

- `red`, `yellow`, `green`: màu của tín hiệu đang phát sáng rõ.
- `off`: nhìn rõ mặt đèn và xác nhận không có bóng nào phát sáng trong đầu đèn.
- `unknown`: không nhìn rõ trạng thái vì đèn nhỏ, mờ, bị lóa, bị che hoặc các dấu hiệu trong ảnh không thống nhất.

Một bóng đỏ sáng và các bóng còn lại tắt vẫn là `red`. Không gán `off` vì đầu đèn quá xa, bị che hoặc quay lưng nên không thấy ánh sáng. Không đoán màu chỉ từ vị trí bóng trên/dưới.

Nếu thấy nhiều màu cùng sáng và không thể chọn một trạng thái phù hợp, chọn `state=unknown` và `review=escalate` để chuyển trường hợp này cho người phụ trách kiểm tra.

### 4.2. Hướng biểu tượng `direction`

- `non_directional`: nhìn rõ tín hiệu tròn, không có mũi tên.
- `left`: nhìn rõ mũi tên trái.
- `right`: nhìn rõ mũi tên phải.
- `straight`: nhìn rõ mũi tên đi thẳng.
- `unknown`: không đọc được hình dạng/hướng biểu tượng.

Đèn tròn không tự động là `straight`. Thuộc tính này mô tả biểu tượng trên đèn, không phải hướng xe sẽ đi. Không suy hướng từ vị trí đầu đèn, biển báo bên cạnh hoặc hình dạng làn đường.

Ưu tiên nhìn biểu tượng đang sáng. Với đèn `off`, chỉ chọn hướng khi vẫn nhìn rõ biểu tượng trên mặt đèn; nếu không thì chọn `unknown`. Nếu đèn hiển thị nhiều hướng cùng lúc và không có giá trị phù hợp để chọn, dùng `direction=unknown`, `review=escalate`.

### 4.3. Mức độ liên quan `relevance`

Trước tiên, xem ảnh có cho biết làn của xe mang camera được đi thẳng, rẽ trái hay rẽ phải hay không. Sau đó chọn theo một trong hai trường hợp dưới đây.

**Trường hợp 1: Không rõ làn xe được đi hướng nào.**

Gán `relevant` cho **tất cả đèn đủ điều kiện gán nhãn ở giao lộ hiện tại**, bao gồm đèn tròn, đèn mũi tên và cả đèn dành cho luồng xe khác tại cùng giao lộ. Đây là quy ước chung của dự án để mọi người gán nhãn thống nhất khi ảnh thiếu thông tin về làn đường.

Không cần chọn `unknown` hay yêu cầu kiểm tra lại chỉ vì chưa rõ hướng đi của làn xe. Tuy nhiên, không áp dụng quy ước này cho đèn ở giao lộ phía xa. Nếu không xác định được đèn thuộc giao lộ nào, chọn `relevance=unknown` và đặt `review=escalate` để người phụ trách xem lại.

**Trường hợp 2: Nhìn rõ làn xe được đi hướng nào.**

- `relevant`: có bằng chứng đầu đèn có thể điều khiển chuyển động của xe mang camera.
- `not_relevant`: có bằng chứng đèn phục vụ luồng khác hoặc giao lộ khác, không điều khiển chuyển động hiện tại của xe mang camera.
- `unknown`: chưa đủ bằng chứng phân biệt hai trường hợp trên.

Để xác định, quan sát vạch đường, mũi tên trên làn, vị trí và hướng quay của đầu đèn, cùng bố cục giao lộ. Không chọn `relevance` chỉ vì đèn đang xanh, đỏ hoặc nằm bên trái hay bên phải ảnh.

Lưu ý: với quy ước ở trường hợp 1, `relevant` chưa có nghĩa đèn chắc chắn điều khiển xe mang camera. Bộ nhãn hiện tại không ghi riêng lý do chọn `relevant`, nên chỉ dựa vào thuộc tính này và màu đèn thì chưa đủ để quyết định xe phải đi hay dừng.

### 4.4. Yêu cầu kiểm tra lại `review`

- `none`: đã gán nhãn được theo hướng dẫn, kể cả khi có thuộc tính là `unknown` do không nhìn rõ.
- `escalate`: cần người phụ trách xem lại vì các dấu hiệu trong ảnh không thống nhất, hướng dẫn chưa nói rõ cách xử lý hoặc không có giá trị phù hợp để chọn.

Ví dụ cần chuyển kiểm tra (`escalate`): nhiều màu cùng sáng; nhiều hướng đồng thời trên một đầu đèn; không xác định được đèn thuộc giao lộ hiện tại hay giao lộ phía xa.

Không tự thêm nhãn hoặc giá trị mới. Hãy ghi lại vấn đề để người phụ trách kiểm tra. Nếu đối tượng không đủ điều kiện gán nhãn, bỏ qua; khi cần trao đổi, ghi vào phần câu hỏi của nhóm thay vì tạo box chỉ để đặt `review=escalate`.

## 5. Inclusion / exclusion — Trường hợp gán nhãn và bỏ qua

### Bắt buộc gán nhãn (LABEL)

Tạo box `traffic_light` khi tất cả điều kiện sau đều đúng:

1. Nhận diện chắc chắn đây là đầu đèn giao thông dành cho xe.
2. Đầu đèn xuất hiện trực tiếp trong cảnh, không phải hình trên màn hình, biển quảng cáo hoặc ánh phản chiếu.
3. Phần nhìn thấy có thể tạo box với cả chiều rộng và chiều cao lớn hơn 5 px trên ảnh gốc.

Vẫn gán nhãn khi đèn bị che một phần, bị cắt ở mép ảnh hoặc không đọc được một số thuộc tính, miễn là xác nhận được đây là `traffic_light` và đạt ngưỡng kích thước. Thuộc tính không xác định được dùng `unknown`.

### Bắt buộc bỏ qua (IGNORE)

Không tạo box cho:

- đèn tín hiệu dành cho người đi bộ;
- ánh phản chiếu hoặc vùng sáng không xác nhận được là đầu đèn;
- đèn xuất hiện trên biển quảng cáo, màn hình hoặc hình ảnh khác;
- cột, thanh treo, dây điện và biển báo;
- đối tượng có ít nhất một cạnh box không lớn hơn 5 px;
- đối tượng quá mờ để xác nhận là đèn giao thông, dù vùng sáng lớn hơn 5 px.

Không dùng `unknown` để thay thế cho quyết định IGNORE. `unknown` chỉ áp dụng sau khi đối tượng đã được xác nhận là `traffic_light`.

## 6. Visibility / occlusion — Khả năng nhìn thấy và che khuất

| Trường hợp | Cách xử lý |
|---|---|
| Nhìn rõ toàn bộ đầu đèn | Vẽ box sát phần thân đèn và gán thuộc tính theo bằng chứng trong ảnh. |
| Bị che một phần | Vẫn gán nhãn nếu nhận diện được; box chỉ bao phần nhìn thấy và phải có hai cạnh >5 px. |
| Bị cắt ở mép ảnh | Vẫn gán nhãn nếu nhận diện được; box dừng tại mép ảnh. |
| Nhỏ hoặc ở xa | Gán nhãn khi nhận diện được và cả hai cạnh >5 px; thuộc tính không đọc được dùng `unknown`. |
| Có ít nhất một cạnh ≤5 px | IGNORE, không nới box để đạt ngưỡng. |
| Lóa hoặc khó phân biệt với nền | Nếu nhận diện được đầu đèn thì gán nhãn; dùng `state=unknown` khi không đọc chắc màu. |
| Phản chiếu | IGNORE, kể cả khi có màu và hình dạng giống tín hiệu thật. |
| Không chắc đó có phải đèn giao thông | IGNORE; không tạo box suy đoán. |

Không gán `off` chỉ vì không thấy ánh sáng ở một đèn xa, bị che hoặc bị lóa. Chỉ dùng `off` khi nhìn rõ mặt đèn và xác nhận không có bóng nào phát sáng.

## 7. Ambiguity / escalation — Trường hợp chưa rõ và chuyển kiểm tra

Mỗi tình huống phải được thể hiện trong CVAT theo một trong bốn quyết định sau:

| Quyết định | Khi nào dùng | Cách ghi trong CVAT và file kết quả xuất ra |
|---|---|---|
| LABEL | Xác nhận được đầu đèn và box đạt ngưỡng kích thước. | Tạo Rectangle với nhãn `traffic_light`, điền đủ bốn thuộc tính. |
| IGNORE | Ngoài phạm vi, không xác nhận được là đèn giao thông hoặc không đạt ngưỡng kích thước. | Không tạo box. Nếu cần trao đổi, ghi trong nhật ký câu hỏi; không tạo box chỉ để ghi vấn đề. |
| UNKNOWN | Xác nhận được đầu đèn nhưng không xác định được một thuộc tính. | Tạo box và chọn `unknown` cho đúng thuộc tính; dùng `review=none` nếu guideline đã nêu rõ cách xử lý. |
| ESCALATE | Bằng chứng xung đột, không xác định được đèn thuộc giao lộ nào, có nhiều cách hiểu hợp lý hoặc hướng dẫn chưa có quy tắc. | Tạo box nếu đối tượng đủ điều kiện; chọn `unknown` cho thuộc tính chưa rõ và `review=escalate`. |

Không tự thêm nhãn hoặc giá trị mới. Các trường hợp sau phải chuyển kiểm tra: nhiều màu cùng sáng mà không chọn được một `state`; nhiều hướng đồng thời nhưng bộ giá trị hiện tại không biểu diễn được; không rõ đèn thuộc giao lộ hiện tại hay giao lộ phía xa.

Riêng việc không rõ làn xe được đi hướng nào đã có quy tắc ở mục 4.3: tất cả đầu đèn thuộc giao lộ hiện tại được gán `relevance=relevant`, không cần chuyển kiểm tra chỉ vì thiếu thông tin hướng làn.

## 8. Temporal rule — Quy tắc giữa các ảnh

**Không áp dụng theo dõi đối tượng — nhiệm vụ này dùng ảnh tĩnh.**

Mỗi ảnh LISA được gán nhãn độc lập. Không xem ảnh trước hoặc sau để:

- xác nhận một vùng mờ là đèn giao thông;
- suy ra `state`, đặc biệt tại thời điểm chuyển đỏ–xanh;
- suy ra `direction` hoặc `relevance`;
- duy trì cùng một box hay ID giữa các ảnh.

Tất cả thuộc tính đặt `mutable=false`, nghĩa là thuộc tính được gán cố định cho từng box. Project dùng Shape trên từng ảnh và không tạo Track theo dõi đèn qua chuỗi ảnh.

## 9. Examples — Ví dụ

Các ảnh dưới đây được dùng làm ví dụ chuyên môn. Trước khi chốt bộ đáp án, ảnh dùng chính thức phải thuộc nhóm `example` hoặc `calibration` trong `sample_pack.csv`, không được đồng thời thuộc nhóm `blind` dành cho kiểm tra độc lập.

| sample_id | Thấy gì | Expected output | Rule áp dụng |
|---|---|---|---|
| LISA01 | Đèn mũi tên trái đỏ và các đèn tròn đỏ có thân riêng tại cùng giao lộ; không rõ làn của xe mang camera được đi hướng nào. | Mỗi thân đèn một box; `state=red`; mũi tên `direction=left`, đèn tròn `direction=non_directional`; các đèn thuộc giao lộ hiện tại `relevance=relevant`; `review=none`. | Mục 2, 4.1–4.3 |
| LISA15 | Ảnh gần thời điểm chuyển trạng thái; một tín hiệu có thể khó đọc chắc màu. | Nếu xác nhận được đầu đèn và kích thước hợp lệ thì LABEL; tín hiệu không đọc chắc dùng `state=unknown`; nếu thấy màu xung đột thì `review=escalate`. | Mục 6–8 |
| LISA16 | Đèn tròn đã xanh nhưng đầu đèn mũi tên trái vẫn đỏ. | Hai box riêng: `green/non_directional` và `red/left`; không gán một state chung cho cả cụm. | Mục 2, 4.1–4.2 |
| LISA20 | Có đầu đèn rõ và các đầu đèn nhỏ/xa trong cùng ảnh. | Đèn nhận diện được với hai cạnh >5 px được LABEL; thuộc tính không đọc được dùng `unknown`; đối tượng không đạt ngưỡng bị IGNORE. | Mục 3, 5–6 |
| LISA30 | Có các điểm sáng rất nhỏ ở xa, không đủ hình dạng hoặc kích thước. | Không tạo box cho đối tượng không xác nhận được hoặc có một cạnh ≤5 px; không nới box lấy nền. | Mục 3, 5 |

## 10. Common mistakes — Lỗi thường gặp

1. **Vẽ từng bóng thành một đối tượng:** các bóng nằm trong cùng một thân đèn chỉ tạo một box.
2. **Gộp nhiều thân đèn vào một box:** đèn tròn và đèn mũi tên có thân tách biệt phải có box riêng.
3. **Bao cả cột hoặc thanh treo:** box chỉ ôm phần đầu đèn nhìn thấy.
4. **Nới box cho đủ 5 px:** đo box ôm sát đầu đèn trên ảnh gốc; không đạt ngưỡng thì IGNORE.
5. **Dùng `unknown` cho đốm sáng chưa xác nhận:** không chắc đó là đèn giao thông thì IGNORE.
6. **Đoán state từ vị trí bóng:** không đọc chắc màu thì dùng `state=unknown`.
7. **Gán `off` cho đèn xa hoặc bị lóa:** `off` chỉ dùng khi nhìn rõ toàn bộ mặt đèn không phát sáng.
8. **Gán đèn tròn là `straight`:** đèn tròn dùng `non_directional`; `straight` chỉ khi nhìn rõ mũi tên đi thẳng.
9. **Gán `direction` theo vị trí trái/phải trong ảnh:** `direction` mô tả biểu tượng trên đèn.
10. **Bỏ qua quy tắc `relevance` của v2:** khi không rõ hướng làn, mọi đèn thuộc giao lộ hiện tại đều là `relevant`.
11. **Dùng ảnh trước/sau để suy ảnh hiện tại:** mỗi ảnh LISA phải được xử lý độc lập.
12. **Không chuyển kiểm tra khi bằng chứng xung đột:** chọn thuộc tính `unknown` và `review=escalate` để quyết định được lưu trong file kết quả xuất từ CVAT.
