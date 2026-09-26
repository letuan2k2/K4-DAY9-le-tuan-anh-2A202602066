# Kế hoạch QA và ngưỡng chất lượng

## Flow

Quy trình: Guideline → Calibration → Gán nhãn → Tự kiểm tra → QA review → Sửa lỗi → Quality gate.

- **Người review:** QA owner không review annotation do chính mình tạo nếu nhóm có từ hai người. Với nhóm chỉ có một người, Team02 review blind set và mọi kết quả phải được ghi là peer review.
- **Phạm vi review:** review 100% ảnh calibration và blind; với batch production lớn hơn 20 ảnh, review ngẫu nhiên 20% nhưng tối thiểu 5 ảnh, cộng thêm 100% ảnh có `review=escalate`, `state=unknown`, đối tượng sát ngưỡng 5 px hoặc nhiều đầu đèn.
- **Cách chọn mẫu:** cố định một seed trước khi chọn ngẫu nhiên; sau đó bổ sung toàn bộ mẫu rủi ro. Không thay mẫu sau khi đã thấy kết quả để làm đẹp chỉ số.
- **Nơi ghi issue:** bất đồng calibration ghi trong `06_calibration_report.csv`; câu hỏi blind ghi trong `07_blind_handoff/clarification_log.csv`; kết quả chấm blind ghi trong `transfer_score.csv`; thay đổi rule ghi trong `08_revision_log.md`.
- **Đóng issue:** mỗi issue phải có người chịu trách nhiệm, severity, quyết định cuối, bằng chứng và action. Issue chỉ đóng sau khi annotation được sửa hoặc guideline có rule/escalation rõ và QA owner xác nhận.
- **Guideline gap:** dừng gán các case cùng loại, ghi issue, cập nhật guideline và revision log. Trước freeze tăng từ v1 lên v2; sau blind handoff chỉ tăng lên v3 khi có bằng chứng từ peer. Nếu đã freeze thì làm theo quy trình refreeze của bài và ghi lý do.

## Defect severity

Nhóm được đổi mapping nếu downstream contract khác, nhưng phải giải thích và chốt trước khi QA.

| Severity | Định nghĩa cho project này | Ví dụ | Action mặc định |
|---|---|---|---|
| Critical | Lỗi có thể làm downstream dùng sai trạng thái hoặc sai đèn điều khiển. | Bỏ sót đầu đèn chính; đọc đèn đỏ thành xanh; gán sai relevance trái với quy tắc giao lộ hiện tại. | Dừng batch, sửa 100% case cùng loại, phân tích nguyên nhân và review lại toàn bộ ảnh rủi ro. |
| Major | Lỗi class/attribute làm annotation sai nhưng chưa tạo nguy cơ trực tiếp như Critical. | Gộp hai vỏ đèn thành một box; dùng `straight` cho đèn tròn; dùng `off` khi thực tế không đọc được. | Trả về sửa ảnh lỗi và review mở rộng thêm 20% batch. |
| Minor | Geometry hoặc thao tác chưa đạt chuẩn nhưng không đổi ý nghĩa chính. | Box hơi rộng, chứa một phần thanh treo; sai lệch nhỏ nhưng vẫn bao đúng đầu đèn. | Sửa trực tiếp; nhắc lại rule nếu lỗi lặp từ hai lần. |
| Question | Trường hợp chưa đủ bằng chứng hoặc guideline chưa bao phủ. | Không xác định được đèn thuộc giao lộ hiện tại hay giao lộ phía xa. | Gán `unknown`/`escalate` theo guideline và chuyển QA owner quyết định; không tính là lỗi annotator trước khi có rule. |

## Metrics

| Metric | Cách tính | Vì sao phù hợp với bài toán |
|---|---|---|
| Object decision accuracy | Số quyết định LABEL/IGNORE đúng chia tổng quyết định object được review. | Đo trực tiếp việc bỏ sót hoặc tạo box sai đối tượng. |
| Attribute accuracy | Số giá trị `state`, `direction`, `relevance`, `review` đúng chia tổng giá trị được review. | Phản ánh chất lượng thông tin downstream sử dụng. |
| Geometry pass rate | Số box ôm đúng phần đầu đèn, không chứa cột/nền đáng kể và thỏa ngưỡng >5 px chia tổng box review. | Kiểm quy tắc hình học mà accuracy thuộc tính không phát hiện được. |
| Escalation compliance | Số case cần escalate được gán `review=escalate` chia tổng case cần escalate trong mẫu QA. | Bắt lỗi do mặc định `review=none` gây ra. |

**Critical defect escape rate:** số lỗi Critical còn tồn tại sau self-QC chia tổng quyết định Critical được QA review. Mục tiêu bắt buộc là 0%. Ngoài ra báo riêng `state` accuracy trên các đầu đèn relevant vì đây là tổ hợp có rủi ro downstream cao nhất.

## Quality gate

Threshold là đề xuất của nhóm, không phải chuẩn ngành. Giải thích trade-off cost/risk.

```text
PASS if:
  - Critical defect escape rate = 0%.
  - Object decision accuracy >= 95%.
  - Attribute accuracy >= 95% và state accuracy trên đèn relevant = 100%.
  - Geometry pass rate >= 90%.
  - Escalation compliance = 100%.
REWORK if:
  - Không có lỗi Critical nhưng ít nhất một metric còn lại thấp hơn ngưỡng.
  - Sửa 100% ảnh lỗi và review thêm 20% batch hoặc tối thiểu 5 ảnh.
REJECT / ESCALATE if:
  - Có ít nhất một lỗi Critical sau vòng rework; hoặc
  - Cùng một guideline gap xuất hiện từ hai ảnh trở lên; hoặc
  - Không có đủ bằng chứng để chốt gold decision.
```

Các ngưỡng ưu tiên an toàn vì nhầm màu hoặc nhầm đèn có thể ảnh hưởng quyết định dừng/đi. Geometry cho phép ngưỡng thấp hơn một chút vì sai lệch nhỏ quanh vỏ đèn thường ít nghiêm trọng hơn sai class hoặc state. Dataset LISA chỉ có 30 frame cùng cảnh nên review 100% calibration và blind là khả thi; kết quả QA không đại diện cho giao lộ hay điều kiện ánh sáng khác.
