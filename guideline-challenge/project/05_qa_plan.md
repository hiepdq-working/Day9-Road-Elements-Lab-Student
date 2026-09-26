# QA plan + quality gates

Không được viết "reviewer kiểm tra lại". Phải có sampling, metric, threshold và action khi fail. Thay mọi placeholder
mới là xong (gate G6).

## Flow

Guideline → Calibration → Production → Self-QC → Review → Rework → Quality Gate. Ghi cụ thể cho project của nhóm:

- **Ai review, review bao nhiêu:** 
QA owner review; annotator không tự review lô của mình. Lô đầu của mỗi annotator
  mới: review 100% ảnh. Sau khi annotator đạt PASS hai lô liên tiếp: review 20% ảnh mỗi lô, cộng 100% ảnh thuộc nhóm rủi
  ro ở dòng dưới.


- **Chọn sample theo rule nào** (random, theo tag rủi ro, theo annotator mới…): 

luôn review 100% ảnh có ít nhất một đèn `relevance=relevant`, ảnh đêm/chạng vạng, ảnh
  có `needs_review=true` hoặc tag `image_escalate`. Phần còn lại chọn ngẫu nhiên đủ 20%.
- **Self-QC trước khi nộp lô** (annotator tự làm): bật **Attribute annotation**, rà từng đèn, không còn giá trị
  `__undefined__`; mọi đèn `relevance=unknown` đều có `needs_review=true`.

- **Issue được ghi ở đâu, đóng thế nào:** 

mỗi lỗi là một Issue trong CVAT gắn vào object hoặc ảnh, ghi severity ở dòng
  đầu (`[critical]`, `[major]`, `[minor]`, `[question]`). Annotator sửa rồi reply; reviewer kiểm lại và bấm Resolve.
  Issue chỉ đóng khi reviewer resolve.

- **Khi phát hiện guideline gap thì update và version ra sao:** 

ssue `[question]` lặp lại từ hai annotator trở lên cho
  cùng tình huống được coi là guideline gap. Spec owner sửa `02_guideline.md`, tăng version, ghi một dòng vào
  `08_revision_log.md` kèm sample_id, dán lại Guide vào task. Lô đã làm theo rule cũ không bị tính lỗi cho phần rule mới.

## Defect severity

Nhóm được đổi mapping nếu downstream contract khác, nhưng phải giải thích và chốt trước khi QA.

| Severity | Định nghĩa cho project này | Ví dụ | Action mặc định |
|---|---|---|---|
| Critical | Sai `state` hoặc sai `relevance` của đèn điều khiển làn xe mình; bỏ sót đèn relevant; gán `relevant` cho đèn không điều khiển làn mình | Đèn tròn đỏ relevant bị gán `green`; mũi tên rẽ trái gán `relevant` khi xe đi thẳng | Sửa ngay; lô chứa lỗi không được PASS; annotator được coaching lại rule relevance |
| Major | Sai inclusion/exclusion hoặc attribute của đèn `not_relevant`; thiếu hoặc thừa `image_escalate`; còn `__undefined__` | Vẽ đèn đi bộ; bỏ sót đèn not_relevant đủ ngưỡng; quên tick `needs_review` | Sửa trong vòng rework |
| Minor | Geometry lệch quá 3 px nhưng vẫn ôm đúng đầu đèn; `occluded` sai | Box đêm ôm một phần quầng sáng | Ghi nhận, sửa nếu còn thời gian; tính vào metric geometry |
| Question | Annotator không chắc và rule chưa trả lời | Đèn nhấp nháy vàng; đèn tạm tại công trường | Chuyển spec owner; có thể thành rule mới |

## Metrics

| Metric | Cách tính | Vì sao phù hợp với bài toán |
|---|---|---|
| TODO | TODO | TODO |

| Metric | Cách tính | Vì sao phù hợp với bài toán |
|---|---|---|
| Relevant-light state accuracy | Số đèn relevant có `state` đúng / tổng đèn relevant trong sample review | Đây chính là thứ planner dùng |
| Relevance accuracy | Số đèn có `relevance` đúng / tổng đèn trong sample review | Lỗi relevance gây quyết định sai dù màu đúng |
| Inclusion precision / recall | Precision = box đúng là đèn xe đủ ngưỡng / tổng box; recall = đèn xe đủ ngưỡng được vẽ / tổng đèn xe đủ ngưỡng | Bắt lỗi vẽ đèn đi bộ, phản chiếu, và lỗi bỏ sót |
| Geometry compliance | Box có mọi cạnh lệch ≤ 3 px / tổng box được review | Tolerance đã ghi trong guideline |
| Undefined rate | Số attribute còn `__undefined__` / tổng attribute | Bắt lỗi quên gán do default |
| Escalation consistency | Ảnh gắn `image_escalate` đúng rule / tổng ảnh thuộc tình huống escalate | Escalate thiếu làm planner nhận dữ liệu sai; escalate thừa làm tắc hàng đợi review |

Metric high-risk tách riêng: **critical defect escape rate** = số lỗi critical reviewer phát hiện ở vòng QA sau (hoặc
trong audit) mà vòng review trước đã để lọt / tổng lỗi critical. Mục tiêu 0.

## Quality gate

Threshold là đề xuất của nhóm, không phải chuẩn ngành. Giải thích trade-off cost/risk.

```text
PASS if:
    0 lỗi critical trong sample review
  AND relevant-light state accuracy = 100%
  AND relevance accuracy ≥ 95%
  AND inclusion precision ≥ 95% AND inclusion recall ≥ 90%
  AND geometry compliance ≥ 90%
  AND undefined rate = 0%

REWORK if: 

có 1–2 lỗi critical, hoặc bất kỳ metric nào dưới ngưỡng PASS nhưng relevance accuracy ≥ 85%


REJECT / ESCALATE if: ≥ 3 lỗi critical trong một lô, hoặc relevance accuracy < 85%, hoặc cùng một lỗi critical lặp
lại ở ≥ 2 annotator (dấu hiệu guideline gap → chuyển spec owner trước khi làm tiếp)
```


Trade-off: 

ngưỡng cho đèn relevant đặt tuyệt đối (100%, 0 lỗi critical) vì một lỗi màu ở đèn điều khiển làn mình có
thể dẫn tới vượt đèn đỏ, và số đèn relevant mỗi ảnh ít (thường 1–3) nên review 100% không tốn nhiều. Recall inclusion
chỉ đòi 90% vì đèn nhỏ sát ngưỡng 20 px khó đo chính xác và bỏ sót một đèn `not_relevant` ít hậu quả. Geometry 90% vì
box đêm quanh đĩa sáng khó đạt ±3 px ổn định; siết hơn sẽ tăng rework mà không cải thiện quyết định của planner.

