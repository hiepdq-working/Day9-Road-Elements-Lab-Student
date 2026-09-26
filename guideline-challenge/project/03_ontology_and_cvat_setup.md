# Ontology + CVAT setup

Bảng ontology là **source of truth** cho schema CVAT: `03_cvat_labels.json` phải khớp từng dòng ở đây. Thay mọi
placeholder mới là xong (gate G2).

## Ontology table

| Name | Geometry | Type (class / attribute) | Allowed values | Default | Mutable? | Rationale |
|---|---|---|---|---|---|---|
| traffic_light | rectangle | class | — | — | — | Planner cần vị trí từng đầu đèn xe; rectangle đủ cho detector và vẽ nhanh |
| state | — | attribute của traffic_light | `__undefined__`, red, yellow, green, off, unknown | `__undefined__` | true | Thuộc tính của cùng một đầu đèn và đổi theo thời gian; tách class sẽ nhân số class lên 5 lần |
| signal_shape | — | attribute của traffic_light | `__undefined__`, circle, arrow_left, arrow_right, arrow_straight, unknown | `__undefined__` | false | Phân biệt đèn mũi tên với đèn tròn, là căn cứ chính cho relevance |
| relevance | — | attribute của traffic_light | `__undefined__`, relevant, not_relevant, unknown | `__undefined__` | false | Downstream chỉ dùng đèn relevant; unknown là đường escalate cho relevance |
| occluded | — | attribute (checkbox) | true / false | false | true | QA và mô hình cần biết box không phải toàn bộ đầu đèn |
| needs_review | — | attribute (checkbox) | true / false | false | false | ESCALATE một đèn, nhìn thấy được trong export |
| image_escalate | tag (cả ảnh) | class kiểu tag | có / không | không | — | ESCALATE cả ảnh khi không có đầu đèn xe nào điều khiển làn mình tại giao lộ gần nhất |


## Class hay attribute


`traffic_light` là class duy nhất vì mọi đầu đèn xe có cùng geometry rule và cùng QA rule. `state`, `signal_shape` và
`relevance` là attribute: chúng là thuộc tính của cùng một vật thể; `state` đổi theo frame; tách class sẽ tạo
5 × 6 × 4 = 120 tổ hợp. Đèn đi bộ **không** thành class vì downstream không dùng; guideline ghi rõ là không vẽ.
`image_escalate` là tag vì quyết định thuộc về cả ảnh, không gắn với object nào.

Default cho mọi select là `__undefined__` để annotator quên gán sẽ lộ ra trong export, thay vì tạo ra đèn đỏ hoặc xanh
"im lặng". Hai checkbox mặc định `false`. Rủi ro còn lại: annotator quên tick `needs_review`; QA plan bắt lỗi này bằng
cách kiểm mọi đèn có `relevance=unknown` phải có `needs_review=true`.

## CVAT

- **Phiên bản CVAT** (`make cvat-status`): v2.74.1 theo GUIDE (người setup xác nhận lại bằng `make cvat-status` trên máy mình)

- **Tên task calibration** (có version guideline, ví dụ `team07-calib-v1`): 

`<ten-nhom>-calib-v1-<ten-nguoi>`
`<ten-nhom>-calib-v1-<ten-nguoi>`
`<ten-nhom>-calib-v1-<ten-nguoi>`
`<ten-nhom>-calib-v1-<ten-nguoi>`
`<ten-nhom>-calib-v1-<ten-nguoi>`


- **Guide của task đã dán `02_guideline.md`?** Có, bản v1 (dán lại khi lên v2)

- **Nhóm dùng Track hay Shape, vì sao:** 

Shape. Task ảnh tĩnh; frame LISA cũng gán độc lập (mục 8 guideline)

## Setup test

Một thành viên **chưa tham gia setup** mở task và trả lời: label gì, dùng tool nào, gán attribute nào, khi nào
escalate. Ghi lại ai test và chỗ họ vấp:

TODO
