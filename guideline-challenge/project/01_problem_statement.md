# Problem statement + downstream contract

Tối đa nửa trang, viết **trước khi mở CVAT**. Đây là bằng chứng của gate G1 (topic lock). Thay mọi placeholder
mới là xong.

## Bài toán

TODO — một câu: road element nào, trong tình huống nào, khó ở đâu. "Label traffic signs" là quá rộng; "hierarchical
sign taxonomy cho biển nhỏ/xa/bị che" là đủ cụ thể.

=>
Gán trạng thái (đỏ / vàng / xanh / tắt) và **mức liên quan tới làn của xe mình (ego relevance)** cho từng đầu đèn
tín hiệu giao thông dành cho xe, tại giao lộ có nhiều đầu đèn, trong điều kiện ngày, chạng vạng và đêm. Khó ở chỗ: nhiều
đầu đèn cùng lúc (đèn mũi tên rẽ trái cạnh đèn đi thẳng, đèn của giao lộ xa hơn), đèn đi bộ trông giống đèn xe, đèn nhỏ ở
xa, và ban đêm chỉ thấy quầng sáng, không thấy vỏ đèn.

## Downstream contract

1. **Downstream task / model / user là ai?** 
Module lập kế hoạch dừng/đi (stop-go planner) của xe tự hành, và mô hình
   phát hiện + phân loại trạng thái đèn huấn luyện từ dữ liệu này. Planner chỉ dùng các đèn `relevance=relevant`.

2. **Output annotation nào thực sự cần?** Rectangle cho mỗi đầu đèn xe quay mặt về phía xe mình; attribute `state`,
   `signal_shape`, `relevance`; cờ `occluded`, `needs_review`; tag cả ảnh `image_escalate`.
3. **Failure nào gây hậu quả lớn nhất?** (đây sẽ là decision `critical` trong gold) 
(1) Đèn đang điều khiển làn xe mình bị gán sai trạng thái, nhất là đỏ ↔ xanh.
(2) Gán `relevant` cho đèn không điều khiển làn mình (đèn mũi tên rẽ trái, đèn giao lộ xa) hoặc ngược lại. Cả hai
   có thể khiến xe vượt đèn đỏ hoặc phanh gấp vô cớ. Đây là các decision `critical`.


4. **Khi ambiguity không resolve được, ai / ở đâu là escalation path?*

Annotator không đoán: đầu đèn không xác định được điều
   khiển làn nào thì `relevance=unknown` + tick `needs_review`; cả ảnh không xác định được đèn nào điều khiển xe mình
   thì gắn tag `image_escalate`. Reviewer (QA owner) xử lý hàng đợi escalate theo `05_qa_plan.md`; nếu cần rule mới
   thì spec owner nâng version guideline.

## Scope

- **Trong scope (bắt buộc label):** đầu đèn tín hiệu dành cho xe, mặt đèn quay về phía xe mình, đủ lớn (vỏ đèn có cạnh
  dài ≥ 20 px; ban đêm không thấy vỏ thì đĩa đèn sáng ≥ 10 px).


- **Ngoài scope (ignore):** 
đèn đi bộ (bàn tay / hình người), đầu đèn nhìn nghiêng hoặc từ phía sau, phản chiếu trên
  kính / xe / nhà, đèn phanh, đèn đường, biển quảng cáo phát sáng, đèn nhỏ hơn ngưỡng.

- **Geometry tolerance:** box ôm sát vỏ đèn nhìn thấy (ban đêm: ôm đĩa đèn sáng, không tính quầng); mỗi cạnh lệch
  ≤ 3 px so với gold là đạt.


## Output chấm được

LABEL = có box `traffic_light`; IGNORE = không có box (theo danh sách ngoài scope); UNKNOWN = giá trị `unknown` của
attribute; ESCALATE một đèn = `needs_review=true`; ESCALATE cả ảnh = tag `image_escalate`. Blind test chấm: số đầu đèn
được vẽ, `state`, `relevance`, việc không vẽ đèn đi bộ, tag escalate, và geometry của box. Tất cả đều có trong export
CVAT for images 1.1.

## Dữ liệu và giới hạn

BDD100K (ảnh có đèn, gồm đêm và chạng vạng) và LISA (một clip 30 frame ban ngày/chiều tối). Dùng khoảng 15 ảnh:
4 example, 6 calibration, 5 blind. Blind chỉ lấy BDD để là cảnh chưa thấy. Giới hạn: LISA chỉ là một giao lộ nên ví dụ
LISA không đại diện; số ảnh đêm ít (2 ảnh); ảnh tĩnh nên không dùng được ngữ cảnh chuyển trạng thái theo thời gian.