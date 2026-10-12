# Hướng dẫn dựng server Minecraft Skyblock qua Tailscale

Tổng hợp từ lần dựng server thực tế (Windows, TLauncher + OptiFine, chơi cùng bạn bè qua Tailscale). Gồm **danh sách nơi tải**, **các bước cụ thể**, lỗi đã gặp và cách sửa, cùng phần chuyển sang Linux.

---

## Mục lục
1. [Cấu hình cuối cùng đã chạy ổn](#1-cấu-hình-cuối-cùng-đã-chạy-ổn)
2. [Danh sách nơi tải](#2-danh-sách-nơi-tải)
3. [Các bước làm chi tiết (Windows)](#3-các-bước-làm-chi-tiết-windows)
4. [Cho bạn bè vào qua Tailscale](#4-cho-bạn-bè-vào-qua-tailscale)
5. [Chung đảo và lệnh trong game](#5-chung-đảo-và-lệnh-trong-game)
6. [Lỗi đã gặp và cách xử lý](#6-lỗi-đã-gặp-và-cách-xử-lý)
7. [Chơi hằng ngày](#7-chơi-hằng-ngày)
8. [Chuyển server sang máy Linux](#8-chuyển-server-sang-máy-linux)
9. [Thêm addon, bảo mật, mẹo](#9-thêm-addon-bảo-mật-mẹo)

---

## 1. Cấu hình cuối cùng đã chạy ổn

| Thành phần | Phiên bản |
|---|---|
| Java | Temurin **21** (LTS) |
| Server | Paper **1.21.11** (build 132) |
| Plugin | BentoBox **3.16.2** |
| Addon | BSkyBlock **1.20.0** |
| Client | TLauncher, OptiFine 1.21.11 |
| Mạng | Tailscale |

> **Lưu ý:** OptiFine chỉ chạy ở phía người chơi (client). Server không cần và không cài được OptiFine.

---

## 2. Danh sách nơi tải

| Cần tải | Nơi tải | Ghi chú |
|---|---|---|
| **Java 21 (Temurin)** | https://adoptium.net | Chọn **Temurin 21 (LTS)**, Windows, loại `.msi` (x64). Không chọn bản 25. |
| **Tailscale** | https://tailscale.com/download | Cài trên máy host và máy của từng bạn bè. |
| **Trang quản lý Tailscale** | https://login.tailscale.com/admin/machines | Xem máy trong tailnet, Users, Share. Chỉ chủ tailnet vào được. |
| **Paper 1.21.11** | https://papermc.io/downloads/paper | Trang mặc định hiện bản mới nhất (26.x). Xem cách lấy 1.21.11 ở mục dưới. |
| Paper (nguồn dự phòng, bên thứ ba) | https://www.doomhosting.com/minecraft-server-download/paper/1.21.11 | Có nút **Download JAR**. Chỉ dùng khi trang chính thức khó tìm. |
| **BentoBox 3.16.2** | https://modrinth.com/plugin/bentobox | Tab **Versions**, lọc Game version 1.21.11 và Platform Paper, tìm bản 3.16.2 (có thể ở trang 2 hoặc 3). |
| BentoBox (nguồn khác) | https://hangar.papermc.io (tìm "BentoBox") | Cũng có tab Versions. |
| **BSkyBlock 1.20.0** | https://download.bentobox.world | Chọn BSkyBlock, bản 1.20.0. Hoặc tìm BSkyBlock trên Modrinth/Hangar. |
| Addon khác (Level, Challenges, Warps...) | https://download.bentobox.world | Tải từng file `.jar` riêng. |
| Tài liệu BentoBox | https://docs.bentobox.world | Cấu hình, lệnh, quyền. |
| **TLauncher** | https://tlauncher.org | Client đã dùng. Chọn bản **OptiFine 1.21.11**. |
| AuthMe Reloaded (tùy chọn) | Tìm "AuthMeReloaded" trên Modrinth hoặc Hangar | Đặt mật khẩu cho người chơi vì server để `online-mode=false`. |

### Cách lấy Paper 1.21.11 trên papermc.io
1. Vào https://papermc.io/downloads/paper. Trang hiện bản mới nhất (ví dụ 26.3), **đừng tải bản này**.
2. Kéo xuống phần **Older builds**, bấm vào link **build explorer**.
3. Trong danh sách **Families** bấm **1.21**.
4. Trong danh sách **Versions** bấm **1.21.11**. Nhãn `UNSUPPORTED` chỉ nghĩa là Paper không còn cập nhật nhánh này, vẫn tải và dùng bình thường.
5. Chọn build có **số cao nhất** (build 132 vào thời điểm làm), bấm tải file `paper-1.21.11-132.jar`.
6. Kiểm tra: tên file có chữ `1.21.11`, dung lượng khoảng **50MB**. Tránh bản `pre` và `rc`.

### Cách tải đúng BentoBox trên Modrinth
1. Vào trang BentoBox, bấm tab **Versions**.
2. Bộ lọc: **Game version: 1.21.11**, **Platform: Paper**.
3. **Đừng tải bản mới nhất** (3.23.x) vì nó đòi Java 25. Tìm bản **3.16.2** trong danh sách (dùng nút chuyển trang) rồi bấm biểu tượng tải xuống.
4. Nếu khi chạy console báo `class file version 69.0`, nghĩa là bản đó cần Java mới hơn, hãy hạ xuống bản thấp hơn.

---

## 3. Các bước làm chi tiết (Windows)

### Bước 1: Cài Java 21
1. Tải file `.msi` Temurin 21 ở https://adoptium.net.
2. Chạy file, bấm Next đến màn hình **Custom Setup**. Bật cả hai mục (bấm vào dấu X đỏ, chọn *Will be installed on local hard drive*):
   - **Modify PATH variable**
   - **Set or override JAVA_HOME variable**
3. Bấm Next, Install, Finish.
4. **Đóng hết cmd cũ**, mở cmd mới (Start, gõ `cmd`) và gõ:
   ```
   java -version
   ```
   Kết quả phải có dòng `openjdk version "21.x"`. Kiểm tra JAVA_HOME bằng `echo %JAVA_HOME%`.

### Bước 2: Tạo thư mục và đặt Paper
1. Tạo thư mục **không dấu, không khoảng trắng**, ví dụ `D:\skyblock-server`.
2. Chép file Paper đã tải vào đó và **đổi tên thành `server.jar`** (cẩn thận, đừng gõ nhầm thành `sever.jar`).
3. Bật hiện đuôi file: File Explorer, **View, Show, File name extensions**. Kiểm tra file không bị thành `server.jar.jar`.

### Bước 3: Tạo `start.bat`
1. Trong thư mục server, bấm chuột phải, **New, Text Document**.
2. Mở bằng Notepad, dán:
   ```bat
   @echo off
   java -Xmx4G -Xms4G -jar server.jar nogui
   pause
   ```
   Máy có 8GB RAM trở xuống thì đổi `4G` thành `2G`.
3. Chọn **File, Save As**, ở mục *Save as type* chọn **All files**, đặt tên `start.bat`, Save.
4. Xóa file `New Text Document.txt` cũ.

### Bước 4: Chạy lần đầu và đồng ý EULA
1. Bấm đúp `start.bat`. Lần đầu nó tải `mojang_1.21.11.jar` rồi áp bản vá, mất vài chục giây (cần có mạng, đừng đóng cửa sổ).
2. Server dừng và báo về EULA. Nhấn phím bất kỳ để đóng.
3. Mở `eula.txt`, đổi `eula=false` thành `eula=true`, lưu lại.
4. Bấm đúp `start.bat` lần nữa. Đợi đến dòng có chữ **Done** và `For help, type "help"`.
5. Gõ `stop` trong cửa sổ đen và Enter để tắt.

### Bước 5: Cho TLauncher vào được (tắt xác thực online)
1. Mở `server.properties` bằng Notepad.
2. Đổi `online-mode=true` thành `online-mode=false`, lưu lại.
3. Bật whitelist (làm trong console khi server đang chạy, không có dấu `/`):
   ```
   whitelist on
   whitelist add <tên nhân vật>
   op <tên nhân vật>
   ```
   Tên phải **trùng từng ký tự** với tên trong launcher.

### Bước 6: Cài BentoBox
1. Tải `BentoBox-3.16.2.jar` (mục 2) và chép vào `D:\skyblock-server\plugins`.
2. Bấm `start.bat`, đợi đến **Done**, rồi gõ `stop`. Lúc này thư mục `plugins\BentoBox` và `plugins\BentoBox\addons` được tạo.
3. Kiểm tra console có dòng `Enabling BentoBox v3.16.2` và không có `ERROR`.

### Bước 7: Cài BSkyBlock
1. Tải `BSkyBlock-1.20.0.jar` (mục 2).
2. Nếu tải bằng gói ZIP từ download.bentobox.world: **chỉ lấy file addon `.jar`**. Không chép file `BentoBox-3.23.x.jar` hay cả thư mục `BentoBox` trong ZIP vào server.
3. Chép file vào `D:\skyblock-server\plugins\BentoBox\addons`.
4. Bấm `start.bat`. Lần đầu sẽ tạo thế giới skyblock nên lâu hơn.
5. Kiểm tra console: **`Loaded 1 addons`**, `Enabling BSkyBlock (1.20.0)`, `Added world BSkyBlock`, không có `ERROR`.

### Bước 8: Vào game thử
1. Giữ cửa sổ server mở. Mở TLauncher, chọn **OptiFine 1.21.11**.
2. Multiplayer, **Add Server**, nhập `localhost` (máy host) rồi Join.
3. Trong game nhấn **T** và gõ `/island` để tạo đảo.

---

## 4. Cho bạn bè vào qua Tailscale

### Phía host (máy chạy server)
1. Cài Tailscale (https://tailscale.com/download) và đăng nhập. Máy có IP dạng `100.x.y.z`.
2. Lấy IP: gõ `tailscale ip -4` trong cmd, hoặc bấm biểu tượng Tailscale ở taskbar rồi bấm vào tên máy (tự copy).
3. Trong console server: `whitelist add <tên bạn>` (đúng tên nhân vật trong launcher).
4. Gửi cho bạn: `100.x.y.z:25565`.

### Phía bạn bè
1. Cài Tailscale (https://tailscale.com/download).
2. Phải đăng nhập **vào đúng tailnet của host**. Có hai cách host cho bạn vào:
   - **Mời user:** https://login.tailscale.com/admin/users, bấm **Invite**, nhập email bạn.
   - **Share máy:** trang Machines, bấm `...` ở máy server, chọn **Share...**, gửi link cho bạn. Bạn chỉ thấy máy server.
3. Kiểm tra mạng đã thông: mở cmd gõ `tailscale ping 100.x.y.z`. Phải ra `pong from ...`.
4. Mở Minecraft **đúng 1.21.11**, Multiplayer, **Add Server**, nhập `100.x.y.z:25565`.

### Kiểm tra bạn đã vào đúng tailnet chưa
- Trên trang Machines của host phải thấy máy của bạn bè (số máy tăng lên).
- Bạn bè gõ `tailscale switch --list` và `tailscale status`. Phải thấy tên tailnet của host và máy server.

### Nếu có `pong` nhưng vẫn timeout: mở tường lửa Windows
Mở cmd bằng quyền **Administrator** trên máy host và chạy:
```
netsh advfirewall firewall add rule name="Minecraft 25565" dir=in action=allow protocol=TCP localport=25565 remoteip=100.64.0.0/10
```
Lệnh này chỉ cho phép cổng 25565 từ dải Tailscale, không mở ra internet.

---

## 5. Chung đảo và lệnh trong game

Mặc định BSkyBlock tạo **mỗi người một đảo riêng**. Muốn chung đảo (cả hai đang online):
```
/is team invite <tên bạn>      (người chủ đảo gõ)
/is team accept                (người được mời gõ)
```
Người được mời nếu đã có đảo riêng thì đảo cũ sẽ bị bỏ, nên lấy hết đồ trước.

### Phân biệt hai nơi gõ lệnh

| Nơi gõ | Cách gõ | Ví dụ |
|---|---|---|
| **Trong game** (nhấn T) | Có dấu `/` | `/island`, `/is sethome`, `/is team invite <tên>` |
| **Console server** (cửa sổ đen) | **Không** có dấu `/` | `whitelist add <tên>`, `op <tên>`, `stop` |

### Lệnh hữu ích
**Trong game**
- `/is`: về đảo (tạo đảo nếu chưa có)
- `/is sethome`: đặt điểm nhà
- `/is team invite <tên>` và `/is team accept`: mời và chấp nhận vào đội
- `/is team promote <tên>`: nâng quyền thành viên
- `/is team leave`: rời đội
- `/is reset`: làm lại đảo

**Console server**
- `whitelist add <tên>`, `whitelist remove <tên>`, `whitelist list`
- `op <tên>`, `deop <tên>`
- `stop`: tắt server

---

## 6. Lỗi đã gặp và cách xử lý

### Chuẩn bị
| Lỗi | Nguyên nhân và cách sửa |
|---|---|
| `java` is not recognized | Chưa cài Java hoặc chưa bật Modify PATH. Cài lại, nhớ mở cmd mới sau khi cài. |
| Lỡ cài Java 25 | Vẫn chạy được Paper, nhưng nên dùng Java 21 để ít lỗi plugin. Cài Temurin 21 lại và kiểm tra `java -version`. |

### Tải và chạy Paper
| Lỗi | Nguyên nhân và cách sửa |
|---|---|
| Không thấy bản 1.21.11 | Minecraft đã chuyển sang đánh số 26.x nên trang mặc định hiện bản mới nhất. Dùng **build explorer** (mục 2). |
| `Unable to access jarfile server.jar` | Tên file sai (`sever.jar`, `server.jar.jar`) hoặc `start.bat` không cùng thư mục với `server.jar`. |
| Treo ở `Downloading mojang_1.21.11.jar` | Bình thường ở lần chạy đầu, chờ vài phút và cần có mạng. Đừng đóng cửa sổ. |

### Plugin
| Lỗi | Nguyên nhân và cách sửa |
|---|---|
| BentoBox 3.23.x báo `class file version 69.0 ... up to 65.0` | Bản đó cần **Java 25**. Với Java 21 hãy dùng bản cũ hơn (đã dùng 3.16.2). Modrinth ghi "hỗ trợ 1.21.11" nhưng không nói rõ yêu cầu Java. |
| BSkyBlock mới đòi BentoBox cao | BSkyBlock 1.20.0 cần BentoBox từ 3.13.0 trở lên. 3.16.2 đáp ứng được. Luôn đọc mô tả từng bản trước khi tải. |
| Gói ZIP từ download.bentobox.world | ZIP chứa cả `BentoBox-3.23.x.jar` (không dùng được với Java 21). Chỉ lấy các file addon cần thiết, thêm **từng cái một** và chạy thử sau mỗi lần. |

### Vào game
| Lỗi | Nguyên nhân và cách sửa |
|---|---|
| `You are not whitelisted on this server!` | Tên whitelist phải **trùng từng ký tự** (hoa thường, số) với tên trong launcher. Từng whitelist `TranNgocAnh` trong khi tên thật là `TranNgocAnh2003`. Xem tên đúng trong dòng log `Disconnecting <tên> ...`. |
| Sai phiên bản / incompatible | Client phải là **1.21.11**, không phải 26.x. |
| Treo ở Joining world hoặc văng game | Thử bản 1.21.11 thường (không OptiFine) để loại trừ OptiFine. |

### Tailscale (rắc rối lớn nhất)
| Triệu chứng | Nguyên nhân và cách sửa |
|---|---|
| Bạn bè bị `Connection timed out`, `tailscale ping` báo `no matching peer` | Tài khoản bạn đã được thêm làm Member, nhưng app Tailscale trên máy họ vẫn đăng nhập vào **tailnet riêng của họ**. |
| Dấu hiệu nhận biết | Trang Machines của host chỉ có 1 máy, và `tailscale switch --list` bên bạn bè chỉ có 1 dòng. |
| Cách sửa đã dùng được | Bạn bè bấm biểu tượng Tailscale, bấm vào dòng tài khoản, chọn **Add another account...** và đăng nhập lại. Trang báo `logged in to the <tailnet host>` là thành công. |
| Bấm link mời hoặc vào `/admin` báo "You don't have access to the admin console" | Bình thường với Member, chỉ cần bỏ qua. Không cần vào admin console. |
| Còn timeout dù đã có `pong` | Mở cổng tường lửa (lệnh `netsh` ở mục 4). |

---

## 7. Chơi hằng ngày

**Mỗi lần chơi**
1. Mở thư mục server, bấm đúp `start.bat`.
2. Đợi dòng **Done** (khoảng 15 đến 30 giây).
3. Bật Tailscale trên máy host (bạn bè cũng bật của họ).
4. Vào Minecraft 1.21.11: máy host dùng `localhost`, bạn bè dùng `100.x.y.z:25565`.

**Khi chơi xong**
- Gõ `stop` trong cửa sổ đen, đợi cửa sổ tự dừng rồi mới đóng. **Đừng bấm dấu X**.

**Lưu ý**
- Cửa sổ đen phải mở suốt lúc chơi. Đóng là server tắt và mọi người bị văng.
- Máy host phải bật, tắt chế độ Sleep của Windows khi đang host.
- Mẹo: chuột phải `start.bat`, **Send to, Desktop (create shortcut)** để mở nhanh.

---

## 8. Chuyển server sang máy Linux

Paper và BentoBox chạy giống hệt Windows. Các lệnh dưới đây dùng cho Ubuntu/Debian.

### Bước 1: Chép dữ liệu
1. Trên Windows gõ `stop`, đợi cửa sổ dừng hẳn.
2. Nén cả thư mục `D:\skyblock-server` thành file `.zip`.
3. Chép sang máy Linux (USB, `scp` hoặc Google Drive).
4. Giải nén:
   ```bash
   sudo apt install -y unzip
   unzip skyblock-server.zip -d ~/skyblock-server
   ```
   Chép nguyên thư mục là giữ được thế giới, đảo, whitelist, danh sách OP và cấu hình. Không cần tải lại gì.

### Bước 2: Cài Java 21
```bash
sudo apt update
sudo apt install -y openjdk-21-jre-headless screen
java -version
```
Phải ra `21.x`. Nếu bản Ubuntu cũ không có gói `openjdk-21`, tải Temurin 21 ở https://adoptium.net (mục Linux).

### Bước 3: Cài Tailscale và vào đúng tailnet
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```
Mở link in ra bằng trình duyệt và đăng nhập **đúng tài khoản chủ tailnet**. Lấy IP mới:
```bash
tailscale ip -4
```
IP này **khác** IP máy cũ, nên gửi IP mới cho bạn bè (hoặc dùng tên MagicDNS).

### Bước 4: Tạo `start.sh`
```bash
cd ~/skyblock-server
nano start.sh
```
Nội dung:
```bash
#!/bin/bash
java -Xmx4G -Xms4G -jar server.jar nogui
```
Lưu (Ctrl+O, Enter, Ctrl+X), rồi:
```bash
chmod +x start.sh
```
Chỉnh `4G` theo RAM máy (chừa ít nhất 1GB cho hệ điều hành).

### Bước 5: Chạy server
Dùng `screen` để server không tắt khi đóng terminal hoặc ngắt SSH:
```bash
screen -S mc
./start.sh
```
- Rời mà vẫn để server chạy: **Ctrl+A rồi D**.
- Quay lại console: `screen -r mc`.
- Tắt server: gõ `stop` trong console.

### Bước 6: Tường lửa (nếu bật UFW)
Chỉ mở cổng cho mạng Tailscale:
```bash
sudo ufw allow in on tailscale0 to any port 25565 proto tcp
```

### Bước 7: Kiểm tra
- Console thấy `Done`, BentoBox `Loaded 1 addons`, không có `ERROR`.
- Từ máy bạn bè: `tailscale ping <IP mới>` ra `pong`, rồi vào Minecraft nhập `<IP mới>:25565`.

### Lưu ý khi chuyển
- Linux **phân biệt hoa thường** (`plugins`, `BentoBox`). Chép nguyên cấu trúc cũ thì không lỗi.
- Giữ `online-mode=false` như cũ. Đảo gắn với UUID sinh từ tên nên người chơi giữ nguyên đảo, miễn là tên không đổi.
- Java vẫn phải là 21 để hợp với BentoBox 3.16.2.
- Sau khi server mới chạy ổn, xóa máy cũ khỏi trang Machines của Tailscale nếu không dùng nữa, và giữ lại bản sao lưu thư mục cũ một thời gian.

---

## 9. Thêm addon, bảo mật, mẹo

### Thêm addon sau này
1. `stop` server.
2. Chép **từng** file addon `.jar` vào `plugins\BentoBox\addons`.
3. Chạy lại, kiểm tra console. Cái nào báo `ERROR` thì xóa file đó và chọn bản cũ hơn.

Nên cân nhắc: **Level**, **Challenges**, **Warps**, **Limits**. Tùy chọn: Bank, Biomes, Border, ControlPanel. Tải ở https://download.bentobox.world.

### Bảo mật khi để `online-mode=false`
- Ai biết tên người chơi đều có thể vào và đóng giả (kể cả OP). Chỉ nên truy cập qua Tailscale và **không mở cổng 25565 ra internet**.
- Luôn bật whitelist.
- Muốn có mật khẩu cho từng người, cài thêm plugin **AuthMe Reloaded**.
- Bạn bè là Member trong tailnet có thể thấy các máy khác trong tailnet. Giới hạn bằng **Access controls** trên trang admin Tailscale, hoặc dùng **Share máy** thay vì mời user.

### Mẹo để lần sau nhanh hơn
- **Kiểm tra trước khi tải:** xem yêu cầu Java và phiên bản Minecraft/BentoBox API trong mô tả từng bản plugin.
- **Thêm từng thứ một**, chạy thử và đọc console sau mỗi lần.
- **Đọc console khi có lỗi:** hầu hết lỗi chỉ rõ nguyên nhân trong vài dòng đầu của `ERROR`.
- **Làm Tailscale trước khi gọi bạn vào:** kiểm tra `tailscale ping` ra `pong` trước khi thử vào game.
- **Không nâng cấp bừa** Paper, BentoBox hay Java khi mọi thứ đang chạy ổn.
- **Đừng đổi tên nhân vật**: UUID sinh từ tên nên đổi tên là mất đảo và dữ liệu.
- **Sao lưu:** copy cả thư mục server ra chỗ khác khi đã `stop`.
- **Tắt server bằng `stop`**, không bấm dấu X.
