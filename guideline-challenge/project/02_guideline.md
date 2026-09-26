# Hướng dẫn gán nhãn đèn giao thông

**Version:** v2

## 1. Mục tiêu và phạm vi

Tài liệu này hướng dẫn cách khoanh vùng đèn giao thông dành cho xe và điền các thông tin đi kèm: đèn đang sáng màu gì, có mũi tên chỉ hướng nào và có liên quan đến xe mang camera hay không.

- **Xe camera (ego):** xe mang camera chụp ảnh.
- **Làn xe:** phần đường dành cho một dòng xe di chuyển.
- **Đầu đèn:** một vỏ đèn độc lập, có thể chứa nhiều bóng đỏ, vàng, xanh.
- **Giao lộ hiện tại:** giao lộ xe camera đang tiếp cận hoặc đang đi qua trong ảnh; không bao gồm giao lộ tiếp theo ở phía xa.

Chỉ gán nhãn đèn dành cho xe nhìn thấy trực tiếp. Bỏ qua đèn cho người đi bộ, phản chiếu, hình đèn trên biển quảng cáo/màn hình, cột, thanh treo và dây điện.

## 2. Cách khoanh vùng đèn

Dùng công cụ **Rectangle**, chế độ **Shape**, để vẽ khung chữ nhật (box) quanh từng đầu đèn trong ảnh.

**Một đầu đèn có vỏ riêng = một đối tượng `traffic_light`.**

- Một vỏ chứa ba bóng đỏ–vàng–xanh: một box.
- Đèn tròn và đèn mũi tên có hai vỏ riêng: hai box.
- Nhiều đầu đèn trên cùng thanh treo: mỗi đầu đèn có box riêng.

Vẽ khung sát phần đầu đèn nhìn thấy được, bao gồm cả mái che hoặc viền đèn nếu nhìn rõ. Không khoanh cả cột, thanh treo, dây điện hay khoảng nền xung quanh. Không vẽ thêm phần đèn đang bị che.

Nếu đèn bị cắt ở mép ảnh, box dừng tại mép ảnh. Nếu đèn bị che, chỉ bao phần nhìn thấy. Cả hai trường hợp vẫn phải đáp ứng mục 3.

## 3. Khi nào gán nhãn, khi nào bỏ qua?

**Gán nhãn khi bạn nhận ra chắc chắn đó là đèn giao thông dành cho xe và khung bao quanh phần đèn nhìn thấy có cả chiều rộng lẫn chiều cao lớn hơn 5 pixel (px).**

Bỏ qua nếu đèn quá nhỏ, có một chiều bằng hoặc nhỏ hơn 5 px. Cũng bỏ qua những vùng quá mờ, chỉ thấy một đốm sáng mà không chắc đó là đèn giao thông, dù vùng đó lớn hơn 5 px.

Ví dụ: khung 6 × 12 px có thể gán nhãn nếu nhận ra được đầu đèn; khung 5 × 12 px thì bỏ qua.

Nếu nhận ra đầu đèn và kích thước đủ lớn nhưng không nhìn rõ màu hoặc mũi tên, vẫn gán nhãn. Chọn `unknown` cho thông tin chưa xác định được.

Kích thước được tính trên ảnh gốc. Bạn có thể phóng to để nhìn rõ hơn nhưng không lấy kích thước hiển thị sau khi phóng to, cũng không nới khung cho đủ 5 px. Với đèn bị che hoặc nằm sát mép ảnh, chỉ đo phần còn nhìn thấy.

## 4. Nhãn và thuộc tính

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

Nếu thấy nhiều màu cùng sáng và không thể chọn một trạng thái phù hợp, chọn `state=unknown`, `review=escalate` để người phụ trách kiểm tra lại.

### 4.2. Hướng biểu tượng `direction`

- `non_directional`: nhìn rõ tín hiệu tròn, không có mũi tên.
- `left`: nhìn rõ mũi tên trái.
- `right`: nhìn rõ mũi tên phải.
- `straight`: nhìn rõ mũi tên đi thẳng.
- `unknown`: không đọc được hình dạng/hướng biểu tượng.

