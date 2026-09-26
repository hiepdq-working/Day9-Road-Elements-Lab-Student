# Revision log

Guideline v1 = bản nháp đầu; v2 = sau calibration nội bộ; v3 = sau blind handoff. Mỗi lần tăng `Version` trong
`02_guideline.md`, thêm một hoặc nhiều dòng vào bảng: đổi gì và vì sao, kèm bằng chứng (sample_id, dòng
calibration report, câu hỏi trong clarification log, feedback của peer).

Cột Version ghi dạng `v1`, `v2`, `v3` — `make status` tìm dòng bảng có `v2` và dòng có `v3`.

| Version | Đổi gì | Vì sao | Bằng chứng |
|---|---|---|---|


=>

Guideline v1 = bản nháp đầu; v2 = sau calibration nội bộ; v3 = sau blind handoff.

| Version | Đổi gì | Vì sao | Bằng chứng |
|---|---|---|---|
| v1 | Bản nháp đầu: 1 class `traffic_light` + tag `image_escalate`; attribute state, signal_shape, relevance, occluded, needs_review; rule relevance theo giao lộ gần nhất; ngưỡng 20 px (vỏ) / 10 px (đĩa sáng đêm); không vẽ đèn đi bộ | Viết trước khi label thử, từ downstream contract ở `01_problem_statement.md` | Ví dụ LISA01, LISA30, BDD12, BDD06; bản gốc lưu ở `02_guideline_v1.md` |

