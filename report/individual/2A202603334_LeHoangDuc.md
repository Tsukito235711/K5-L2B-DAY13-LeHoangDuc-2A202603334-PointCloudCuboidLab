# Báo cáo Day 13 — Lê Hoàng Đức, 2A202603334


## 1. Kết luận trước

-Mã sv 2A202603334, task D13-21, 10 frame là pointcloud/s01f18.pcd, pointcloud/s01f54.pcd, pointcloud/s02f39.pcd, pointcloud/s02f54.pcd, pointcloud/s03f00.pcd, pointcloud/s03f20.pcd, pointcloud/s04f15.pcd, pointcloud/s04f41.pcd, pointcloud/s05f03.pcd, pointcloud/s05f45.pcd
hệ điều hành windows, trình duyệt chrome, cvat 2.81.0, tổng 109 cuboid, frame s04f41 và s02f54 ít nhất vì ít xe, 28 cờ, tin phần hướng, class, vật xa, còn yếu phần đáy và kích thước

-cách dùng: chọn label rồi shape, cho vào vật r chỉnh hướng, sau đó chỉnh theo trục z và rồi theo trục y, bấm n để shape mới



## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-<frame>-3d`, `t1-<frame>-top` (ảnh nhìn từ trên xuống, có đánh dấu phía trước, +x, +y).

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f18 |trái dưới, +x về phía dưới phải, +y về phía trên phải | 1.87|tầm 35m | car| car| ngoài đường|
| s01f54|trái trên, +x phía dưới trái, +y phía dưới phải |1.4m |tầm 20m | car|car |xe trên cùng đường đi rất mờ |

 3 điều **chỉ camera** cho biết và 3 điều **chỉ LiDAR** cho biết, lấy từ chính hai frame trên.
chỉ camera biết : làn đường, biển báo, tín hiệu đèn
chỉ lidar: xe bị che bởi rào, khoảng cách xe, kích thước thực xe

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| | | |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.
pointcloud/s01f18.pcd
pointcloud/s01f54.pcd
| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | 1 | car|có |có |rồi |phù hợp |
| (b) xe buýt hoặc xe tải | 7 |bus | có| có| rồi|phù hợp |
| (c) người đi bộ hoặc xe hai bánh |15 |cyclist |có |có |rồi |phù hợp |



## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | frame 2 của tôi, cuboid 16 |cây che |khuyết |nội suy gán |
| 2. Thưa ở xa | frame 2 của tôi,cuboid 21 | ở xa|khuyết nhiều | xem camera gán|
| 3. Phản xạ thấp / ít điểm bất thường | frame 4 của tôi, cuboid 31 |xe trước chắn |ít diểm dù ở khá gần |nội suy |
| 4. Điểm lạ | frame 6 | camera thay nhung lidar khong thay ng di xe dap| khong co point| không gán|

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-<MÃ SỐ>-bangA.csv`; số điểm lấy từ cột `points_in` của `-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r | yaw_deg | Nhận xét |
|---|---|---|---|---|---|---|---|
| s01f18, #1, Car | −0,942 m | 1,464 m | −1,674 m | 6,44 m | 6,51 m | 269,0° | chạm đường (lệch +0,03 m so với −1,7 m) |
| s03f00, #7, Car | −0,968 m | 1,586 m | −1,761 m | 8,11 m | 8,17 m | 271,1° | chạm đường (lệch −0,06 m) |
| s02f54, #3, Car | −0,864 m | 1,615 m | −1,672 m | 20,38 m | 20,40 m | 271,9° | chạm đường (lệch +0,03 m) |

(a) Cuboid gần nhất: s05f03 #4 (Car), dist_m = 3,43 m, r = 3,53 m. Cuboid xa nhất: s05f45 #7 (Car), dist_m = 58,88 m, r = 58,91 m (ngoài 50 m nên không được chấm). Trong phạm vi 50 m, xa nhất là s04f41 #6 (Car), dist_m = 46,86 m, r = 46,87 m.

(b) Người đi bộ s01f54 #12: l = 0,901 m, w = 0,616 m, h = 1,777 m, cả ba chiều nằm trong khoảng của bảng 4.3 (cao 0,9–2,2 m). Xe con s01f18 #6: l = 3,996 m, w = 2,345 m, h = 1,371 m; chỉ width nằm ngoài khoảng 1,4–2,3 m, vượt 0,045 m. Giả thuyết: hộp bao cả gương chiếu hậu hai bên thân xe, nên rộng hơn thân xe; chưa kiểm chứng.