Đèn tròn không tự động là `straight`. Thuộc tính này mô tả biểu tượng trên đèn, không phải hướng xe sẽ đi. Không suy hướng từ vị trí đầu đèn, biển báo bên cạnh hoặc hình dạng làn đường.

Ưu tiên nhìn biểu tượng đang sáng. Với đèn `off`, chỉ chọn hướng khi vẫn nhìn rõ biểu tượng trên mặt đèn; nếu không thì chọn `unknown`. Nếu đèn hiển thị nhiều hướng cùng lúc và không có giá trị phù hợp để chọn, dùng `direction=unknown`, `review=escalate`.

### 4.3. Mức độ liên quan `relevance`

Trước tiên, xem ảnh có cho biết làn xe camera đang đứng được đi thẳng, rẽ trái hay rẽ phải hay không. Sau đó chọn theo một trong hai trường hợp dưới đây.

**Trường hợp 1: Không rõ làn xe được đi hướng nào.**

Gán `relevant` cho **tất cả đèn đủ điều kiện gán nhãn ở giao lộ hiện tại**, bao gồm đèn tròn, đèn mũi tên và cả đèn dành cho luồng xe khác tại cùng giao lộ. Đây là quy ước chung của dự án để mọi người gán nhãn thống nhất khi ảnh thiếu thông tin về làn đường.

Không cần chọn `unknown` hay yêu cầu kiểm tra lại chỉ vì chưa rõ hướng đi của làn xe. Tuy nhiên, không áp dụng quy ước này cho đèn ở giao lộ phía xa. Nếu không xác định được đèn thuộc giao lộ nào, chọn `relevance=unknown` và đặt `review=escalate` khi cần người phụ trách phân xử.

**Trường hợp 2: Nhìn rõ làn xe được đi hướng nào.**

- `relevant`: có bằng chứng đầu đèn có thể điều khiển chuyển động của xe camera.
- `not_relevant`: có bằng chứng đèn phục vụ luồng khác hoặc giao lộ khác, không điều khiển chuyển động hiện tại của xe camera.
- `unknown`: chưa đủ bằng chứng phân biệt hai trường hợp trên.

Để xác định, quan sát vạch đường, mũi tên trên làn, vị trí và hướng quay của đầu đèn, cùng bố cục giao lộ. Không chọn relevance chỉ vì đèn đang xanh, đỏ hay nằm bên trái, bên phải ảnh.

Lưu ý: với quy ước ở trường hợp 1, `relevant` chưa có nghĩa đèn chắc chắn điều khiển xe camera. Bộ nhãn hiện tại không ghi riêng lý do chọn `relevant`, nên chỉ dựa vào thuộc tính này và màu đèn thì chưa đủ để quyết định xe phải đi hay dừng.

### 4.4. Yêu cầu kiểm tra lại `review`

- `none`: đã gán nhãn được theo hướng dẫn, kể cả khi có thuộc tính là `unknown` do không nhìn rõ.
- `escalate`: cần người phụ trách xem lại vì các dấu hiệu trong ảnh không thống nhất, hướng dẫn chưa nói rõ cách xử lý hoặc không có giá trị phù hợp để chọn.

Ví dụ cần escalate: nhiều màu cùng sáng; nhiều hướng đồng thời trên một đầu đèn; chưa thống nhất được đèn thuộc giao lộ hiện tại hay giao lộ phía xa.

Không tự thêm nhãn hoặc giá trị mới. Hãy ghi lại vấn đề để người phụ trách kiểm tra. Nếu đối tượng không đủ điều kiện gán nhãn, bỏ qua; khi cần trao đổi, ghi vào phần câu hỏi của nhóm thay vì tạo box chỉ để đặt `review=escalate`.

## 5. Các trường hợp dễ gây nhầm lẫn

Các ví dụ dưới đây giúp bạn chọn cách xử lý trong những tình huống thường gây nhầm lẫn. Chỉ gán nhãn cho đèn đáp ứng mục 3; những thuộc tính không được nêu trong bảng thì điền theo mục 4.

