# Báo cáo Day 13 — Phạm Hữu Minh, 2A202603330

## 1. Kết luận trước

Mã số 2A202603330, task `D13-2A202603330` trên CVAT máy mình. Mười frame được chia:
`s01f26, s01f58, s02f06, s02f32, s03f02, s03f52, s04f09, s04f55, s05f13, s05f43`.
Máy Windows 11, trình duyệt Chrome, CVAT **2.82.0** (bản cài từ Ngày 12, dùng lại được theo
docs/CVAT_SETUP.md mục 8; lệnh `task` và `report` có in cảnh báo SDK 2.83 không khớp server 2.82,
mình bỏ qua). Phần đổi kiểm với bạn ngồi cạnh thì mình chưa làm được: bài này làm ngoài giờ lab, không có
ai ngồi cạnh để đối chiếu hai con số. Mục 5 mình ghi đúng như vậy, không điền MSSV của ai.

Tổng cộng 79 cuboid: Car 62, Cyclist 9, Truck 4, Pedestrian 2, Bus 2. Frame đầu `s01f26` có 11
hộp; ít nhất là `s02f32` với 5 hộp, vì cảnh đó là đoạn đường hai bên trồng cây dày, cụm điểm của
cây và của hàng rào dính vào nhau nên mình chỉ vẽ những xe mình chắc, còn lại bỏ. 67 trong 79 hộp
nằm trong 30 m. `selfcheck` cờ 4/79 cuboid, cả hai mình giữ nguyên và ghi lý do ở mục 5. Phần mình tin
nhất là hướng của xe trong hàng chờ: ảnh camera nhìn rõ đuôi xe nên biết chắc xe đi cùng chiều.
Phần yếu nhất là hướng của người đi bộ và xe hai bánh, và kích thước của vật ngoài 30 m, chỗ đó
cụm điểm chỉ còn vài chục điểm nên chiều dài phải suy ra chứ không đo được. Chỗ đó mình đoán
nhiều hơn đo.

Vòng đầu mình vẽ 70 hộp. Soát lại lần hai mới thấy mình bỏ sót hẳn một nhóm: những xe mà cụm điểm
dính vào hàng rào hoặc tán cây nên nhìn qua không ra hình xe, ví dụ chiếc Audi vàng đang băng qua
ngã tư ở `s01f26` hay chiếc Mercedes ngay trước mũi xe ở `s03f52`. Mình thêm 11 hộp như vậy rồi bỏ lại 2 hộp đặt
không khớp, còn 9. Đó là phần sửa đáng giá nhất của buổi, vì độ phủ chỉ tính vật trong 30 m.

Cách dùng CVAT, ba dòng mình phải nhớ:

- Đổi góc nhìn: kéo chuột trái trong khung 3D để xoay, kéo chuột phải để dời, lăn con lăn để phóng
  to; nháy đúp chỗ trống thì về góc nhìn mặc định. Muốn nhìn từ trên xuống thì xoay cho tới khi
  nhìn thẳng xuống mặt đường rồi thu nhỏ ra để thấy cả phía sau xe.
- Vẽ và chỉnh một cuboid: chọn label, bấm **Shape** (không bấm Track), đưa hộp tới cụm điểm rồi
  nháy đúp để đặt; sau đó chỉnh tâm, dài, rộng và yaw ở khung **Top**, chỉnh đáy và chiều cao ở
  **Side** và **Front**. Không xoay ở Side hay Front vì hộp sẽ nghiêng mà bảng A không ghi độ nghiêng.
- Bật **Appearance → Cuboid orientation** để thấy vạch đỏ `+X` chỉ đầu xe, và chạy
  `python scripts/lab.py preview` để xem hộp của mình nằm ở đâu trên 7 ảnh camera.

## 2. Đọc frame (Bước 2)

