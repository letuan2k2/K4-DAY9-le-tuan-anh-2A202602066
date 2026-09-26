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
- **Tên task calibration:** `team05-lisa-calib-v2`. Dữ liệu export của 2 annotator lưu tại `project/06_calibration_exports/`.
- **Guide của task:** Đã tích hợp nội dung `02_guideline.md` (v2) vào Guide của task trước khi chạy calibration và handoff.
- **Chế độ:** Shape. Mỗi ảnh được đánh giá độc lập; không dùng Track và không truyền kết quả giữa các frame LISA.

## Setup test

Setup test được thực hiện bởi Lê Tuấn Anh (QA owner) đối với task do Trần Minh Nhật (CVAT owner) thiết lập vào ngày 2026-09-26. Các câu hỏi kiểm tra xác nhận người gán nhãn nắm rõ quy trình:

1. Dùng class `traffic_light` và công cụ Rectangle/Shape: **ĐẠT**.
2. Chỉ giữ box khi nhận diện được đầu đèn và cả hai cạnh đều lớn hơn 5 px trên ảnh gốc: **ĐẠT**.
3. Điền đủ 4 thuộc tính `state`, `direction`, `relevance`, `review` cho từng box: **ĐẠT**.
4. Dùng `unknown` khi đã nhận diện được đèn nhưng không đọc được thuộc tính: **ĐẠT**.
5. Dùng `review=escalate` khi bằng chứng xung đột hoặc guideline không giải quyết được: **ĐẠT**.

**Trạng thái:** Hoàn thành (PASSED). Toàn bộ 5 tiêu chí đều chính xác; không có thay đổi schema phát sinh. Cấu hình task CVAT đã sẵn sàng và được nghiệm thu trước khi bước vào calibration và freeze.
