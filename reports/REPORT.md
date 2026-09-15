# Báo cáo Ngày 3 — Tracking Annotation

Họ tên / nhóm: `NGÔ VĂN HƯNG`
Ngày: `DAY03-15/9/2026`

---

## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | CVAT / khác: `CVAT` |
| Thời gian gán `clip_02` (warm-up) | `45` phút |
| Thời gian gán `clip_01` | `120` phút |
| Số track đã vẽ trong `clip_01` | `8` |
| Số keyframe trung bình mỗi track | `~8` |

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

1. `Clip-01 / Frame 100 / ID 4`: xe bị che một phần nhẹ. Tôi giữ continuity của ID khi phần còn lại của xe vẫn đủ rõ để xác định đây là cùng một vehicle.

2. `Clip-01 / Frame 100 / ID 5`: xe bị che rồi xuất hiện lại. Tôi giữ nguyên ID khi có thể liên kết object trước và sau đoạn bị che dựa vào vị trí, hướng di chuyển và đặc điểm nhìn thấy của xe.

3. `Clip-01 / Frame 136 / ID 8`: vehicle mới xuất hiện ở mép khung nên cần xác định thời điểm bắt đầu track. Trường hợp này khó ở chỗ lúc mới xuất hiện chỉ nhìn thấy một phần nhỏ của xe; guideline chưa quy định cụ thể mức độ quan sát bao nhiêu thì được xem là đủ bằng chứng để bắt đầu track.

Khi kiểm tra lại video, tôi cũng kiểm tra các đoạn vehicle rời khỏi khung. Hai đoạn `ID 4` ở frame `149–151` và `ID 8` ở frame `169–171` vẫn còn một phần vehicle trong khung, nên không xem đây là lỗi chỉ vì bbox còn xuất hiện ở các frame này.

---

## 2. Tự kiểm và kiểm chéo

Tôi thực hiện tự kiểm theo 3 lượt:

- Lượt 1 — kiểm ID: Tôi tua lại toàn bộ clip, tập trung vào continuity của 8 track, đặc biệt ở các đoạn xe bị che hoặc đi gần nhau. Kết quả không phát hiện trường hợp đổi ID trong annotation. Kết quả evaluation cũng ghi nhận `IDSW = 0` và không có fragmented GT track.{index=2}

- Lượt 2 — kiểm frame đầu/cuối: Tôi kiểm các đoạn vehicle mới xuất hiện và các đoạn rời khỏi khung. Trường hợp đáng chú ý nhất là `ID 6 / frame 82–100`, trong đó track bắt đầu khá sớm khi xe mới chỉ xuất hiện một phần ở mép phải. Tôi đánh dấu đây là điểm cần xem lại về rule “first appearance”. Ngược lại, `ID 4 / frame 149–151` và `ID 8 / frame 169–171` không được xem là lỗi vì vehicle vẫn còn một phần trong khung hình.

- Lượt 3 — kiểm frame giữa: Tôi tập trung vào các đoạn có bbox IoU thấp trong evaluation, gồm `ID 5` ở các frame `83, 84, 85, 96` và `ID 6` ở các frame `108, 109, 110`. Đây là các vị trí bbox cần chú ý vì IoU với gold chỉ khoảng `0.506–0.599`. Tuy nhiên, IoU thấp hơn không tự động có nghĩa annotation sai, nên tôi không tự ý sửa nếu chưa có lỗi hình học rõ ràng khi xem lại frame. Evaluation cũng không ghi nhận ID switch hay track bị đứt ở các đoạn này. 

Sau 3 lượt kiểm, tôi ghi nhận:
- `ID 6 / frame 82–100`: cần review lại thời điểm bắt đầu track.
- `ID 4 / frame 149–151`: không phải lỗi.
- `ID 8 / frame 169–171`: không phải lỗi.
- Các bbox có IoU thấp quanh `83–110`: cần chú ý khi review, nhưng chưa đủ căn cứ để kết luận là lỗi annotation.
- Không phát hiện lỗi đổi ID trong bản annotation.

Kiểm chéo với: `CHƯA CÓ DỮ LIỆU về người kiểm chéo`. Phần đối chiếu với gold được dùng như một bước kiểm tra bổ sung, không thay cho kiểm chéo với người khác.