| Mã | Thấy gì | Quyết định |
|---|---|---|
| E1 | Đèn tròn đỏ rõ, có bằng chứng điều khiển làn xe | `state=red`, `direction=non_directional`, `relevance=relevant`, `review=none` |
| E2 | Đầu đèn 5 × 15 px, nhìn thấy điểm sáng đỏ | Bỏ qua; chiều rộng không lớn hơn 5 px |
| E3 | Vùng sáng 12 × 12 px nhưng không xác nhận được đầu đèn | Bỏ qua; đạt kích thước chưa đủ để gán nhãn |
| E4 | Đầu đèn 6 × 12 px nhận diện được, màu và biểu tượng không rõ | Gán nhãn; `state=unknown`, `direction=unknown`; không bắt buộc escalate chỉ vì nhỏ |
| E5 | Không rõ hướng đi của làn xe; nhiều đèn thuộc giao lộ hiện tại | Tất cả đèn đủ điều kiện đều `relevance=relevant`, kể cả đèn cho luồng khác; không bắt buộc escalate vì thiếu hướng làn |
| E6 | Như E5 nhưng có thêm đèn ở giao lộ phía xa | Đèn giao lộ hiện tại: `relevant`; đèn xác nhận ở giao lộ khác và không điều khiển xe hiện tại: `not_relevant` |
| E7 | Không rõ đèn thuộc giao lộ hiện tại hay giao lộ phía xa | `relevance=unknown`, `review=escalate` |
| E8 | Hướng đi của làn xe rõ; đèn rõ ràng phục vụ luồng khác | `relevance=not_relevant`; không áp dụng ngoại lệ E5 |
| E9 | Đèn bị che một phần hoặc cắt ở mép ảnh | Box phần nhìn thấy; giữ khi nhận diện được và hai cạnh > 5 px, nếu không thì bỏ qua |
| E10 | Mặt đèn rõ, tất cả bóng không phát sáng | `state=off`; direction theo biểu tượng đọc được, nếu không thì `unknown` |
| E11 | Nhận diện được đầu đèn nhưng lóa, không đọc được màu | `state=unknown`, không gán `off` |
| E12 | Đèn tròn xanh và mũi tên trái đỏ có hai vỏ riêng | Hai box; lần lượt `green/non_directional` và `red/left`; relevance theo mục 4.3 |
| E13 | Nhiều màu sáng trên một đầu đèn gây xung đột | `state=unknown`, `review=escalate` |
| E14 | Phản chiếu đèn trên kính hoặc đèn cho người đi bộ | Bỏ qua vì ngoài phạm vi |

Khi bổ sung ảnh minh họa, ghi mã ảnh (`sample_id`) từ tập ví dụ (`example`) hoặc tập gán thử để thống nhất cách làm (`calibration`). Không dùng ảnh trong tập đánh giá độc lập (`blind`) làm ví dụ gửi cho nhóm đánh giá chéo.

## 6. Quy tắc cho chuỗi ảnh

Xử lý từng ảnh riêng, kể cả khi các ảnh LISA nằm trong cùng một chuỗi. Chỉ dựa vào ảnh đang xem để gán nhãn. Không xem ảnh trước hoặc sau để đoán màu, hướng mũi tên, mức độ liên quan hay xác nhận một đốm mờ là đèn giao thông.

## 7. Kiểm tra trước khi hoàn thành ảnh

1. Xác nhận đối tượng là đầu đèn dành cho xe trong phạm vi.
2. Kiểm tra cả chiều rộng và chiều cao box đều > 5 px trên ảnh gốc.
3. Kiểm tra box ôm sát phần nhìn thấy, mỗi vỏ riêng có một box.
4. Gán `state`; phân biệt `off` với `unknown`.
5. Gán `direction` theo biểu tượng; đèn tròn là `non_directional`.
6. Gán `relevance` theo hai trường hợp ở mục 4.3, chú ý quy ước khi chưa rõ hướng đi của làn xe.
7. Chọn `review=escalate` nếu cần người phụ trách xem lại. Kiểm tra các thuộc tính trước khi hoàn thành, tránh bỏ sót vì đã có giá trị mặc định.
