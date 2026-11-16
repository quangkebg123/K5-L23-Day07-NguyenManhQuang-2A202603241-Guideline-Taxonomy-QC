# Guideline gán nhãn — <v1.0> — <2A202603241 - Nguyen Manh Quang | T068>

> Copy file này (hoặc `guideline_v1.0_agreed.md`) rồi viết. Một guideline đủ gồm **5 phần**. Câu nào của v0
> làm hai người đọc ra hai cách khác nhau thì phải viết lại cho một cách hiểu duy nhất.

- Phiên bản: v1.0
- Ngày: 2026-11-16
- Người viết: Nguyen Manh Quang — 2A202603241

## 1. Mục tiêu

Nhãn này dùng để huấn luyện mô hình phát hiện người và phương tiện cơ giới trong ảnh đường phố. Mục tiêu là xác định vị trí từng đối tượng bằng bounding box và gán đúng lớp, để kết quả có thể dùng trong các tác vụ như phân tích giao thông.

Các lỗi cần hạn chế là bỏ sót người hoặc phương tiện nhìn thấy được, gán nhầm lớp, và vẽ box quá lệch hoặc không bao sát đối tượng. Những lỗi này làm giảm độ tin cậy của việc phát hiện và đo lường đối tượng trong ảnh.

## 2. Lớp và thuộc tính

### Lớp

| Lớp             | Định nghĩa                                                                                                            | Ví dụ                                                                | Phản ví dụ / ca dễ nhầm                                                                                                                   |
| --------------- | --------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| `car`           | Phương tiện cơ giới đường bộ chủ yếu dùng để chở người, có thân xe kín hoặc khoang hành khách.                        | Sedan, hatchback, SUV chở người.                                     | Xe tải chở hàng (`truck`), xe thùng nhỏ (`van`), xe hai bánh (`motorcycle`).                                                              |
| `truck`         | Phương tiện cơ giới đường bộ có kết cấu chính để chở hàng, thường có thùng hoặc sàn chở hàng.                         | Xe tải thùng, xe tải có sàn chở hàng.                                | Xe con (`car`), xe van có khoang hàng kín (`van`). Pickup có thể khó phân loại; đánh dấu `can_review` nếu không xác định rõ theo kết cấu. |
| `van`           | Phương tiện cơ giới có thân dạng hộp, khoang hành khách hoặc khoang hàng kín, kích thước nhỏ hơn xe tải thông thường. | Minivan, xe giao hàng thân kín cỡ nhỏ.                               | Sedan/SUV (`car`), xe tải thùng lớn (`truck`).                                                                                            |
| `motorcycle`    | Phương tiện cơ giới hai bánh được thiết kế để người điều khiển ngồi/đứng trên xe; gồm xe máy và mô tô.                | Xe máy, mô tô hai bánh.                                              | Xe đạp không động cơ; ô tô hoặc xe ba bánh (chưa có lớp phù hợp thì không tự gán sang lớp khác).                                          |
| `person`        | Người thật có thể quan sát được trong ảnh, dù bị che một phần.                                                        | Người đi bộ, người lái xe nhìn thấy được.                            | Tượng, ma-nơ-canh, hình người in trên biển/quảng cáo.                                                                                     |
| `ignore_region` | Vùng có đối tượng mục tiêu nhưng không thể gán từng đối tượng thành box đáng tin cậy theo guideline này.              | Một vùng đông/che khuất đến mức không thể tách riêng từng đối tượng. | Không dùng thay cho box của một đối tượng nhìn thấy rõ; không dùng chỉ vì chưa chắc lớp.                                                  |

Đối tượng không thuộc các lớp trên thì không gán vào một lớp gần giống chỉ để đủ nhãn. Nếu không thuộc lớp nào nhưng vùng đó cần được loại khỏi đánh giá, dùng `ignore_region` theo quy tắc trên; trường hợp chưa rõ thì đánh dấu `can_review` và ghi chú.

### Thuộc tính

| Thuộc tính       | Mức cố định        | Định nghĩa và ngưỡng đo được                                                                                                                                                                                                                                                          |
| ---------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `occluded_level` | `0`, `1`, `2`, `3` | Ước lượng phần diện tích toàn vật bị vật khác che khuất; không tính phần bị cắt bởi mép ảnh. `0`: dưới 10%; `1`: từ 10% đến dưới 30%; `2`: từ 30% đến dưới 60%; `3`: từ 60% trở lên nhưng vẫn nhận ra được lớp. Nếu không nhận ra được lớp thì không đoán lớp, đánh dấu `can_review`. |
| `truncated`      | `no`, `yes`        | `yes` nếu vật bị biên ảnh cắt qua và phần vật còn tiếp tục ra ngoài khung; nếu vật nằm trọn trong ảnh thì `no`. Không tính che khuất bởi vật khác là truncation.                                                                                                                      |
| `can_review`     | tick / không       | Tick khi có điểm chưa chắc về lớp, ranh giới box hoặc thuộc tính; ghi ngắn gọn lý do để nhóm xem lại. Không dùng tick thay cho việc áp dụng quy tắc đã rõ.                                                                                                                            |

## 3. Quy tắc vẽ box