Số lỗi tôi tìm được trong bản của mình: `1 trường hợp cần review (ID 6 / frame 82–100)`.
Số lỗi người kiểm chéo tìm được trong bản của tôi: `CHƯA CÓ DỮ LIỆU`.

Ca nào hai người quyết khác nhau, và luật nào còn thiếu trong `GUIDELINE_MINI.md`?

`CHƯA CÓ DỮ LIỆU về quyết định khác nhau giữa hai người. Qua quá trình tự kiểm, điểm còn chưa rõ trong guideline là tiêu chí cụ thể để xác định frame bắt đầu track khi vehicle mới chỉ xuất hiện một phần ở mép ảnh. Ngoài ra cần quy định rõ hơn cách xác định frame cuối khi vehicle vẫn còn một phần trong khung.`

---

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence | Giá trị |
| --- | --- |
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` | `652bd67fe61f3f26a29b6b63826d57e8fbfd69af09ee6913daab535e15f973d5` |
| Thời điểm khóa | `2026-09-15T15:41:10.183439+00:00` |
| Số row / frame / track trước khi mở reference | `596 / 190 / 8` |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bản pre-gold | 0.8221 | 0.8087 | 0.8367 | 0.8871 | 0.9632 | 0.9250 | 0.8756 | 33 | 10 | 0 |
| Sau rework | CHƯA CÓ DỮ LIỆU | CHƯA CÓ DỮ LIỆU | CHƯA CÓ DỮ LIỆU | CHƯA CÓ DỮ LIỆU | CHƯA CÓ DỮ LIỆU | CHƯA CÓ DỮ LIỆU | CHƯA CÓ DỮ LIỆU | CHƯA CÓ DỮ LIỆU | CHƯA CÓ DỮ LIỆU | CHƯA CÓ DỮ LIỆU |

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **có** (`Gate = PASS` cho kết quả bạn vs gold).

Sau khi đọc danh sách lỗi, bạn đã sửa cụ thể những gì? Ghi theo frame và ID:

`CHƯA CÓ DỮ LIỆU về rework thực tế.`

| Loại lỗi | Frame | ID | Đã sửa thế nào |
| --- | --- | --- | --- |
| CHƯA CÓ DỮ LIỆU về rework | CHƯA CÓ DỮ LIỆU | CHƯA CÓ DỮ LIỆU | CHƯA CÓ DỮ LIỆU |

Khi kiểm tra lại video, các đoạn `ID 4 / frame 149–151` và `ID 8 / frame 169–171` không được xem là lỗi cần rework vì vehicle vẫn còn một phần trong khung hình.

Trường hợp cần chú ý thêm là `ID 6 / frame 82`, khi vehicle mới xuất hiện ở mép phải frame. Đây là trường hợp nên đối chiếu lại guideline về tiêu chí đủ bằng chứng để bắt đầu track.

---

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | `3.13.15 / 8.4.145 / 2.11.0+cpu / 0.5.13` |
| weights / hai tracker | `yolo26n.pt`; `ByteTrack control`; `BoT-SORT + ReID` |
| conf / IoU / imgsz / classes | `0.25 / 0.70 / 960 / [2,5,7]` |
| device | `cpu` |

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| bạn vs gold | 0.8221 | 0.8087 | 0.8367 | 0.8871 | 0.9632 | 0.9250 | 0.8756 | 33 | 10 | 0 |
| ByteTrack control vs gold | 0.7085 | 0.6487 | 0.7761 | 0.8463 | 0.8746 | 0.7487 | 0.8226 | 88 | 54 | 2 |
| BoT-SORT + ReID vs gold | 0.7635 | 0.7110 | 0.8204 | 0.8721 | 0.9001 | 0.7923 | 0.8595 | 91 | 26 | 2 |
| ReID vs bạn | 0.8165 | 0.7581 | 0.8800 | 0.9308 | 0.8947 | 0.7869 | 0.9246 | 83 | 41 | 3 |

---

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

`IDF1 của tôi cao hơn MOTA: 0.9632 > 0.9250. MOTA có tính đến FP, FN và IDSW; IDSW là một thành phần của MOTA, nhưng MOTA không trực tiếp và nhạy với identity consistency như IDF1. Vì vậy, MOTA có thể cao trong khi IDF1 thấp nếu detection tốt nhưng association hoặc identity consistency kém.`

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

`ByteTrack có IDF1 = 0.8746, AssA = 0.7761, IDSW = 2. ReID có IDF1 = 0.9001, AssA = 0.8204, IDSW = 2. Theo evaluation, ReID có ID switch tại frame 87 với GT track 5, từ ReID track 17 sang 18; và tại frame 113 với GT track 6, từ ReID track 24 sang 31. ByteTrack có ID switch tại frame 59 với GT track 4, từ track 14 sang 15; và tại frame 94 với GT track 5, từ track 23 sang 32. Như vậy ReID tốt hơn trên IDF1 và AssA trong kết quả này, còn IDSW không đổi. Tuy nhiên, các evaluation hiện có chỉ cung cấp các frame ID switch chứ chưa cung cấp evidence hình ảnh/video để mô tả chính xác toàn bộ frame sequence trước và sau các điểm này. Đây không phải causal experiment cô lập effect của ReID vì ByteTrack và BoT-SORT là hai tracker implementation khác nhau.`

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

`Từ ByteTrack sang ReID, DetA tăng từ 0.6487 lên 0.7110, FP tăng nhẹ từ 88 lên 91, FN giảm từ 54 xuống 26, AssA tăng từ 0.7761 lên 0.8204, còn IDSW giữ nguyên ở 2. Trong dataset này, ReID run có detection/coverage tốt hơn, thể hiện qua FN giảm và DetA tăng; association cũng tốt hơn theo AssA nhưng không giảm IDSW. Tuy nhiên, không thể quy toàn bộ thay đổi cho ReID vì hai tracker implementation khác nhau. Kết quả cho thấy vẫn còn cả vấn đề về detection (FP/FN) và association (AssA/IDSW), nhưng chưa thể định lượng phần đóng góp của từng nguyên nhân chỉ từ các metric này.`

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

`Một trường hợp đáng chú ý là frame 87 của GT track 5, tại đó ReID chuyển từ track 17 sang track 18. Đây là một ID switch của model. Khi đối chiếu với video, cần xem continuity của vehicle qua đoạn này; nếu vehicle vẫn là cùng một object thì việc ReID đổi ID là lỗi association của model, trong khi annotation giữ nguyên ID là hợp lý. Không nên kết luận chỉ dựa vào metric mà cần nhìn chuỗi frame tại vị trí này.`

**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

`Frame 82 của GT track 6 là một trường hợp tôi cần xem lại annotation. Vehicle mới xuất hiện ở mép phải frame và chỉ nhìn thấy một phần nhỏ, nên cần kiểm tra xem tại frame này đã đủ bằng chứng để bắt đầu track hay chưa. Trường hợp này liên quan trực tiếp đến rule “first appearance” trong guideline và có thể giúp xác định rõ hơn thời điểm bắt đầu một track.`

---

## 6. Nếu phải gán thêm 10 clip nữa

Bạn sẽ sửa gì trong `GUIDELINE_MINI.md`, và đổi gì trong quy trình làm việc của mình?

`Đây là recommendation cho các clip tiếp theo, không phải việc đã thực hiện. Trong GUIDELINE_MINI.md, tôi sẽ làm rõ: (1) tiêu chí “đủ bằng chứng” để bắt đầu track khi vehicle xuất hiện ở mép khung; (2) cách xử lý occlusion trên 25 frame và khi nào cần tạo ID mới; (3) cách xử lý khi hai vehicle overlap/intersect; (4) rule cho parked vehicle; và (5) keyframe density. Trong quy trình làm việc, tôi sẽ thực hiện 3 lượt self-check trước khi lock pre-gold: kiểm continuity của ID, kiểm frame đầu/cuối của track và kiểm các frame giữa/đoạn occlusion. Sau đó kiểm riêng các trường hợp vehicle mới xuất hiện ở mép frame, bị che và rời khung.`

---

## 7. Tệp đã nộp

- [ ] `annotations/clip_01/gt.txt`
- [ ] `annotations/clip_02/gt.txt`
- [ ] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
- [ ] `GUIDELINE_MINI.md` đã điền
- [ ] `outputs/eval_vs_gold.json`
- [ ] `outputs/model_bytetrack_clip_01.txt`
- [ ] `outputs/model_reid_clip_01.txt`
- [ ] `outputs/model_run_config.json`
- [ ] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [ ] `reports/review_partner.md`
- [ ] `reports/REPORT.md` (file này)