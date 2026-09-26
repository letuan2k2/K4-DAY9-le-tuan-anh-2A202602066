# Phản hồi blind handoff từ Team02

- **Nhóm thực hiện:** Team02
- **Bộ ảnh đã làm:** `LISA14`, `LISA16`, `LISA22`, `LISA27`
- **Kết quả bàn giao:** `peer_output/team02-blind.zip`
- **Trạng thái:** Đã hoàn thành blind handoff; Team02 xác nhận PASS.
- **Kết luận:** **PASS**

Team02 đã đọc guideline v2, tự gán nhãn và xuất kết quả mà không cần Team05 giải thích thêm. Dưới đây là phản hồi sau khi làm xong.

## Trả lời 5 câu hỏi

1. **Rule nào rõ nhất, giúp quyết định nhanh nhất?**
   Quy tắc mỗi vỏ đèn là một object rất dễ áp dụng. Ngưỡng hai cạnh phải lớn hơn 5 px cũng giúp chúng tôi quyết định khá nhanh khi gặp đèn nhỏ. Việc nói rõ đèn tròn là `non_directional`, không phải `straight`, tránh được một lỗi dễ mắc.

2. **Rule nào còn mơ hồ hoặc phải tự suy diễn?**
   Phần khó nhất là xác định một đầu đèn nhỏ ở xa có còn thuộc giao lộ hiện tại hay đã thuộc giao lộ tiếp theo. Guideline có nêu cách xử lý khi không chắc, nhưng chưa có ví dụ hình ảnh đủ gần với tình huống này.

3. **Sample nào làm guideline bị thử thách nhiều nhất?**
   `LISA27`. Các đèn nhỏ ở giữa và bên phải ảnh vẫn nhận diện được, nhưng phải quan sát khá kỹ bố cục mới quyết định chúng thuộc cùng giao lộ. Cuối cùng Team02 chọn `relevant` vì vị trí lắp đặt và pha đèn nhất quán với các đèn chính.

4. **Attribute hoặc default nào dễ gây thao tác sai?**
   `review=none` dễ bị bỏ qua vì đây là giá trị mặc định. Nếu annotator chọn `unknown` cho một thuộc tính rồi chuyển ảnh ngay, họ có thể quên cân nhắc xem object đó có cần `review=escalate` hay không.

5. **Một thay đổi cụ thể để annotator mới ít phải hỏi hơn?**
   Nên thêm một sơ đồ quyết định ngắn cho `relevance`: trước hết xác định đèn thuộc giao lộ hiện tại hay giao lộ phía xa; nếu chưa chắc thì dùng `unknown + escalate`; chỉ khi chắc là cùng giao lộ nhưng không rõ hướng làn mới gán tất cả là `relevant`. Một ví dụ đúng và một ví dụ cần escalate là đủ.

## Gợi ý xử lý cho Team05

| Nội dung | Nhận định của Team02 | Gợi ý |
|---|---|---|
| Cách nhận biết “giao lộ hiện tại” | Đây là chỗ guideline còn thiếu ví dụ trực quan | Bổ sung decision tree và hai ảnh minh họa ở bản v3 |
| Default `review=none` | Hợp lý nhưng dễ bị giữ nguyên theo thói quen | Thêm một bước kiểm tra `review` vào checklist cuối ảnh |

Nhìn chung guideline dùng được và đủ rõ để hoàn thành bài độc lập. Hai góp ý trên nhằm giảm thời gian do dự cho annotator mới, không phải lỗi làm cản trở bàn giao.