- Vẽ một box chữ nhật song song với cạnh ảnh cho mỗi đối tượng thuộc lớp; box ôm sát phần nhìn thấy, không bao gồm bóng đổ, vùng trống hoặc phần bị che mà không nhìn thấy.
- Nếu đối tượng bị che, chỉ vẽ quanh các phần nhìn thấy được; không tự đoán đường viền bị khuất. Các đối tượng riêng biệt vẫn có box riêng, không gộp chung vì chồng lấp.
- Nếu đối tượng bị cắt bởi mép ảnh, vẽ phần box nhìn thấy đến sát biên ảnh, không kéo box ra ngoài ảnh, và đặt `truncated=yes`.
- Không gán người/phương tiện cho hình in, ảnh phản chiếu, tượng, ma-nơ-canh, đồ chơi hoặc mô hình thu nhỏ. Nếu không xác định được đó là vật thật hay hình/mô hình, đánh dấu `can_review` và ghi lý do.
- Người đang ngồi hoặc đi trên phương tiện được gán box `person` riêng; phương tiện có box riêng. Hàng hóa thông thường không được gán thành phương tiện. Một phương tiện khác được chở chỉ có box riêng nếu nhìn thấy đủ để nhận diện và phân loại.
- Vật cao dưới 20 px: không gán từng vật, dùng `ignore_region`. Vật cao từ 20 px trở lên nhưng nhoè hoặc tối vẫn được gán nếu nhận diện được lớp và xác định được phần nhìn thấy để vẽ box. Nếu không đủ căn cứ, không đoán; đánh dấu `can_review`. Chỉ dùng `ignore_region` khi không thể tách riêng các đối tượng trong một vùng, không dùng thay cho một đối tượng chưa rõ lớp.

## 4. Ca khó

Các ca dưới đây là đề xuất AI từ ảnh; cần người viết tự xem lại, xác nhận hoặc sửa trước khi nộp.

| Ảnh       | Tình huống                                                      | Đề xuất quyết định ban đầu (AI; cần xác nhận)                                                                                                                   | Lý do / câu hỏi cần họp                                                                                                             |
| --------- | --------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `img_002` | Một xe tải chở nhiều ô tô trên sàn vận chuyển.                  | Gán box `truck` cho xe chở và box `car` riêng cho từng ô tô nhìn thấy rõ; không gộp thành một box.                                                              | Các ô tô vẫn là những đối tượng thuộc lớp `car` dù đang được vận chuyển; cần chốt cách xử lý xe bị che một phần.                    |
| `img_004` | Hai xe tuk-tuk ba bánh chở khách.                               | Đánh dấu `can_review`; chưa ép vào `motorcycle` vì định nghĩa lớp hiện chỉ nói xe hai bánh. Đưa nhóm chốt phạm vi lớp hoặc hướng xử lý phương tiện chưa có lớp. | Taxonomy hiện không có lớp riêng cho xe ba bánh; dùng nhãn gần nhất có thể làm các thành viên gán khác nhau.                        |
| `img_020` | Cảnh đường và phương tiện chỉ xuất hiện qua gương chiếu hậu.    | Đề xuất không gán các hình ảnh phản chiếu như đối tượng riêng; đánh dấu `can_review` để xác nhận quy tắc phản chiếu.                                            | Tránh đếm một đối tượng phản chiếu như thể nó xuất hiện trực tiếp trong cảnh; cần áp dụng nhất quán với các bề mặt phản chiếu khác. |
| `img_027` | Tượng/đài tưởng niệm có hình dạng một xe pickup.                | Không gán tượng là `car` hay `truck`.                                                                                                                           | Đây là vật thể điêu khắc, không phải phương tiện thật; hình dạng giống xe không đủ để thuộc lớp phương tiện.                        |
| `img_033` | Ma-nơ-canh trong cửa hàng, phía sau kính có hình ảnh trưng bày. | Không gán ma-nơ-canh hoặc người trong hình trưng bày là `person`.                                                                                               | Đây không phải người thật trong cảnh; tránh nhầm vật trưng bày/hình ảnh với đối tượng cần phát hiện.                                |
| `img_038` | Nhiều xe đồ chơi bày trong tủ kính cửa hàng.                    | Không gán xe đồ chơi là `car`.                                                                                                                                  | Đây là mô hình thu nhỏ, không phải xe thật; cần áp dụng cùng nguyên tắc với đồ chơi và mô hình khác.                                |

## 5. QC và phiên bản

- **IAA:** IoU trung bình của các cặp box ghép được ≥ 0.85 và tỷ lệ cùng lớp ≥ 95%. Ngưỡng IoU theo chiều cao box: ≥ 0.85 nếu box cao ≥ 50 px; ≥ 0.7 nếu cao từ 20 đến dưới 50 px. Luôn ghi số cặp box làm mẫu số.
- **QC lô 40 ảnh:** kiểm tra mọi ảnh từng bị gắn cờ và 20 ảnh ngẫu nhiên; ghi rõ mẫu số. Tỷ lệ ảnh có ít nhất một lỗi phải ≤ 2% để Release, đồng thời không có lỗi lặp lại cùng kiểu và IAA phải đạt ngưỡng. Nếu vượt ngưỡng, lỗi lặp lại hoặc IAA không đạt thì Rework; guideline chưa rõ hoặc dữ liệu hỏng thì Hold.
- **Ca mơ hồ:** người gán đánh dấu `can_review` và ghi lý do; nhóm thảo luận, chốt ở Pha 2 hoặc calibration Pha 5. Người được nhóm giao ghi quyết định vào bảng calibration.

| Phiên bản | Thay đổi                                                                                | Lý do                                                                         |
| --------- | --------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| v0 → v1.0 | Làm rõ mục tiêu, lớp/thuộc tính, quy tắc box, ca khó và ngưỡng QC theo đề xuất hiện có. | Chuẩn bị bản cá nhân để xin nhóm/giảng viên xem xét; chưa có đồng thuận nhóm. |