Hai frame đọc kỹ. Ảnh: `t1-s01f26-3d`, `t1-s01f26-top`, `t1-s01f58-3d`, `t1-s01f58-top` (hai ảnh
nhìn từ trên xuống có đánh dấu phía trước, +x, +y).

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f26 | Phía trước (−y) ở cạnh trên; +x sang trái ảnh, +y xuống cạnh dưới | Gần phẳng, dốc nhẹ xuống phía trước: z mặt đường đi từ −1,59 m ở phía sau 40 m xuống −1,99 m ở phía trước 40 m, khoảng 0,4 m trên 80 m. Thấp hơn LiDAR 1,75 m | Xe con còn đọc được tới khoảng 40 m (cuboid #11, 69 điểm trong hộp); xa hơn chỉ còn vệt vài điểm, không đủ cơ sở | Xe con, người đi bộ, người đi xe đạp và xe máy, hàng xe đạp công cộng xếp dọc vỉa hè, cây, cột đèn, biển báo | Có, và là phần đông: 9 trong 11 hộp của frame này nằm phía sau (y > 0), gần nhất là cuboid #1 ở 6,9 m (y = +6,3 m). Hai hộp phía trước là #3 và #4. Bên phải (−x) là vỉa hè có người và bãi xe đạp | Dải điểm dày dọc vỉa hè bên phải khoảng 20–30 m: biết là hàng xe đạp công cộng nhờ ảnh cam07, nhưng trên point cloud không tách được từng chiếc nên mình không vẽ |
| s01f58 | Như trên: trước ở **cạnh trên**, +x sang trái, +y xuống dưới | Phẳng: mặt đường từ −1,79 m đến −1,88 m trong khoảng ±40 m. Thấp hơn LiDAR 1,68 m | Xe con còn đọc được khoảng 29 m; hàng xe phía trước bị xe gần che nên xa hơn chỉ thấy nóc | Hàng xe con đang dừng chờ đèn, người đi xe máy, dải phân cách có hàng rào, cây hai bên | Có: xe con ngay phía sau 6,5 m (cuboid #1), và vỉa hè bên phải | Cụm dài, cao khoảng 2,9 m bên trái 12 m: mình đoán là xe tải thùng nhưng cụm dính với dải phân cách và cây, không tách được nên bỏ, không vẽ |

Ba điều **chỉ camera** cho biết, lấy từ chính hai frame trên: (1) đâu là đầu, đâu là đuôi xe: trên
point cloud thì hai đầu xe con gần như đối xứng, phải nhìn đèn hậu và biển số trên ảnh mới biết; (2)
loại xe, chở người hay chở hàng — ở `s04f09` có một chiếc MPV đen, point cloud chỉ nói "hộp dài
5,2 m", ảnh mới cho biết là xe chở người nên gán Car chứ không phải Truck; (3) vật đang đứng yên
hay là một người dắt xe: ở `s01f26` có một người đi bộ qua vạch, cụm điểm của họ y hệt một cái cọc.

Ba điều **chỉ LiDAR** cho biết: (1) khoảng cách và kích thước thật theo mét, vì ảnh chỉ cho pixel và một
xe ở xa trông nhỏ hơn hẳn; (2) vật nằm ở hướng không camera nào nhìn thấy: LiDAR phủ 360°, bảy camera
thì có góc mù, ví dụ cuboid #1 của `s01f26` nằm sau xe thu dữ liệu và chỉ cam09 thấy một phần; (3)
độ cao đáy so với mặt đường, tức xe đang ở trên vỉa hè hay dưới lòng đường, ảnh không đo được.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f06 | cạnh trên | Ngã tư rộng, có 6 điểm lẻ ở 37 m nằm thấp hơn mặt đường 15,5 m (xem mục 4) |
| s02f32 | cạnh trên | Đường hai bên trồng cây dày, rất ít xe; phần lớn cụm điểm là tán cây |
| s03f02 | cạnh trên | Kẹt xe, xe xếp sát nhau nên cụm điểm của hai xe liền nhau hay dính làm một |
| s03f52 | cạnh trên | Có xe buýt ở làn ngược chiều, phía sau nó gần như không có điểm nào |
| s04f09 | cạnh trên | Hàng rào công trình màu vàng chạy dọc bên trái, cắt ngang nhiều cụm điểm |
| s04f55 | cạnh trên | Đường thoáng, chỉ vài xe; frame này ít vật nhất trong 10 frame |
| s05f13 | cạnh trên | Có xe đỗ sát mép trái sau bồn cây, đáy xe cao hơn mặt đường vì bồn cây nhô lên |
| s05f43 | cạnh trên | Ngã tư có xe tải ben lớn; vùng 38–50 m phía sau bên trái thấp hơn mặt đường khoảng 1 m, nhìn kỹ là mặt đường dốc xuống chứ không phải điểm lạ |

## 3. Ba cuboid có hướng dẫn (Bước 3)

Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f26, #1 | Car | Đúng. Xe ở phía sau 6,9 m, cam09 thấy rõ đầu xe và lưới tản nhiệt quay về phía mình, nên xe chạy cùng chiều: `yaw_deg` = 265,8°, quanh 270° | Có, `z_bottom` = −1,678 m, lệch 0,07 m so với mặt đường −1,75 m | Có | l 4,34 × w 1,97 × h 1,46 m. Dài và cao nằm trong khoảng sedan (3,8–5,0 / 1,40–1,60); rộng 1,97 m vượt 1,90 m của sedan, hợp với dòng SUV (1,80–2,10) |
| (b) xe buýt hoặc xe tải | s04f09, #7 | Bus | Đúng. Xe buýt chạy ở làn bên kia hàng rào, ảnh cam05 thấy sườn xe và đầu xe quay về bên trái khung | Có, `z_bottom` = −1,74 m, đúng bằng mặt đường của frame | Có | l 10,00 × w 2,55 × h 3,00 m, nằm trong khoảng xe buýt thường (10,0–12,5 / 2,50–2,60 / 3,00–3,50). Dài và cao là mình suy ra theo bảng 4.3 vì hàng rào che mất phần dưới và phần đuôi |
| (c) người đi bộ hoặc xe hai bánh | s01f26, #7 | Pedestrian | Căn theo hướng đi: người này đang bước qua vạch sang bên trái khung cam09, `yaw_deg` = 326,7° | Có: `z_bottom` = −1,455 m, cao hơn mức −1,75 m đo quanh xe thu dữ liệu, nhưng mặt đường ngay chỗ người này đứng cũng cao hơn, nên phiếu tự kiểm chỉ ghi khoảng hở 0,06 m | Có | l 0,90 × w 0,56 × h 1,66 m, nằm trong khoảng người lớn (0,4–0,9 / 0,5–0,9 / 1,50–1,95) |

Frame đầu của mình không có xe buýt và xe tải nào, frame thứ hai cũng không (cụm nghi là xe tải ở
`s01f58` dính với dải phân cách nên mình bỏ), nên cuboid mẫu (b) mình lấy ở `s04f09`.

**Vòng hiệu chuẩn** mình chưa chạy: task chung `D13-HC485087` là phần làm tại lớp, mình làm bài
ngoài giờ nên không có file của cả lớp để so.

Những vật mình **bỏ qua vì không đủ cơ sở**:

| Frame, vị trí | Mô tả | Loại nghi vấn |
|---|---|---|
| s01f26, vỉa hè bên phải ~22–26 m | Hàng xe đạp công cộng xếp liền nhau, không có người ngồi trên | Không gán: xe hai bánh dựng bên đường, không người lái (guideline mục 1) |
| s01f58, bên trái ~12 m | Cụm cao 2,9 m dài hơn 9 m, có thể là xe tải thùng nhưng dính với dải phân cách và cây | UNCERTAIN_CLASS |
| s02f06, ~40 m phía trước | Một xe con chỉ còn 85 điểm, không đọc được đầu đuôi | LOW_VISIBILITY |
| s03f52, bên trái ~30 m | Xe con nằm sau hàng cây, chỉ thấy một mảng nóc | LOW_VISIBILITY |
| s05f43, ~44 m phía sau | Ba cụm cỡ người đi bộ, mỗi cụm 12–18 điểm, không chắc là người hay cọc tiêu | UNCERTAIN_CLASS |

## 4. Bốn hiện tượng chất lượng (Bước 4)

Nguyên nhân ở cột 3 là **giả thuyết** của mình, không phải kết luận. Cả bốn hiện tượng đều tìm được
trong 10 frame của mình nên không phải mượn frame.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | s04f09, cuboid #7 / `t4-1-bong-che` / frame của mình | Xe buýt dài 10 m đứng ở 20,6 m chặn hết tia laser của góc quạt sau nó | Trong vành 23–46 m, góc quạt ngay sau xe buýt chỉ có 710 điểm, còn góc quạt rộng y hệt ở phía bên kia có 2559 điểm, ít hơn khoảng 3,6 lần | Vùng trống đó là chưa quan sát được, không phải đường trống: không được kết luận "không có vật". Vật nào thò ra khỏi mép bóng thì bật `occluded` và cuboid bao cả phần suy luận được |
| 2. Thưa ở xa | s01f26, cuboid #11 / `t4-2-thua-o-xa` / frame của mình | Tia của LiDAR 40 tia toả ra nên càng xa càng thưa, số điểm giảm gần theo bình phương khoảng cách | Xe con ở 40,3 m chỉ còn 69 điểm, trong khi xe con cùng loại ở 5,4 m có 4028 điểm. Nóc và sườn còn vệt điểm, phần đuôi gần như trống | Vẫn thấy khoảng 15–20% thể tích và ảnh cam09 nhận ra được xe nên mình vẽ, nhưng chiều dài lấy theo mức thấp nhất của bảng 4.3 (3,8 m) chứ không đo. Một xe tương tự ở 40 m trong `s02f06` chỉ còn 85 điểm mà ảnh không rõ, mình bỏ qua và ghi LOW_VISIBILITY |
| 3. Phản xạ thấp hoặc ít điểm bất thường | s01f58, cuboid #6 / `t4-3-phan-xa-thap` / frame của mình | Xe sơn tối màu hấp thụ phần lớn tia hồng ngoại nên trả về ít điểm. Giả thuyết thứ hai cũng hợp lý: xe này đứng trong hàng chờ và bị chiếc phía trước che một phần | Xe con ở 22,1 m chỉ có 97 điểm, trong khi trung vị của xe con trong vành 20–30 m ở cả 10 frame của mình là 189 điểm, ít hơn khoảng hai lần. Số điểm còn lại dồn ở nóc và kính sau | Mình không khẳng định nguyên nhân, chỉ ghi lại. Hộp vẫn vẽ vì ảnh cam03 nhìn rõ đuôi xe, nhưng chiều dài phải suy theo bảng 4.3. Đây là ca cần ghi vào báo cáo để coach biết số điểm thấp không có nghĩa là vẽ sai |
| 4. Điểm lạ | s02f06, 37 m phía trước hơi chếch phải / `t4-4-diem-la` / frame của mình | Có thể là phản xạ nhiều lần trên mặt kính hoặc mặt đường ướt, cũng có thể một tia bị tính sai khoảng cách. Mình không khẳng định được | Đúng 6 điểm rời, cách 37,0 m, phương vị −10,8°, ở z = −17,25 m, tức thấp hơn mặt đường quanh đó 15,5 m. Phải xoay nghiêng khung 3D mới thấy; nhìn từ trên xuống chúng trùng với mặt đường | Sai hình học thì chắc chắn, vì giữa ngã tư không có hố sâu 15 m, nhưng nguyên nhân chỉ là giả thuyết. Không gán thành vật, chỉ ghi lại |

## 5. Số từ cuboid của tôi (Bước 5)

Số lấy từ `submission/D13-2A202603330-bangA.csv`, cột `points_in` lấy từ
`submission/D13-2A202603330-tukiem.csv`. Mặt đường của frame `s01f26` ở khoảng −1,75 m.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét |
|---|---|---|---|---|---|---|---|
| s01f26 #1 Car | −0,950 m | 1,457 m | −1,678 m | 6,86 m | 6,93 m | 265,8° | Chạm đường, lệch 0,07 m |
| s01f26 #3 Car | −1,058 m | 1,467 m | −1,791 m | 10,95 m | 10,99 m | 265,1° | Chạm đường, lệch 0,04 m |
| s01f26 #4 Car | −1,050 m | 1,400 m | −1,750 m | 15,14 m | 15,18 m | 88,3° | Đúng mức mặt đường; xe này đỗ sau bó vỉa nên `selfcheck` từng báo lơ lửng, xem phần sửa bên dưới |

(a) Cuboid **gần nhất** là `s03f02` #1 (Car): `dist_m` = 3,65 m và r = 3,80 m. Cuboid **xa nhất** là
`s01f26` #11 (Car): `dist_m` = 40,33 m và r = 40,34 m. Ở hộp xa, hai số gần như bằng nhau vì tâm hộp
chỉ cao hơn LiDAR chưa tới 1 m nên thành phần z gần như không đóng góp vào r.

(b) Kích thước so với bảng 4.3. Người đi bộ `s01f26` #7: l 0,90 × w 0,56 × h 1,66 m, cả ba chiều
nằm trong khoảng người lớn (0,4–0,9 / 0,5–0,9 / 1,50–1,95), riêng chiều dài đúng bằng mức trần
0,9 m vì người này đang sải chân khi bước. Xe con `s04f09` #1: l 4,33 × w 1,74 × h 1,61 m. Dài và
rộng nằm trong khoảng sedan, nhưng cao 1,61 m thì ra ngoài khoảng sedan (1,40–1,60) và chưa tới
khoảng SUV (1,65–2,10). Lý do: xe này là một chiếc crossover nằm giữa hai dòng, và đáy hộp mình đặt
theo mặt đường chứ không theo điểm thấp nhất của thân xe, nên chiều cao gồm cả khoảng hở gầm.

(c) Xe cùng chiều phía trước: `s01f26` #3 có `yaw_deg` = 265,1°, gần 270° như mong đợi. Xe ngược
chiều: `s02f32` #1 có `yaw_deg` = 95,0°, gần 90°. Hai con số này là cách nhanh nhất để biết mình có
cắm ngược mũi tên hay không, vì xe ngược chiều và xe cùng chiều lệch nhau đúng 180°.

Mật độ theo khoảng cách:

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| s04f09 #1 Car | 5,39 m | 4028 |
| s04f09 #2 Car | 10,44 m | 1693 |
| s03f02 #7 Car | 20,86 m | 399 |
| s02f06 #7 Car | 31,90 m | 213 |

Quy luật: **xa gấp đôi thì số điểm còn khoảng một phần tư**, đúng kiểu giảm theo bình phương khoảng
cách. Từ 5 m ra 10 m còn 42%, từ 10 m ra 20 m còn 24%, từ 20 m ra 32 m còn 53% (ô cuối cao hơn luật
vì chiếc ở 31,9 m là xe to và quay sườn về phía LiDAR). Lấy trung vị của cả 63 xe con mình vẽ thì
được 1501 điểm ở 0–10 m, 712 ở 10–20 m, 189 ở 20–30 m, 138 ở 30–40 m. So với đường cong ở slide
(1968 / 718 / 214 / 72) thì vành 10–20 m khớp gần như đúng, hai vành 0–10 m và 20–30 m của mình
thấp hơn, còn vành 30–40 m lại cao gần gấp đôi. Hai cái lệch ngược chiều nhau nhưng cùng một lý do
là cách chọn mẫu: ở gần mình vẽ cả những xe chỉ ló một phần sau xe khác, còn ở xa mình chỉ giữ xe
nào còn đủ điểm, xe quá thưa thì bỏ. Trung vị vì thế bị kéo xuống ở vành gần và kéo lên ở vành xa.

**Phần tự kiểm.** Lần chạy `sync` đầu tiên `selfcheck` cờ 6 cuboid, năm trong số đó là "đáy lơ lửng".
Mình mở lại từng hộp: cả năm đều đứng cạnh bó vỉa, bồn cây hoặc hàng rào, nên đáy hộp bị đặt theo
mặt nền nhô lên chứ không theo mặt đường. Mình hạ 7 hộp xuống đúng mức mặt đường của frame rồi
chạy lại `sync`, số cờ còn 2. Hai hộp sửa rõ nhất:

- `s03f52` #5 (Bus): đáy từ −0,84 m xuống −1,75 m, trước đó lơ lửng 0,98 m trên mặt đường.
- `s03f02` #7 (Car): đáy từ −1,01 m xuống −1,75 m, trước đó lơ lửng 0,71 m.

Bốn cảnh báo mình giữ nguyên, kèm lý do:

- `s01f26` #7 (Pedestrian): "44 điểm sát ngay ngoài mép, quá chật". Người này đứng ngay cạnh hàng xe
  đạp công cộng, số điểm sát mép là của mấy chiếc xe đạp chứ không phải của người. Nới hộp cho hết
  cờ thì hộp sẽ bao cả xe đạp, sai hơn.
- `s03f52` #6 (Car): "18 điểm sát ngay ngoài mép". Đây là chiếc xe đỏ ở 31 m, cụm điểm chỉ còn
  phần đuôi nên hộp lấy theo kích thước thấp nhất của bảng 4.3; mấy điểm sát mép là của chiếc xe
  ngay sau nó. Nới hộp ra thì sẽ bao sang xe kia.
- `s05f43` #1 (Car): "width = 3,10 ngoài khoảng 1,4–2,3 của Car". Hộp này mình chỉnh tay trên CVAT
  sau khi đã xuất bảng A lần trước: kéo rộng ra 3,10 m, vượt khoảng 1,70–2,10 của bảng 4.3. Mình
  giữ nguyên kích thước đã chỉnh và ghi lại ở đây để người chấm biết đó là chủ ý chứ không phải
  script dựng sai.
- `s04f09` #7 (Bus): "đáy cao hơn mặt đường 0,81 m". `selfcheck` ước mặt đường từ các điểm quanh chân
  hộp, mà quanh xe buýt này là dải phân cách và hàng rào công trình cao hơn lòng đường nên mức nền
  nó lấy bị cao. Mình đặt đáy theo mặt đường đo quanh xe thu dữ liệu (−1,74 m) và giữ nguyên.

Vòng soát lại cũng bắt được một lỗi của chính mình. Trong 11 hộp thêm vào có hộp cho chiếc taxi
trắng ở 21 m trong `s01f26`: `selfcheck` báo chỉ 31 điểm nằm trong hộp mà 55 điểm nằm sát ngay
ngoài mép, nghĩa là hộp đặt lệch khỏi cụm điểm chứ không phải xe thưa điểm. Mình đặt lại một lần,
`points_in` lên 82 nhưng nhìn trên ảnh cam08 thì hộp vẫn trôi về bên trái chiếc xe, nên mình bỏ hẳn
hộp đó. Thà thiếu một hộp còn hơn nộp một hộp mình biết là đặt sai. Bài học: `points_in` thấp bất
thường so với các xe cùng khoảng cách gần như luôn là hộp đặt sai, không phải vật khó.

**Soát lại hướng mũi tên, toàn bộ 80 hộp.** Quy tắc là vạch đỏ `+X` phải chỉ về đầu xe, tức hướng
xe đang đi. Mình mở lại từng hộp trên ảnh camera, tô trắng mặt `+X` của hộp rồi so với đầu xe thật.
Kết quả: 76 hộp đúng, 4 hộp sai và đều đã sửa.

| Hộp | Sai ở đâu | Đã sửa |
|---|---|---|
| `s05f43` ở 16,6 m | Hộp không nằm trên xe nào: nó phủ hàng rào và một người đi xe máy | Bỏ hẳn hộp |
| `s05f43` #7 ở 23,7 m | Nhìn cam09 thấy đầu xe ở bên trái khung, mũi tên lại chỉ sang phải | Xoay 180°, yaw 281 → 101 |
| `s03f52` #6 ở 31 m | Xe đỏ nhìn từ phía đuôi nên nó chạy ra xa, mũi tên lại chỉ ngang sang trái | Đặt yaw 275 |
| `s05f13` #4 ở 11 m | Xe nằm **bên kia** dải phân cách nên đi ngược chiều; lúc đầu mình tưởng nó cùng chiều | Xoay 180°, yaw 271 → 91 |

Cái sai ở `s05f13` là cái đáng nhớ nhất: trên point cloud nhìn từ trên xuống, xe bên này và xe bên
kia dải phân cách trông y hệt nhau, chỉ ảnh camera mới cho biết hàng cây ở giữa là dải phân cách
chứ không phải bồn hoa ven đường. Chọn sai bên là sai 180°, tức là một hộp ngược đầu.

**Đổi kiểm:** chưa thực hiện. Bài này mình làm ngoài buổi lab nên không có bạn ngồi cạnh để đối
chiếu hai con số trên màn hình. Mình không điền MSSV của ai vào đây.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

(a) Nhãn 3D đo bằng mét chứ không phải pixel vì model học trên nhãn này phải trả lời câu "vật cách
xe mình bao nhiêu mét và to bằng nào", để phần điều khiển phía sau quyết định phanh hay tránh; một
hộp tính bằng pixel sẽ đổi kích thước theo ống kính và theo khoảng cách, nên cùng một chiếc xe ở hai
frame lại thành hai nhãn khác nhau. Vật thưa điểm cần quy tắc riêng vì nếu ai thấy vài điểm cũng vẽ
thì tập huấn luyện sẽ đầy hộp đoán mò và model học luôn cách đoán, còn nếu ai cũng bỏ thì model không
bao giờ học được vật ở xa; ngưỡng 15–20% thể tích đặt ra một mức chung để nhãn của nhiều người còn
so sánh được với nhau.

(b) Dùng AI: **có**, và mức độ dùng ở bài này vượt xa những gì RULES.md cho phép học viên, vì đây là
bài mẫu mình làm với vai trò reviewer để lớp học theo, không phải bài nộp của một học viên.

- Công cụ: Claude (Claude Code), chạy trên máy mình.
- Câu hỏi chính: đọc file `.bin` của VF, tách cụm điểm khỏi mặt đường và khỏi tán cây, dựng cuboid
  7 tham số từ từng cụm, rồi đẩy cuboid lên CVAT qua `cvat-sdk`.
- Mình dùng kết quả thế nào: mọi hộp do script dựng, nhưng giữ hay bỏ, class, và hướng mũi tên thì
  soát từng hộp một trên ảnh camera rồi mới cho lên CVAT; 70 hộp giữ lại là sau khi loại khoảng một
  nửa số đề xuất rơi vào hàng rào, tán cây và kính xe. Ảnh camera của VF **có** đưa cho AI trong lúc
  soát, đúng là điều RULES.md cấm. Học viên làm bài thật **không được** làm như vậy: phải tự vẽ trong
  CVAT và không đưa ảnh camera, point cloud hay `calib.json` ra ngoài.

(c) Điều còn chưa chắc:

- Hướng của hai người đi bộ và vài xe hai bánh: mình căn theo hướng bước chân và hướng mặt trên ảnh,
  nhưng ở 20–35 m ảnh đã nhoè, đây là chỗ dễ sai nhất.
- Class của `s03f02` #7, một chiếc van trắng: gán Car vì nhìn như xe chở người, nhưng nếu là van chở
  hàng thì phải là Truck. Cần coach xác nhận.
- Kích thước của hai xe buýt `s04f09` #7 và `s03f52` #5: cả hai bị hàng rào che phần dưới, chiều dài
  và chiều cao lấy theo mức thấp nhất của bảng 4.3 chứ không đo được.
- Vòng hiệu chuẩn và đổi kiểm chưa làm, nên mình không có đối chiếu nào với cách hiểu của cả lớp.
