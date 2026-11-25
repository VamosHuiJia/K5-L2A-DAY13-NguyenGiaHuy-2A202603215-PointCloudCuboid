# Báo cáo Day 13 — Nguyễn Gia Huy, 2A202603215

## 1. Kết luận trước
**Thông tin bài làm, kết quả tự kiểm và mức độ tin cậy**

Nguyễn Gia Huy — MSSV `2A202603215`; task `D13-2A202603215` (CVAT task 44). Môi trường: Windows, Chrome, CVAT 2.74.1. Bạn hiệu chuẩn cùng: MSSV `2A202603190`.

10 frame: `s01f07`, `s01f22`, `s02f11`, `s02f39`, `s03f13`, `s03f44`, `s04f10`, `s04f29`, `s05f11`, `s05f38`.

Bài có 116 cuboid: 100 Car, 5 Bus, 3 Truck và 8 Cyclist. `s03f44` có ít nhất, gồm 6 Car và 1 Cyclist; hai xe ở 28,61 m và 31,56 m chỉ có 31 và 14 điểm, cho thấy vật xa trong frame khó đọc, có thể góp phần làm số hộp ít hơn các frame khác. Phiếu tự kiểm có 0/116 cuboid bị cảnh báo. Phần nhận dạng xe con gần đáng tin cậy hơn phần hướng và kích thước của vật thưa điểm ở xa.

**Cách dùng CVAT**

Em kéo chuột trái trong khung 3D để xoay, chuột phải để dời và dùng con lăn để phóng to, thu nhỏ; khi nhìn Top, đặt −y hướng lên để xác định phía trước.

Dùng cuboid dạng Shape, nháy đúp để đặt hộp; chỉnh tâm, chiều dài, chiều rộng và yaw ở Top, rồi chỉnh chiều cao và đáy ở Side, Front.

Bật Appearance → Cuboid orientation để xem hướng hộp, đối chiếu đầu xe với camera và xem ảnh do `preview` tạo trong `work/xem/`.

## 2. Đọc frame (Bước 2)
**Hai frame đọc kỹ**

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| `s01f07` | Phía trước −y ở cạnh trên; +x sang trái, +y xuống dưới trong `t1-s01f07-top.png` | Vùng đường gần tương đối phẳng; mốc tham chiếu khoảng 1,7 m dưới LiDAR | Ít nhất 38,21 m: Car idx 9, là hộp xa nhất đã gán trong frame | Xe con; xe lớn ở xa cần đối chiếu thêm class | Xe xanh bên trái thấy trong cam05/cam06; xe phía sau xem cam09 | Cụm mảnh ngoài hàng cây chưa đủ rõ để xác định vật riêng |
| `s01f22` | Phía trước −y ở cạnh trên; +x sang trái, +y xuống dưới trong `t1-s01f22-top.png` | Vùng đường gần tương đối phẳng; đáy xe buýt khoảng −1,737 m | Ít nhất 37,73 m: Car idx 9, là hộp xa nhất đã gán trong frame | Car, Bus, Cyclist | Xe con và xe buýt bên trái thấy ở cam05/cam06; xe phía sau xem cam09 | Vùng ít điểm ngoài phía xe buýt che chưa đủ cơ sở nhận dạng vật |

Ảnh 3D: `t1-s01f07-3d.png`, `t1-s01f22-3d.png`. Khoảng xa nhất trong bảng là khoảng cách tới tâm hộp đã gán, chưa phải giới hạn nhìn thấy của cảm biến.

**Ba điều camera cho biết và ba điều LiDAR cho biết**

Camera cho biết rõ: (1) màu xanh của xe bên trái ở `s01f07`; (2) chi tiết đầu, đuôi và kiểu thân xe; (3) vạch kẻ đường cùng hình thức bên ngoài của xe buýt, xe con ở `s01f22`. LiDAR cho biết: (1) vị trí và khoảng cách theo mét so với gốc cảm biến; (2) kích thước, độ cao 3D của vật; (3) cấu trúc không gian quanh xe ở phạm vi 360°, kể cả ngoài ảnh cam01. Ảnh camera đơn lẻ không trực tiếp cho các số đo 3D theo mét này.

