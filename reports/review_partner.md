# Peer review — Day 3

Reviewer chỉ ghi finding; tác giả tự sửa annotation và điền closure.

| Trường | Giá trị |
| --- | --- |
| Author | `NGÔ VĂN HƯNG` |
| Reviewer | `CHƯA CÓ DỮ LIỆU` |
| Pair ID | `CHƯA CÓ DỮ LIỆU` |
| CVAT version | `CHƯA CÓ DỮ LIỆU` |
| Thời điểm review | `2026-09-16` |

## Danh sách finding

Review dựa trên video `clip_01` sau khi gán nhãn, kiểm các đoạn được evaluator đánh dấu và xem frame trước/sau để tránh kết luận chỉ từ metric.

| # | CVAT frame | MOT frame | ID | Loại lỗi | Quan sát + rule áp dụng | Cách sửa đề xuất | Closure: fixed / not-a-defect / needs-review |
| ---: | ---: | ---: | ---: | --- | --- | --- | --- |
| 1 | `82` | `82` | `6` | Entry / bắt đầu track quá sớm | Ở frame 82, ID 6 mới chỉ lộ một phần rất nhỏ ở mép phải, chưa đủ rõ để chắc chắn đây là vehicle bốn bánh. Đến các frame sau object mới lộ rõ hơn. Rule: bắt đầu track từ frame đầu tiên vehicle đủ rõ để xác định. | Kiểm lại frame bắt đầu của ID 6; nếu frame 82 chưa đủ bằng chứng thì xóa bbox từ frame 82 và bắt đầu ở frame đầu tiên đủ rõ. | `needs-review` |

**Các candidate từ diagnostics đã được kiểm tra bằng hình ảnh nhưng chưa đưa thành finding:**

- `Frame 149–151 / ID 4`: vehicle vẫn còn một phần nhìn thấy ở mép trái khung; bbox bám theo phần còn nhìn thấy. Chưa xem đây là lỗi exit.
- `Frame 169–171 / ID 8`: vehicle vẫn còn một phần nhìn thấy ở mép phải khung; bbox bám theo phần còn nhìn thấy. Chưa xem đây là lỗi exit.
- `Frame 83–85 / ID 5` và `Frame 108–110 / ID 6`: bbox vẫn bám vào phần vehicle nhìn thấy; IoU thấp hơn không đủ để kết luận lỗi nếu chỉ dựa trên metric.

## Reviewer checklist

| Hạng mục | PASS / FINDING / N/A | Frame–ID–evidence |
| --- | --- | --- |
| Có tối thiểu 6 track hợp lệ; chỉ gồm xe bốn bánh | PASS | Có 8 track trong clip; các object được gán là vehicle bốn bánh. |
| Một xe giữ một ID; không reuse ID cho xe khác | PASS | Annotation vs gold có `IDSW = 0`. |
| Occlusion ngắn giữ ID; crossing không đổi ID | PASS | Không có ID switch trong annotation vs gold; các đoạn occlusion được giữ ID. |
| Entry/exit đúng; không box treo sau khi xe rời khung | FINDING | ID 6 frame 82 cần review thời điểm bắt đầu. Các candidate ID 4 frame 149–151 và ID 8 frame 169–171 vẫn còn phần vehicle nhìn thấy nên không đánh lỗi. |
| Bbox ôm phần nhìn thấy, không đoán phần bị che/ngoài khung | PASS | Các bbox được kiểm tra ở vùng occlusion/edge; chưa thấy bbox đoán phần khuất. |
| Frame giữa hai keyframe không bị interpolation drift | NEEDS-REVIEW | Video cho thấy bbox nhìn chung bám object, nhưng chưa có danh sách keyframe để kiểm interpolation một cách đầy đủ. |
| Export đúng MOT 1.1; frame bắt đầu từ 1; cột 2 là track ID | PASS | File được evaluator đọc thành 190 frames và 8 tracks; pre-gold có 596 rows. |
| Mọi finding có cách sửa và closure do tác giả điền | FINDING | Finding #1 có frame, ID và cách sửa; closure vẫn chờ tác giả xác nhận. |

## Self-QC attestation của reviewer

| Lượt | PASS / ĐÃ SỬA / NEEDS-REVIEW | Frame–ID–evidence |
| --- | --- | --- |
| 1 — identity/timeline | PASS | Không phát hiện ID switch; annotation vs gold có `IDSW = 0`. |
| 2 — endpoint/scope | NEEDS-REVIEW | ID 6 frame 82 cần kiểm thời điểm bắt đầu. ID 4 frame 149–151 và ID 8 frame 169–171 đã kiểm bằng hình ảnh, vehicle vẫn còn visible ở mép khung. |
| 3 — geometry/interpolation | NEEDS-REVIEW | Các vùng frame 83–85 ID 5 và 108–110 ID 6 cần tiếp tục kiểm nếu muốn xác nhận interpolation/geometry hoàn toàn chính xác. |

## Exit ticket

1. Finding quan trọng nhất và rule dùng để kết luận: `Frame 82 / ID 6 — bắt đầu track khi object chưa đủ rõ là vehicle bốn bánh. Rule: bắt đầu track từ frame đầu tiên object đủ rõ để xác định.`

2. Một finding tác giả đóng là `not-a-defect`, kèm lý do (nếu có): `Candidate frame 149–151 / ID 4 và frame 169–171 / ID 8: hình ảnh cho thấy vehicle vẫn còn visible ở mép khung, nên việc còn bbox không tự động là lỗi exit. Tác giả cần xác nhận closure nếu đóng not-a-defect.`

3. Một rule cần Lab Coach làm rõ (nếu có): `Cần quy định cụ thể thế nào là “đủ bằng chứng” để bắt đầu track khi vehicle chỉ mới lộ một phần nhỏ ở mép khung.`
