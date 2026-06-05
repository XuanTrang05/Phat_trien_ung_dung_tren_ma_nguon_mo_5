# Phat_trien_ung_dung_tren_ma_nguon_mo_5
bt5:
  - lý thuyết: 
    + docker là gì? 
    + các keyword được sử dụng trong docker-compose.yml
      để mô tả 1 service, network, volume,...
      liệt kê + ý nghĩa của từ khoá đó + ví dụ minh hoạ
    + ưu điểm khi triển app sử dụng docker là gì?
    + dùng docker: tạo app, test app OK trên laptop cá nhân
      giờ muốn triển khai app này trên máy chủ thật ko có internet
      thì các bước cần làm là?
  - thực hành áp dụng: APP MONITOR + ALERT DATA REALTIME
    sử dụng docker compose có nhiều serivce 
    và các thành phần cần thiết để tạo thành ứng dụng:
     + nodered liên tục lấy dữ liệu từ nguồn nào đó (chứng khoán, thời tiết, giá vàng,...)
       nguồn thực tế, số liệu luôn động sau thời gian ngắn
     + nodered lưu trữ dữ liệu vào 2 database: mariadb để lưu giá trị tức thời
       lưu lịch sử vào influxdb
     + sử dụng grafana để trực quan hoá dữ liệu: vẽ biểu đồ
     + sử dụng nginx để làm webserver
       chạy 1 trang web html+js+css làm front-end
       js: lấy dữ liệu tức thời trong mariadb qua (ajax | socket) 
           gọi api (api tự build bằng Flask giống bt1)
           api trả về giá trị tức thời trong mariadb
           hiển thị lên web, auto hiển thị số mới khi thay đổi
       sử dụng iframe để gọi grafana
       hiển thị biểu đồ dữ liệu lịch sử của thông số đã lưu
     + QUAN SÁT DỮ LIỆU LỊCH SỬ => GIÁ TRỊ BẤT THƯỜNG
       (VD MIỀN A..B: OK, DƯỚI A: ALERT LOW, TRÊN B: ALERT HIGH)
     + nodered: kết hợp bot Telegram
       khi dữ liệu not OK, thì gửi tin nhắn từ bot => group trên telegram
       group đã add bot vào: (nhóm đã có 2 người), add thêm 1875746636 thành 3 người
       mỗi khi bot gửi dữ liệu vào nhóm: mọi member of group đều nhận đc
       nội dung alert: tường minh, có value gây alert

     xuất tất cả các container ra file nén.
     xoá mọi container đang chạy
     load lại các container  từ file nén để khôi phục các container đã xoá
=========
quá trình làm: chụp ảnh lại, mô tả cho ảnh
  lưu vào trong github => paste link access public của repo: vào file excel online

## BÀI LÀM
# LÝ THUYẾT
# THỰC HÀNH ÁP DỤNG : APP MONITOR + ALERT DATA REALTIME
- Tạo thư mục monitor: 
mkdir ~/monitor
cd ~/monitor
<img width="746" height="317" alt="image" src="https://github.com/user-attachments/assets/a8e8b525-8925-40f2-a1a6-1835dd3a36c6" />

- Tạo file docker-compose.yml

nano docker-compose.yml
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/bc51c8f2-67f5-4408-b5b5-1570711506de" />

- Kiểm tra file: 
cat docker-compose.yml
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b003b906-aab9-459c-bb04-d7fb1dae911c" />

- Khởi động các container
docker compose up -d
<img width="810" height="159" alt="image" src="https://github.com/user-attachments/assets/2a698294-dd3b-4493-adc3-f3bdb3d4aa19" />

- Kiểm tra
docker ps
<img width="1660" height="723" alt="image" src="https://github.com/user-attachments/assets/1f139d43-1e68-48d7-a34d-e2f3d72f8c1a" />

- Kiểm tra Node-RED
  > - Mở trình duyệt:http://192.168.18.128:1880
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1ec9d324-8454-468d-874c-d77bbfb7817c" />

- Kiểm tra Grafana

Mở: http://192.168.18.128:3000
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c212258c-fc2f-452b-949e-c11ab257d976" />
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8173d8d9-c569-4ea0-9f5f-7441afd58e89" />

