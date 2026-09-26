# Edge-case library

Tối thiểu **8 card**, khuyến nghị 10–12. Một edge case tốt là case mà hai annotator hợp lý có thể làm khác nhau nếu
guideline chưa rõ. Tám ảnh dễ có label rõ ràng không được tính là edge-case library.

Cần có đủ độ đa dạng: occlusion / truncation / small-far · ambiguous semantics · conflicting road elements · **một case
critical-risk** · **một case guideline cho phép escalation**.

File này là kho nội bộ của nhóm, **không gửi cho peer**. Card dùng ảnh example/calibration thì chép rule + ví dụ sang
`02_guideline.md` (mục 7 và 9) để peer đọc được. Card về ảnh blind chỉ nằm ở đây, và decision của nó phải có trong
`gold_decisions.csv` trước `make freeze`.

`make status` đếm số dòng `CASE ID:` đã điền (đã thay placeholder). Copy khối dưới cho mỗi case.

---

CASE ID: TODO
Sample: TODO (sample_id)
Scene: TODO
Observation: TODO — thấy gì trong ảnh
Decision: TODO — LABEL / IGNORE / UNKNOWN / ESCALATE
Expected: TODO — class, attribute, geometry cụ thể
Rationale: TODO — gắn với downstream contract ở `01_problem_statement.md`
Common mistake: TODO
Diversity: TODO — occlusion / small_far / ambiguity / conflict / critical / escalation / …

---
---

CASE ID: EC01
Sample: LISA01 (example)
Scene: Giao lộ chiều tối, một cần treo có ba đầu đèn đang đỏ
Observation: Đầu đèn trái sáng mũi tên rẽ trái màu đỏ; hai đầu còn lại sáng đèn tròn đỏ
Decision: LABEL
Expected: 3 box; đầu trái `red`/`arrow_left`/`not_relevant`; hai đầu tròn `red`/`circle`/`relevant`
Rationale: Planner cần phân biệt đèn cho hướng rẽ trái với đèn cho hướng đi thẳng của xe mình
Common mistake: Gán cả ba đầu là `relevant` vì cùng màu đỏ
Diversity: conflict / critical

---

CASE ID: EC02
Sample: LISA30 (example)
Scene: Cùng giao lộ, đèn tròn đã chuyển xanh, mũi tên trái vẫn đỏ
Observation: Hai trạng thái khác nhau trên cùng một cần treo
Decision: LABEL
Expected: Đầu trái `red`/`arrow_left`/`not_relevant`; hai đầu tròn `green`/`circle`/`relevant`
Rationale: Nếu annotator gán một màu chung cho cả cần treo, planner nhận tín hiệu sai cho làn đi thẳng
Common mistake: Gán `red` cho đèn tròn vì thấy mũi tên đỏ bên cạnh
Diversity: conflict / critical

---

CASE ID: EC03
Sample: BDD12 (example)
Scene: Ngã tư đô thị ban ngày, trước vạch qua đường
Observation: Hộp đèn trên cột phải hiện bàn tay màu cam (đèn đi bộ); không thấy đầu đèn xe nào
Decision: IGNORE đèn đi bộ + ESCALATE ảnh
Expected: Không có box `traffic_light`; có tag `image_escalate`
Rationale: Đèn đi bộ không điều khiển xe; giao lộ phía trước không có đèn xe nhìn thấy nên planner không có căn cứ
Common mistake: Vẽ đèn đi bộ thành `traffic_light` `state=red`
Diversity: conflict / escalation

---

CASE ID: EC04
Sample: BDD06 (example)
Scene: Cao tốc, biển cảnh báo vàng hình thoi hai bên
Observation: Không có giao lộ, không có đèn tín hiệu
Decision: IGNORE
Expected: Không box, không tag
Rationale: Ảnh negative để mô hình không học nhầm biển vàng thành đèn vàng
Common mistake: Gắn `image_escalate` cho mọi ảnh không có đèn
Diversity: negative

---

CASE ID: EC05
Sample: BDD02 (calibration)
Scene: Đại lộ đô thị, nhiều đầu đèn ở nhiều khoảng cách
Observation: Có đầu đèn xanh gần, đầu đèn trên cần vươn xa, vài vỏ đèn vàng nhìn nghiêng ở mép phải
Decision: LABEL đèn đủ ngưỡng và quay mặt về phía xe; IGNORE vỏ nhìn nghiêng và đèn dưới ngưỡng
Expected: Chưa chốt — dùng để đo bất đồng về ngưỡng 20 px và rule nhìn nghiêng
Rationale: Hai annotator hợp lý dễ đếm khác nhau số đầu đèn ở cảnh này
Common mistake: Vẽ cả vỏ đèn nhìn nghiêng vì thấy màu vỏ vàng
Diversity: small_far / conflict