**Tầm quan sát và góc mù camera**

Cam01 và cam03 nhìn phía trước, nên không thấy toàn bộ vật phía sau và hai bên. Khi đối chiếu đủ bảy camera, cam05/cam06 hỗ trợ phía trái, cam07/cam08 phía phải và cam09 phía sau. Vùng không camera nào quan sát được: khoảng chuyển tiếp giữa các camera kề nhau (ví dụ giữa cam06 trái-sau và cam09 sau, giữa cam08 phải-sau và cam09 sau), vùng sát nóc và gầm xe thu dữ liệu nằm ngoài tầm chụp, và vùng bị thân xe hoặc vật lớn che phía sau chúng theo hướng tia nhìn của camera. Chưa xác định chính xác ranh giới góc mù theo độ, nên em không coi bảy ảnh camera là quan sát đầy đủ 360°.

**Tám frame lướt**

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| `s02f11` | Cạnh trên (−y); cam01 xác nhận xe phía trước theo −y | 9 Car, 2 Cyclist, 1 Bus; Car idx 10 ở 29,55 m có 84 điểm |
| `s02f39` | Cạnh trên (−y); cam01 xác nhận xe phía trước theo −y | 7 Car, 1 Cyclist; Car idx 5 ở 30,14 m có 71 điểm, ít vật gần |
| `s03f13` | Cạnh trên (−y); cam01 xác nhận hướng di chuyển | 9 Car, 1 Truck, 1 Cyclist; Car idx 8 chỉ có 40 điểm ở 18,00 m, cần kiểm tra che khuất |
| `s03f44` | Cạnh trên (−y); cam01 xác nhận hướng −y là phía trước | 6 Car, 1 Cyclist; ít vật nhất; Car idx 6 ở 31,56 m chỉ có 14 điểm |
| `s04f10` | Cạnh trên (−y); cam01 xác nhận xe phía trước theo −y | 14 Car, 1 Bus; nhiều xe; Car idx 15 ở 45,90 m có 42 điểm |
| `s04f29` | Cạnh trên (−y); cam01 xác nhận hướng −y là phía trước | 12 Car, 1 Bus, 1 Cyclist; Car idx 14 ở 43,90 m có 52 điểm |
| `s05f11` | Cạnh trên (−y); cam01 xác nhận xe phía trước theo −y | 19 Car, 1 Truck, 1 Cyclist; nhiều nhất; có cụm điểm tách rời chưa xác định |
| `s05f38` | Cạnh trên (−y); cam01 xác nhận hướng −y là phía trước | 6 Car, 1 Truck, 1 Bus; Car idx 7 ở 40,31 m có 47 điểm |