(c) Xe cùng chiều phía trước: s01f54 #5, yaw = 268,9° (gần 270°), cách 10,04 m. Xe ngược chiều: s02f54 #5, yaw = 91,1° (gần 90°), cách 10,17 m.

Mật độ theo khoảng cách (xe con, không bị cờ):

| Cuboid (frame, idx, class) | dist_m | Số điểm (points_in) |
|---|---|---|
| s01f18, #1, Car | 6,44 m | 2124 |
| s03f00, #2, Car | 12,04 m | 843 |
| s04f15, #2, Car | 26,84 m | 146 |
| s05f03, #3, Car | 32,86 m | 156 |

 Số điểm giảm nhanh khi xa dần: từ 6,4 m đến 12,0 m (xa gấp khoảng 1,9 lần) số điểm giảm từ 2124 xuống 843 (ít hơn khoảng 2,5 lần), và đến 26,8 m (xa gấp khoảng 4,2 lần) chỉ còn 146 điểm (ít hơn khoảng 14,5 lần). Tốc độ giảm nhanh hơn tỉ lệ 1/khoảng cách và gần với 1/khoảng cách², phù hợp với giả thuyết chùm tia LiDAR tỏa ra nên càng xa càng ít tia trúng vật. So với đường cong ở slide (xe con, trung vị: 1968 điểm ở 0–10 m, 718 ở 10–20 m, 214 ở 20–30 m, 72 ở 30–40 m), hai hộp gần (2124 và 843) cao hơn trung vị, hộp ở 26,8 m (146) thấp hơn trung vị 214, còn hộp ở 32,9 m (156) cao hơn trung vị 72 gần 2 lần. Chỗ lệch này có thể do kích thước xe khác nhau (s05f03 #3 dài 4,15 m còn s04f15 #2 chỉ dài 3,12 m) và hướng nhìn hoặc bị che khác nhau

đổi kiểm: MSSV người đã kiểm chéo với bạn, hai con số nào được đối chiếu với bảng A, khớp hay lệch (lệch thì đã sửa gì).

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

**(a)** Model phát hiện 3D học từ nhãn để suy ra vị trí, kích thước và hướng thật của vật trong không gian, nên nhãn phải đo bằng mét; pixel đổi theo camera và khoảng cách nên không cho biết vật cách bao xa hay to bao nhiêu. Vật thưa điểm (ở xa hoặc bị che) chỉ có vài điểm LiDAR, dễ bị vẽ theo phỏng đoán, mà nhãn đoán sai sẽ dạy model sai, nên cần quy tắc riêng: chỉ vẽ khi thấy khoảng 15–20% thể tích, còn lại bỏ qua hoặc ghi LOW_VISIBILITY, occluded.

**(b)** Có dùng AI, với sự cho phép của lab coach cho phần báo cáo.
- Công cụ: Claude. Câu hỏi chính: cách tạo `.venv`, ý nghĩa các cờ của `selfcheck` và thứ tự sửa cuboid, cách tính z đáy, dist_m, r.
- Công cụ: Claude. Câu hỏi chính: diễn đạt lại và sắp xếp các mục §5, §6. Tôi đưa cho AI nội dung bảng A và phiếu tự kiểm dưới dạng chữ để tính số; tôi không đưa ảnh chụp màn hình hay ảnh camera.
- Cách dùng kết quả: tôi tự vẽ và tự sửa mọi cuboid trên CVAT, rồi đối chiếu lại các số trong báo cáo với bảng A và phiếu tự kiểm của mình trước khi chép.

**(c)** Điều còn chưa chắc:
- Hướng đầu xe ở một số hộp có yaw đúng bằng 0° (s02f39 #7, s03f20 #4, s05f45 #8, #9 và s03f20 #13): tôi chưa chắc hộp đã được xoay đúng hay còn ở giá trị mặc định.
- Kích thước người đi bộ và xe hai bánh: nhiều hộp có width đúng 1,000 m, rộng hơn thực tế, nên IoU có thể thấp hơn mong đợi.
- Nhóm hộp ở s05f45 (#5, #6, #7) nằm ngoài 50 m và có cờ chìm: tôi chưa chắc mặt đường ở đó dốc hay hộp đặt quá thấp.
- s03f20 #5 và #10 vẫn còn cờ sau khi sửa (kích thước lớn, đáy chìm): tôi chưa chắc hộp bao một vật hay hai vật sát nhau.
