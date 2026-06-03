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
<img width="679" height="905" alt="image" src="https://github.com/user-attachments/assets/393c014c-1c0e-4b0d-af5c-ac6e9c5a92e7" />

11. KÉO FUNCTION ALERT : kéo Function -> đổi tên thành Function Alert ->  Nối cả 2 output của Switch vào Function này-> code -> done
<img width="682" height="842" alt="image" src="https://github.com/user-attachments/assets/a05fcf45-f7bc-4043-b85e-105efea63ec3" />

12. KÉO TELEGRAM SENDER : Kéo: telegram sender Nối: Function Alert với Telegram Sender -> Cấu hình -> done -> deploy
<img width="747" height="905" alt="BCBCBCBCBC" src="https://github.com/user-attachments/assets/79a9bebc-6a21-4c80-a832-e68693aa7751" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/530c3d67-75d6-47f1-8efd-9e51da7fd8ea" />

- test bot
truy cập https://api.telegram.org/bot8861635868:AAGkMTSJ-mnag_f4DutFUNibgmgeUNWZDcw/getMe
<img width="853" height="495" alt="image" src="https://github.com/user-attachments/assets/fa26654a-c456-4b41-846c-35478fadbd56" />



