Tám frame được đối chiếu với cam01/cam03 và ảnh BEV do `preview` tạo, trong đó đầu xe thu dữ liệu hướng lên trên. Vì vậy phía trước −y nằm ở cạnh trên của ảnh BEV. Trong CVAT, vị trí trên màn hình thay đổi khi xoay khung 3D; phải kiểm lại trục trước khi chỉnh hộp. Các số điểm lấy từ phiếu tự kiểm.

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car.png`, `t2-b-bus-truck.png`, `t2-c-ped-cyclist.png`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | `s01f07`, idx 10 | Car | Yaw 270,5°, theo −y, phù hợp đầu xe trong cam05 | z đáy −1,808 m; `ground_gap_m` −0,02 m, sát mặt đường cục bộ | Đã chọn hộp gần LiDAR, đối chiếu vị trí và kích thước với CSV; ảnh (a) được chụp lại với Top/Side/Front và cam05 | 4,650 × 1,800 × 1,520 m; trong khoảng sedan/hatchback |
| (b) xe buýt hoặc xe tải | `s01f22`, idx 3 | Bus | Yaw 270,7°, theo −y; không đổi hướng hộp | z đáy −1,737 m; khoảng hở 0,03 m, sát đường | Đã xem 3D và ba view; một bên thân có nhiều điểm, bên khuất chưa đủ rõ để đo chắc chiều rộng | 12,842 × 2,877 × 3,490 m; dài và rộng vượt khoảng xe buýt thường. Giữ nguyên chiều rộng vì chưa đủ căn cứ điều chỉnh theo điểm |
| (c) người đi bộ hoặc xe hai bánh | `s01f22`, idx 7 | Cyclist | Yaw 20,6°, phù hợp hướng chéo ra lòng đường quan sát trên cam03 phóng lớn; giữ nguyên | z đáy −1,621 m; khoảng hở 0,06 m, gần mặt đường | Đã xem ba view, cam03 phóng lớn và cam07 để đối chiếu vật ở mép phải phía trước | 2,234 × 0,755 × 1,653 m; trong khoảng xe máy có người |

`idx` là số thứ tự trong bảng A, khác ID hiển thị trên CVAT.

**Kết quả hiệu chuẩn và các vật bỏ qua vì không đủ cơ sở**

Em đã hiệu chuẩn cùng bạn có MSSV `2A202603190` và không tạo thêm task chung theo hướng dẫn của coach. Hai bài lệch nhau chủ yếu ở kích thước vật xa, ít điểm. Cả hai thống nhất nâng góc nhìn, thu nhỏ để thấy toàn cảnh, dùng hướng đường làm gợi ý về hướng vật và đối chiếu chiều dài, chiều rộng với bảng 4.3 để ước lượng phần hình dạng còn thiếu. Đầu và đuôi xe vẫn cần được kiểm tra bằng ảnh camera; hướng đường không đủ để kết luận hướng của mọi vật.

Vật bỏ qua vì chưa đủ cơ sở: ở `s05f11`, cụm điểm giữa khung 3D được khoanh xanh trong `t4-4-diem-la.png` chưa phân biệt được cây hay người đi xe hai bánh. Cụm thưa, hình dạng chưa rõ nên em chưa tạo cuboid, ghi `UNCERTAIN_CLASS` và `LOW_VISIBILITY`; chưa kết luận đây là nhiễu. Trên hai frame có hướng dẫn (`s01f07` và `s01f22`), không có vật nào bị bỏ qua vì thiếu cơ sở: mọi cụm điểm đủ rõ hình dạng trong phạm vi 50 m đều đã được gán cuboid; các cụm mảnh ngoài hàng cây ở `s01f07` không đủ hình dạng để nhận dạng vật nhưng nằm xa ngoài phạm vi.

## 4. Bốn hiện tượng chất lượng (Bước 4)

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | `s01f22` / `t4-1-bong-che.png` / task cá nhân | Giả thuyết: xe buýt chặn tia LiDAR tới vùng phía ngoài nó | Phía sau xe buýt theo hướng tia từ LiDAR có vùng thiếu điểm rõ hơn hai bên | Không coi vùng trống điểm là không có vật. Nếu vật bị che vẫn đủ cơ sở suy luận thì bao phần bị che và bật `occluded` |
| 2. Thưa ở xa | `s02f39` / `t4-2-thua-o-xa.png` / task cá nhân | Giả thuyết: khoảng cách lớn làm các tia quét trên bề mặt vật cách nhau hơn | Xe xa chỉ còn các điểm rời, hình dạng ít rõ hơn vật gần | Chỉ vẽ khi thấy khoảng 15–20% thể tích hoặc có đủ bằng chứng suy luận; nếu không thì bỏ qua, ghi LOW_VISIBILITY |
| 3. Phản xạ thấp / ít điểm bất thường | `s04f10` / `t4-3-phan-xa-thap.png` / task cá nhân | Giả thuyết: bề mặt tối, kính hoặc góc tới làm giảm điểm trả về; chưa xác định nguyên nhân | Điểm tập trung ở một phần hộp, phần thân còn lại thưa; cam07 có xe tối màu để đối chiếu | Dùng camera và ba view để kiểm vật, không nới hộp theo vùng không có điểm và không khẳng định nguyên nhân từ một ảnh |
| 4. Điểm lạ | `s05f11` / `t4-4-diem-la.png` / task cá nhân, vùng nghi vấn | Giả thuyết: điểm trả về rời rạc từ phản xạ đa đường, vật bị che một phần hoặc nhiễu cảm biến; điểm không tạo thành bề mặt liên tục như thân xe hay người, phân bố tản mát không theo hình dạng vật nào trong 5 class | Trên point cloud, cụm khoanh xanh gồm các điểm nằm rải rác ở độ cao khác nhau, không xếp thành mặt phẳng hay đường cong như bề mặt xe; mật độ thấp hơn rõ rệt so với các vật đã gán ở cùng khoảng cách; đối chiếu cam01/cam03 không nhận ra vật tương ứng ở vị trí đó (ảnh tối mờ, tầm nhìn hạn chế) | Giữ vùng nghi vấn, chưa gán cuboid hay class. Không gán điểm lạ thành vật khi chưa đủ bằng chứng; nếu là vật thật bị che thì cần thêm frame liền kề để xác nhận |

## 5. Số từ cuboid của em (Bước 5)
Nguồn số liệu: `D13-2A202603215-bangA.csv` và cột `points_in` trong `D13-2A202603215-tukiem.csv`.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| `s01f07`, idx 10, Car | −1,048 m | 1,520 m | −1,808 m | 3,65 m | 3,799 m | 270,5° | Lệch mốc −1,7 m khoảng −0,108 m; khoảng hở cục bộ −0,02 m, sát đường |
| `s01f22`, idx 3, Bus | 0,008 m | 3,490 m | −1,737 m | 6,92 m | 6,917 m | 270,7° | Lệch mốc khoảng −0,037 m; khoảng hở cục bộ 0,03 m, sát đường |
| `s01f22`, idx 7, Cyclist | −0,795 m | 1,653 m | −1,621 m | 28,49 m | 28,506 m | 20,6° | Lệch mốc khoảng +0,079 m; khoảng hở cục bộ 0,06 m, gần đường |

`z_bottom` được chép từ bảng A. Tính lại bằng các số z, h đã làm tròn có thể lệch 0,001 m; r tính từ x, y, z trong CSV nên đôi khi nhỏ hơn `dist_m` đã làm tròn 0,01 m.

**(a) Cuboid gần nhất, xa nhất; (b) kích thước so với bảng 4.3; (c) yaw của xe cùng chiều, ngược chiều**

(a) Gần nhất: Car `s01f22`, idx 1, tâm (3,037; −1,001; −0,918) m; `dist_m` = 3,20 m, r = √(3,037² + (−1,001)² + (−0,918)²) ≈ 3,327 m. Xa nhất: Car `s05f11`, idx 21, tâm (45,415; 13,794; −1,270) m; `dist_m` = 47,46 m, r ≈ 47,481 m.

(b) Cyclist `s01f22`, idx 7 có l × w × h = 2,234 × 0,755 × 1,653 m, nằm trong khoảng xe máy có người của bảng 4.3; không dùng khoảng xe đạp để so hộp này. Car `s01f07`, idx 10 có kích thước 4,650 × 1,800 × 1,520 m, cả ba chiều nằm trong khoảng sedan/hatchback.

(c) Car `s01f07`, idx 3 ở phía trước (y = −19,384 m) có yaw 265,7°, gần 270°, phù hợp chiều −y. Car idx 4 của cùng frame có yaw 91,3°, gần 90°, phù hợp chiều +y, ngược chiều xe thu dữ liệu.

**Mật độ theo khoảng cách của bốn cuboid**

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| `s01f07`, idx 10, Car | 3,65 m | 3.171 |
| `s01f07`, idx 3, Car | 19,52 m | 445 |
| `s01f07`, idx 6, Car | 29,31 m | 226 |
| `s01f07`, idx 9, Car | 38,21 m | 90 |

**Quy luật mật độ, so sánh với bài giảng và kết quả sửa sau tự kiểm**

Trong bốn xe con trên, khoảng cách từ 3,65 m đến 38,21 m tăng khoảng 10,47 lần, còn số điểm giảm từ 3.171 xuống 90, tức còn khoảng 2,84%. So với trung vị trong bài giảng: 3.171 > 1.968 điểm ở 0–10 m; 445 < 718 điểm ở 10–20 m; 226 gần 214 điểm ở 20–30 m; 90 > 72 điểm ở 30–40 m. Xu hướng chung là càng xa càng ít điểm, nhưng đây là bốn xe riêng lẻ, không phải trung vị của từng khoảng; che khuất, góc bề mặt và kích thước cũng ảnh hưởng số điểm. Không có cuboid ở 60–80 m để so với mốc 19 điểm.

Sau lần tự kiểm đầu có 4 cảnh báo, em chỉ chỉnh kích thước trên CVAT, không sửa vị trí, yaw hay class. Hai trường hợp được ghi lại là Bus `s02f11`, idx 11: chiều rộng từ khoảng 3,29 m còn 2,920 m; Car `s05f38`, idx 5: chiều cao từ khoảng 2,37 m còn 2,270 m. Sau khi chạy lại `sync`, phiếu hiện tại không còn cảnh báo nên không có cảnh báo được giữ nguyên.

Hết cảnh báo chưa chứng minh các hộp đều đúng. Bus `s01f22`, idx 3 vẫn rộng 2,877 m, vượt khoảng xe buýt thường 2,50–2,60 m của bảng 4.3. Sau khi xem lại 3D và ba view, mặt bên khuất chưa đủ rõ để xác định lại chiều rộng. Em giữ nguyên số đo và ghi phần chưa chắc, không thu hộp về ngưỡng chỉ để hết cảnh báo; chiều rộng này chưa được xác nhận là đúng.

**Kiểm chéo: MSSV, hai số đối chiếu và kết quả**

Bạn `2A202603190` đọc hai con số trong báo cáo §5 và đối chiếu trực tiếp với bảng A `D13-2A202603215-bangA.csv` mở trên màn hình máy em:

1. Con số thứ nhất: `length` = 4,464 m của `s05f11` idx 11 (ID CVAT 95) ghi trong báo cáo → bạn tìm dòng `s05f11`, idx `11` trong bảng A, cột `length` = `4.464` → **khớp**.
2. Con số thứ hai: `width` = 1,878 m của `s05f11` idx 12 (ID CVAT 108) ghi trong báo cáo → bạn tìm dòng `s05f11`, idx `12` trong bảng A, cột `width` = `1.878` → **khớp**.

Kết quả: hai con số trong báo cáo khớp bảng A. Ngoài ra, khi so cách vẽ, hai bài còn lệch ở kích thước vật xa, ít điểm. Cả hai đổi sang góc nhìn cao và xa hơn, dùng hướng đường làm gợi ý rồi kết hợp bảng 4.3 để ước lượng phần thiếu. Việc thống nhất cách nhìn không chứng minh kích thước đã khớp nhãn chuẩn.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
**(a) Vì sao nhãn 3D dùng mét và vật thưa điểm cần quy tắc riêng?**

Nhãn 3D dùng mét để mô hình học vị trí và kích thước thật của vật trong không gian, không bị thay đổi theo độ phóng ảnh. Vật thưa điểm cần quy tắc riêng vì phần quan sát được ít, dễ làm sai tâm, kích thước và hướng nếu chỉ ôm các điểm còn lại.

**(b) Cách dùng AI**

Công cụ: ChatGPT/Codex.

Câu hỏi chính: hệ trục VF, cách dùng CVAT, lệnh lab, kiểm tra ảnh, đối chiếu số liệu và trình bày báo cáo.

Cách dùng kết quả: hỗ trợ đọc yêu cầu, chụp và kiểm tra ảnh, tính số từ CSV, soạn và sửa câu từ trong báo cáo; em tự chạy lệnh lab và chỉnh kích thước trên CVAT.

**(c) Điều còn chưa chắc và lỗi phát hiện sau hạn**

Vùng khoanh ở `s05f11` mới là điểm lạ nghi vấn, chưa đủ rõ để xác nhận nhiễu; chiều rộng xe buýt mẫu cũng còn chưa chắc. Hướng Cyclist mẫu đã được đối chiếu thêm với cam03 phóng lớn và được giữ nguyên. Bảng A có một hộp `occluded = 1` là Cyclist `s03f44`, idx 3; 115 hộp còn lại bằng 0. Đáy cần so với mặt đường cục bộ thay vì chỉ dùng mốc −1,7 m; riêng Car `s01f07`, idx 11 có `ground_gap_m` = 0,40 m dù không có cờ, nên độ sát đường của hộp này còn cần xem lại. Chưa có lỗi sau hạn được xác nhận.
