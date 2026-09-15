# Mini annotation guideline — Ngày 3 (tracking)

> Điền file này **trong lúc gán nhãn**, không phải sau khi xong. Mỗi lần bạn dừng
> lại nghĩ "cái này tính sao nhỉ?" thì đó là một dòng phải ghi vào đây.
>
> Đây là tài liệu mà người gán nhãn tiếp theo sẽ đọc để làm giống bạn. Nếu hai
> người trong nhóm gán khác nhau, gần như luôn là vì file này chưa nói rõ — chứ
> không phải vì ai kém.

Nhóm / tên: `Ngô Văn Hưng`
Clip: `clip_01`, `clip_02`

---

## 1. Phạm vi: gán cái gì, không gán cái gì

Một lớp duy nhất: **`vehicle`** — xe bốn bánh (xe con, van, xe buýt, xe tải).

| Gán | Không gán |
| --- | --- |
| xe con, SUV, t  axi, xe bán tải | người đi bộ |
| van, minivan | xe đạp |
| xe buýt, minibus | **xe máy / mô tô** |
| xe tải, xe đầu kéo | xe trong ảnh quảng cáo, trong gương, dưới bóng nước |

Bổ sung của nhóm (nếu có): `...`

## 2. Luật ID — phần quan trọng nhất

| Tình huống | Luật của nhóm | Vì sao |
| --- | --- | --- |
| Xe bị che một phần rồi hiện lại | giữ nguyên ID nếu bị che **dưới ... frame** (mặc định của lab: 25 frame = 2 giây @ 12.5 fps) | `...` |
| Xe bị che lâu hơn ngưỡng trên | `...` | `...` |
| Xe rời khung hình rồi quay lại | mặc định: **track mới** | `...` |
| Hai xe cắt nhau / chồng lên nhau | `...` | `...` |

## 3. Luật bbox

| Tình huống | Luật của nhóm |
| --- | --- |
| Xe bị cắt bởi rìa ảnh | bbox chạm đúng rìa, không đoán phần ngoài ảnh |
| Xe bị xe khác che một phần | bbox ôm phần **nhìn thấy được** |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | bắt đầu track từ frame đầu tiên xác định được là xe bốn bánh; ngưỡng nhóm chọn: `...` |
| Xe đang đỗ, không di chuyển | `...` |
| Keyframe đặt dày ở đâu | `...` |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

Ghi **frame cụ thể** và **ID cụ thể**, không ghi chung chung.

### Ca 1
- Clip / frame / ID: `Clip-01/Frame-100/ID-4`
- Tình huống: `Xe chỉ bị che một phần rất nhỏ, như bánh xe nhưng phần thân xe còn lại vẫn đủ rõ để nhận dạng.`
- Quyết định: `Tiếp tục giữ nguyên ID và tracking bình thường.`
- Lý do: `Phần bị che không làm mất khả năng xác định identity của xe, nên không cần coi đây là một ca gián đoạn track do occlusion.`

### Ca 2
- Clip / frame / ID: `Clip-01/Frame-100/ID-5`
- Tình huống: `Xe đang nhìn thấy rõ thì bị xe khác hoặc vật cản che mất một phần, sau đó phần xe bị che xuất hiện lại.`
- Quyết định: `Giữ nguyên ID nếu có thể liên kết object trước và sau khi bị che.`
- Lý do: `Dựa vào vị trí, hướng di chuyển và đặc điểm phần xe còn nhìn thấy để xác định đó vẫn là cùng một xe.`

### Ca 3
- Clip / frame / ID: `Clip-01/Frame-136/Xe đỏ góc phải cuối - id8`
- Tình huống: `Xe vừa xuất hiện ở mép khung hình, ban đầu chỉ nhìn thấy một phần nhỏ nên chưa chắc chắn đó là xe bốn bánh.`
- Quyết định: `Chỉ bắt đầu track khi đã có đủ bằng chứng để xác định đó là vehicle; tuy nhiên guideline hiện chưa quy định cụ thể “đủ bằng chứng” là như thế nào.`
- Lý do: `Vì thiếu tiêu chí cụ thể, các annotator có thể chọn những frame bắt đầu khác nhau dù đều cho rằng mình đang làm đúng guideline.`

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

Luật nào trong file này hoá ra còn thiếu hoặc còn mơ hồ? Viết lại cho rõ:

- `**trước rework**`

- `**sau rework**`