---

CASE ID: EC06
Sample: BDD07 (calibration)
Scene: Phố dân cư ban ngày, đèn trên cột trái và trên cần vươn bên phải, thêm đèn của giao lộ xa
Observation: Đèn gần và đèn giao lộ xa cùng màu
Decision: LABEL
Expected: Chưa chốt — đo bất đồng về rule "giao lộ gần nhất"
Rationale: Gán `relevant` cho đèn giao lộ xa là lỗi critical với planner
Common mistake: Gán `relevant` cho mọi đèn cùng màu nhìn thấy phía trước
Diversity: conflict / small_far

---

CASE ID: EC07
Sample: BDD26 (blind)
Scene: Ban đêm, đường đô thị
Observation: Một đầu đèn xanh sáng rõ trên cột bên trái (tâm khoảng x=488, y=72, đĩa sáng khoảng 20 px); bên dưới có đèn đi bộ bàn tay cam; phía xa có vài đèn đỏ/xanh rất nhỏ (khoảng 5 px); trên kính có vài đốm sáng phản chiếu
Decision: LABEL 1 đèn; IGNORE đèn đi bộ, đèn xa dưới ngưỡng và phản chiếu
Expected: 1 box `green`/`circle`/`relevant`, box ôm đĩa sáng không tính quầng
Rationale: Đây là đèn điều khiển làn xe mình ở giao lộ gần nhất; sai màu hoặc relevance là lỗi critical
Common mistake: Vẽ box ôm cả quầng sáng; vẽ thêm đèn đi bộ màu cam thành đèn đỏ
Diversity: critical / low_visibility

---

CASE ID: EC08
Sample: BDD18 (blind)
Scene: Ban đêm, trước vạch qua đường
Observation: Cột bên phải có hộp đèn đi bộ (bàn tay cam và hình người trắng, khoảng x=1150–1210, y=262); xa phía trước có hai đèn xanh rất nhỏ (đĩa sáng khoảng 7–8 px) của giao lộ sau; không thấy đầu đèn xe nào của giao lộ gần nhất
Decision: IGNORE đèn đi bộ; không đèn nào relevant; ESCALATE ảnh
Expected: Không có box nào `relevance=relevant`; có tag `image_escalate`
Rationale: Gán `relevant` + `green` cho đèn giao lộ xa sẽ khiến planner cho xe đi qua vạch khi chưa biết tín hiệu tại vạch
Common mistake: Vẽ hai đèn xanh xa và gán `relevant`
Diversity: ambiguity / critical / escalation

---

CASE ID: EC09
Sample: BDD21 (blind)
Scene: Đường ven công viên ban ngày, đường cong sang trái
Observation: Một đầu đèn xanh treo trên cần vươn (tâm khoảng x=336, y=292, vỏ khoảng 14 × 28 px); bên trái có vỏ đèn vàng nhìn nghiêng; một đốm xanh rất nhỏ sau tán cây
Decision: LABEL 1 đèn; IGNORE vỏ nhìn nghiêng và đốm dưới ngưỡng
Expected: 1 box `green`/`circle`, relevance không phải `not_relevant`; box ôm vỏ, không gồm cần treo
Rationale: Đèn nhỏ nhưng đủ ngưỡng; kiểm tra rule ngưỡng và geometry trên vật thể nhỏ
Common mistake: Box gồm cả đoạn cần treo phía trên; vẽ thêm vỏ đèn nhìn nghiêng
Diversity: small_far / geometry

---

CASE ID: EC10
Sample: BDD11 (blind)
Scene: Góc phố dân cư ban ngày, trước vạch qua đường
Observation: Hộp đèn ở cột góc phải hiện bàn tay đỏ (đèn đi bộ); không thấy đầu đèn xe
Decision: IGNORE + ESCALATE ảnh
Expected: Không box `traffic_light`; có tag `image_escalate`
Rationale: Đèn đi bộ đỏ không có nghĩa xe phải dừng; gán nhầm làm mô hình học sai
Common mistake: Vẽ hộp bàn tay đỏ thành `traffic_light` `state=red`
Diversity: conflict / negative

---

CASE ID: EC11
Sample: BDD14 (blind)
Scene: Cao tốc nhiều làn, cầu vượt phía xa
Observation: Không có giao lộ và không có đèn tín hiệu
Decision: IGNORE
Expected: Không box, không tag
Rationale: Kiểm tra annotator không escalate thừa và không vẽ đèn phanh
Common mistake: Vẽ đèn phanh đỏ của xe phía trước
Diversity: negative
