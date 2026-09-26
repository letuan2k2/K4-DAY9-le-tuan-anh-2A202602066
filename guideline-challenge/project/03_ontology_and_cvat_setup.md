# Ontology + CVAT setup

Bảng ontology là nguồn tham chiếu chính cho schema CVAT. Tên nhãn, thuộc tính, giá trị và mặc định phải khớp với `03_cvat_labels.json`.

## Ontology table

| Name | Geometry | Type (class / attribute) | Allowed values | Default | Mutable? | Rationale |
|---|---|---|---|---|---|---|
| `traffic_light` | Rectangle | Class | — | — | Không áp dụng | Một box cho mỗi vỏ/đầu đèn giao thông dành cho xe. |
| `state` | Thuộc box `traffic_light` | Attribute / select | `red`, `yellow`, `green`, `off`, `unknown` | `unknown` | Không | Trạng thái có thể khác giữa các ảnh; `unknown` tránh ép đoán khi không đọc được. |
| `direction` | Thuộc box `traffic_light` | Attribute / select | `non_directional`, `left`, `right`, `straight`, `unknown` | `unknown` | Không | Mô tả biểu tượng trên đèn; đèn tròn là `non_directional`. |
| `relevance` | Thuộc box `traffic_light` | Attribute / select | `relevant`, `not_relevant`, `unknown` | `unknown` | Không | Ghi mức liên quan theo quy tắc ở guideline mục 4.3. |
| `review` | Thuộc box `traffic_light` | Attribute / select | `none`, `escalate` | `none` | Không | Đánh dấu trường hợp cần QA owner xem lại. |

## Class hay attribute

`traffic_light` là class vì đây là loại đối tượng cần phát hiện và có geometry riêng. `state`, `direction`, `relevance` và `review` là attribute vì chúng mô tả cùng một đầu đèn; nếu tách thành class sẽ tạo nhiều tổ hợp như đèn đỏ–mũi tên trái–relevant và làm schema khó kiểm soát.

Ba thuộc tính nội dung dùng mặc định `unknown`. Mặc định này an toàn hơn việc chọn sẵn một màu hoặc hướng, nhưng vẫn có thể làm tăng số lượng `unknown` nếu người gán nhãn quên kiểm tra. `review` mặc định `none`; đây là điểm dễ bỏ sót nhất vì trường hợp đáng lẽ phải escalate vẫn trông hợp lệ trong export. Bước self-QC phải kiểm tra lại cả bốn thuộc tính trước khi hoàn thành ảnh.

## CVAT

- **Phiên bản CVAT:** 2.75.1, kiểm tra qua API local `/api/server/about` ngày 2026-09-26.
- **Tên task calibration dự kiến:** `team05-lisa-calib-v2`. Repo chưa có task ID hoặc export để xác nhận task đã được tạo.
- **Guide của task:** chưa có bằng chứng trong repo cho thấy `02_guideline.md` đã được dán vào CVAT Guide.
- **Chế độ:** Shape. Mỗi ảnh được đánh giá độc lập; không dùng Track và không truyền kết quả giữa các frame LISA.

## Setup test

Setup test cần được thực hiện bởi thành viên không tạo task hoặc annotator của Team02. Người test phải trả lời được:

1. Dùng class `traffic_light` và công cụ Rectangle/Shape.
2. Chỉ giữ box khi nhận diện được đầu đèn và cả hai cạnh đều lớn hơn 5 px trên ảnh gốc.
3. Điền `state`, `direction`, `relevance`, `review` cho từng box.
4. Dùng `unknown` khi đã nhận diện được đèn nhưng không đọc được thuộc tính.
5. Dùng `review=escalate` khi bằng chứng xung đột hoặc guideline không giải quyết được trường hợp.

**Trạng thái:** chưa có tên người test và biên bản thao tác trong repo, nên chưa thể coi bước setup test đã hoàn thành. Sau khi test, ghi tên người thực hiện, thời gian, câu trả lời sai và thay đổi phát sinh ngay tại mục này.