- Kiểm tra InfluxDB

Mở: http://192.168.18.128:8086

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/bbe5e335-cab0-4788-9ddc-378063457670" />

- Tạo bảng dữ liệu trong MariaDB
- Vào container MariaDB
Chạy trong Terminal Ubuntu: docker exec -it mariadb_bt5 bash . sau đó nhập passwword ( password ghi trong docker-compose.yml

<img width="687" height="331" alt="image" src="https://github.com/user-attachments/assets/d4e4d997-d66e-449a-9594-fd77e56a8803" />

- sau khi vào thành công, chạy : SHOW DATABASES
- sẽ thấy có database: monitordb
<img width="682" height="603" alt="image" src="https://github.com/user-attachments/assets/1ca39a05-7611-4b25-88c3-28daa07755ca" />

- Chọn database: USE monitordb;

<img width="359" height="95" alt="image" src="https://github.com/user-attachments/assets/272512bc-a921-46cb-b4d7-df1731cd01ae" />

- Tạo bảng realtime_data
<img width="358" height="147" alt="image" src="https://github.com/user-attachments/assets/111a43ce-294a-4d55-a631-cc39b0bf1a41" />

<img width="600" height="249" alt="image" src="https://github.com/user-attachments/assets/7ec02982-13a4-48af-8c78-75d1175dfb4b" />

- Kiểm tra bảng: SHOW TABLES;

<img width="366" height="190" alt="image" src="https://github.com/user-attachments/assets/a0a5cefe-af29-4c93-8328-c4fe7a030f21" />

- Thêm dữ liệu thử: 
INSERT INTO realtime_data(value, update_time)
VALUES (30.5, NOW());
- Kiểm tra dữ liệu: 
SELECT * FROM realtime_data;
<img width="678" height="313" alt="image" src="https://github.com/user-attachments/assets/f035ab99-d6cf-4901-b6bf-bc8b3afc4bfe" />

# Tạo Flask API đọc dữ liệu từ MariaDB
- Mục tiêu:
Khi truy cập: http://IP-Ubuntu:5000/api/data
sẽ nhận được:

{
  "id": 1,
  "value": 30.5,
  "update_time": "2026-06-03 15:06:37"
}
--------------------------------
- Thoát MariaDB
Đang ở: MariaDB [monitordb]>
gõ: exit;
<img width="737" height="873" alt="image" src="https://github.com/user-attachments/assets/4afe0f65-1d45-482e-88b8-a1e7551d5a39" />

Tạo thư mục Flask API
- Trong thư mục monitor:
mkdir flask-api
cd flask-api
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ec14caf4-3da4-4eed-b3ff-0e2101b9bf20" />

- Tạo file app.py: 
nano app.py
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/09949a5b-77fb-4c16-a611-7ec80f425576" />

- Tạo requirements.txt: 
nano requirements.txt
<img width="737" height="480" alt="image" src="https://github.com/user-attachments/assets/9d122a44-e04b-4650-9719-6da0ca4e54ef" />

- Tạo Dockerfile: 
nano Dockerfile
<img width="749" height="897" alt="image" src="https://github.com/user-attachments/assets/2468fbf6-42fc-458b-883f-e4a9ad55a361" />

- Quay về thư mục monitor:
cd ..
- Mở docker-compose.yml: 
nano docker-compose.yml
Thêm service Flask vào dưới phần Node-RED:
<img width="757" height="918" alt="image" src="https://github.com/user-attachments/assets/dcb9958e-40da-4ffe-b628-5efd54363d90" />

- Build lại
  > - Trong thư mục monitor:
docker compose up -d --build

- Kiểm tra Flask: 
docker ps
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/92e71ed9-e1a4-4694-8dbc-d1c56317d6a2" />

- Kiểm tra API
Chạy: curl localhost:5000/api/data
<img width="694" height="92" alt="image" src="https://github.com/user-attachments/assets/a461c4dc-8fca-41c9-91c1-3e5223005151" />

# Node-RED tự động lấy dữ liệu thời tiết
- Mục tiêu:
Open-Meteo
    ↓
Node-RED
    ↓
MariaDB

- Mỗi 1 phút:
  > - lấy nhiệt độ Hà Nội
  > - ghi vào bảng realtime_data

- Cài các node cần thiết cho bài
  > - Góc trên bên phải: ☰ -> Manage palette -> Install
- lần lượt cài:
  > - MySQL: node-red-node-mysql
  > - InfluxDB: node-red-contrib-influxdb
  > - Telegram: node-red-contrib-telegrambot

- sau khi cài xong khởi động lại:  docker restart nodered_bt5

SƠ ĐỒ FLOW Bài Tập 5 (PHẦN NODE-RED)
Inject
   ↓
HTTP Request
   ↓
Function GetTemp
   ├───────────→ Function SQL → MySQL
   │
   ├───────────→ Function Influx → InfluxDB Out
   │
   └───────────→ Switch
                     ↓
                Function Alert
                     ↓
              Telegram Sender

1. KÉO NODE INJECT -> Double click -> Repeat -> interval -> Every 60 -> Seconds seconds -> Done
<img width="690" height="848" alt="image" src="https://github.com/user-attachments/assets/c37d2345-8aa9-402d-903d-bcc9e3277000" />

2. KÉO NODE HTTP REQUEST -> Nối: Inject với HTTP Request -> Double click -> Method: GET -> URL: https://api.open-meteo.com/v1/forecast?latitude=21.0285&longitude=105.8542&current=temperature_2m -> Return: a parsed JSON object -> Done
<img width="758" height="908" alt="image" src="https://github.com/user-attachments/assets/d4f58a50-03fa-4daa-8ebf-50aeb4cfb209" />

3. KÉO FUNCTION GetTemp: Kéo: Function -> nối HTTP Request với Function -> Double click -> Đổi tên: GetTemp -> code -> done
<img width="762" height="940" alt="image" src="https://github.com/user-attachments/assets/012da1be-407d-4995-91c3-90f816239669" />

4. KÉO DEBUG -> nối GetTemp với Debug -> deploy -> Nhấn nút Inject -> Debug phải hiện nhiệt độ
<img width="1009" height="358" alt="image" src="https://github.com/user-attachments/assets/c5cc29ff-fafa-4388-827d-9147b3691d6e" />

5. KÉO FUNCTION SQL : Kéo: Function -> nối GetTemp với Function SQL -> Đổi tên: SQL Insert -> Code -> Done.
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/82a411e8-c12a-4308-8c7d-d84c7d8913c8" />

6. KÉO MYSQL -> Nối: SQL Insert với MySQL -> cấu hình MySQL -> done
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/af36c547-102d-4363-867d-54beae9eb34f" />

7. TEST GHI DATABASE
Deploy -> Nhấn Inject -> Kiểm tra MariaDB:

docker exec -it mariadb_bt5 mariadb -u root -p
sau đó nhập password, rồi nhập :
USE monitordb;
SELECT * FROM realtime_data;
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/26ebdbbe-0272-4542-971d-04458cc152b2" />
=> Node-RED đã ghi được MariaDB.
8. KÉO FUNCTION INFLUX Kéo:Function-> đổi tên thành Function Influx-> Nối GetTemp với Function Influx -> code -> done
<img width="685" height="886" alt="image" src="https://github.com/user-attachments/assets/3d311482-c36b-46af-a105-f6a5e85521f3" />

9. KÉO INFLUXDB OUT -> Nối Function Influx với InfluxDB Out -> Cấu hình InfluxDB
Mở InfluxDB: Mở trình duyệt: http://192.168.18.128:8086 Kiểm tra đã setup InfluxDB chưa
Nếu lần đầu mở sẽ có:
Get Started
phải tạo: Username,Password,Organization,Bucket
<img width="758" height="970" alt="image" src="https://github.com/user-attachments/assets/4ff62072-6d82-4f0c-a3af-e9d634d1ab7d" />
 Sau đó ấn continue sẽ hiện ra token -> ấn coppy token -> sau đó nhấn CONFIGURE LATER
<img width="753" height="940" alt="AAAAAAAA" src="https://github.com/user-attachments/assets/6ac89462-70cb-49be-8a84-cbba27ff351f" />

 sau đó quay lại Node-RED trong cửa sổ cấu hình InfluxDB:
<img width="693" height="864" alt="image" src="https://github.com/user-attachments/assets/a3b68924-8f19-4332-86c6-25f2dea1f2d9" />
Sau khi bấm Add, node InfluxDB Out thường sẽ hiện thêm: điền: 
<img width="685" height="861" alt="image" src="https://github.com/user-attachments/assets/5d4b3fd4-3645-4f84-9414-f749c739ebe5" />

- Test -> Deploy -> Bấm Inject -> Xem node influxdb out có báo lỗi đỏ không nếu không báo lỗi đỏ là đã ok

<img width="1141" height="594" alt="image" src="https://github.com/user-attachments/assets/4cb43adf-562a-4a27-8324-a65042ad8698" />

10. KÉO SWITCH ->  Nối: GetTemp với Switch -> cấu hình
<img width="756" height="904" alt="image" src="https://github.com/user-attachments/assets/601a5ab0-6cce-49c3-88a5-499daf67f286" />

11. KÉO FUNCTION ALERT : kéo Function -> đổi tên thành Function Alert ->  Nối cả 2 output của Switch vào Function này-> code -> done
<img width="695" height="913" alt="nudjidvn" src="https://github.com/user-attachments/assets/c33cb447-f75b-41d5-9921-fa7aed9fd296" />

12. KÉO TELEGRAM SENDER : Kéo: telegram sender Nối: Function Alert với Telegram Sender -> Cấu hình -> done -> deploy
  > - ( để lấy id nhóm chat ta truy cập vào telegram -> tìm @userinfobot -> nhấn start -> ấn group/ hoặc my group sau đó tìm nhóm chat cần lấy id )
<img width="758" height="900" alt="vdhjbeh" src="https://github.com/user-attachments/assets/d35b61cc-5b0d-4abb-b9bf-567c37214632" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/530c3d67-75d6-47f1-8efd-9e51da7fd8ea" />

- test bot telegrem xem có hoạt động hay không
Vì nhiệt độ đã cấu hình ở Switch hiện tại đang ở mức bình thường (từ 20-35°C), nên cả 2 điều kiện đều không thỏa:
< 20 → nhiệt độ không dưới 20 → không gửi
> 35 → nhiệt độ không trên 35 → không gửi
Đây là hoạt động ĐÚNG! Switch chỉ gửi cảnh báo khi nhiệt độ thực sự bất thường.
Để test thử có thể đổi tạm thành > 20 rồi inject → sẽ gửi tin. Sau đó đổi lại > 35 như cũ là xong.
<img width="685" height="762" alt="image" src="https://github.com/user-attachments/assets/5be7f6e4-6a0a-4ad6-a0c6-7e237eba7df3" />
- khi đó bot sẽ gửi tin nhắn cảnh báo nhiệt độ cao -> Hệ thống của bạn hoạt động hoàn toàn bình thường!
<img width="1179" height="2556" alt="image" src="https://github.com/user-attachments/assets/ffa36119-44e5-488e-8b11-e04bac592dab" />

# KẾT QUẢ SAU KHI LÀM XONG PHẦN NODE-RED
- Ta sẽ có:
> -  ✓ Open-Meteo lấy dữ liệu thật
> -  ✓ Node-RED lấy nhiệt độ mỗi 60 giây
> -  ✓ Ghi MariaDB
> -  ✓ Chuẩn bị ghi InfluxDB
> -  ✓ Kiểm tra ngưỡng
> -  ✓ Gửi Telegram khi vượt ngưỡng

#  Grafana — vẽ biểu đồ từ InfluxDB
- Vào trình duyệt: http://192.168.18.128:3000
- Thêm Data Source InfluxDB
  > - Menu trái → Connections → Data sources
  > - Nhấn Add data source
  > - Chọn InfluxDB

<img width="954" height="974" alt="image" src="https://github.com/user-attachments/assets/28b5c14d-ab58-49d1-8b73-ac7858d05a63" />
Điền vào Grafana
Sau khi nhấn vào InfluxDB → Add new data source, điền:
Query Language: Flux
URL: http://influxdb:8086
<img width="954" height="974" alt="image" src="https://github.com/user-attachments/assets/7f4edbd3-034f-425d-bccf-7a114056b9b3" />
- Data source đã kết nối thành công!
Bây giờ tạo Dashboard:
Menu trái → Dashboards → New → New dashboard → Nhấn Add visualization → Chọn data source InfluxDB vừa tạo

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a07fd3aa-67f5-41ba-b3be-af7929c432a2" />




<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/cd4d6ea3-d768-4047-a040-ccec74d73653" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f57cc755-2117-42b2-a20e-d989c4ae0a45" />

# Tạo thư mục frontend
- Tạo thư mục: mkdir -p nginx/html

<img width="692" height="132" alt="image" src="https://github.com/user-attachments/assets/76cf6920-db96-498d-b8f9-d6ae91efdaa8" />

- Tạo file index.html: nano ~/bt5-monitor/nginx/html/index.html


<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/213e6a0d-ba41-4d56-a159-716ef95d3c3a" />

- Tạo Nginx service
Mở: nano ~/bt5-monitor/docker-compose.yml
<img width="764" height="939" alt="image" src="https://github.com/user-attachments/assets/f5f83246-b544-4149-977b-805c1a204f1f" />

- Khởi động lại
cd ~/bt5-monitor
docker compose up -d
- sau đó kiểm tra xem đã chạy thành công chưa
<img width="610" height="276" alt="image" src="https://github.com/user-attachments/assets/9070501a-1c8b-414a-82d6-5aa10b58c603" />

# Nhúng Grafana

- Mở file index.html: nano ~/bt5-monitor/nginx/html/index.html -> thêm iframe Grafana
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/73140903-c80f-4d31-9d30-b77757446c40" />

- kiểm tra xem đã hiện biểu đồ chưa: http://192.168.18.128:8088/
<img width="957" height="1016" alt="image" src="https://github.com/user-attachments/assets/e17e11b6-7081-48a4-9cf0-df9a72145a42" />

# Export/Import Docker
- xem các image đang dùng : docker images
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d82fcc6a-77a4-4d6c-a27d-f5f69a7b641b" />

- Tạo thư mục backup: mkdir -p ~/bt5-backup
<img width="825" height="92" alt="image" src="https://github.com/user-attachments/assets/89d7f5bd-f2a7-4c34-b08b-12386ded9d22" />

- EXPORT TOÀN BỘ IMAGE
<img width="741" height="179" alt="image" src="https://github.com/user-attachments/assets/bc7ae3fe-0408-4195-8ef9-01f3b7928cd8" />

- KIỂM TRA FILE ĐÃ TẠO: ls -lh ~/bt5-backup
<img width="815" height="73" alt="image" src="https://github.com/user-attachments/assets/10aa3dd1-b299-428d-ac88-962693bb3189" />

- NÉN FILE: gzip ~/bt5-backup/bt5-images.tar
<img width="915" height="89" alt="image" src="https://github.com/user-attachments/assets/b38d9a65-2b3a-426d-9133-b8d6d37cbc31" />

- DỪNG HỆ THỐNG
Trong thư mục project:
cd ~/bt5-monitor
docker compose down
Kiểm tra: docker ps
<img width="1658" height="600" alt="image" src="https://github.com/user-attachments/assets/65631622-b845-4238-b7e0-f43cfbbac3a2" />

- XÓA IMAGE
  <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/dd19ecae-8e4d-44d9-a9c1-f1f2a747696b" />

-  Load lại từ file nén
  <img width="1063" height="180" alt="image" src="https://github.com/user-attachments/assets/9f31a4a2-c70e-4611-992c-e3ef05c29a59" />
- khởi động lại các containers: docker compose -f ~/bt5-monitor/docker-compose.yml up -d
<img width="1167" height="228" alt="image" src="https://github.com/user-attachments/assets/aacf5d02-7e95-45fd-83a1-cd8502bf4adb" />
- kiểm tra lại
- <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/376c8a28-6da2-4117-a01b-6b6e0e95f4f8" />








