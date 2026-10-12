# Hướng dẫn dựng server Minecraft Skyblock qua Tailscale

Tổng hợp từ lần dựng server thực tế (Windows, TLauncher + OptiFine, chơi cùng bạn bè qua Tailscale). Gồm các bước đúng, lỗi đã gặp và cách sửa.

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

## 2. Các bước làm (theo thứ tự)

### Bước 1: Cài Java 21
1. Tải file `.msi` **Temurin 21** ở `adoptium.net`.
2. Ở màn hình **Custom Setup**, bật cả hai mục:
   - **Modify PATH variable**
   - **Set or override JAVA_HOME variable**
3. Đóng hết cmd cũ, mở cmd mới, gõ `java -version`. Kết quả phải là `21.x`.

### Bước 2: Dựng server Paper
1. Tạo thư mục **không dấu, không khoảng trắng**, ví dụ `D:\skyblock-server`.
2. Tải Paper 1.21.11 (xem mục 3 nếu không tìm thấy), chép vào thư mục và đổi tên thành `server.jar`.
3. Bật hiện đuôi file trong File Explorer (View, Show, File name extensions).
4. Tạo file `start.bat` (chọn Save as type: All files) với nội dung:
   ```bat
   @echo off
   java -Xmx4G -Xms4G -jar server.jar nogui
   pause
   ```
   Máy có 8GB RAM trở xuống thì đổi `4G` thành `2G`.
5. Chạy `start.bat` lần 1. Server tự dừng và báo về EULA.
6. Mở `eula.txt`, đổi `eula=false` thành `eula=true`, lưu lại.
7. Chạy `start.bat` lần 2, đợi đến dòng có chữ **Done**, rồi gõ `stop` để tắt.

### Bước 3: Cho TLauncher vào được
Mở `server.properties` và sửa:
```
online-mode=false
```
Bật whitelist trong console (không có dấu `/`):
```
whitelist on
whitelist add <tên nhân vật>
```

### Bước 4: Cài BentoBox rồi BSkyBlock
1. Chép `BentoBox-3.16.2.jar` vào thư mục `plugins`.
2. Chạy server một lần, rồi `stop`. Lúc này thư mục `plugins\BentoBox\addons` được tạo.
3. Chép `BSkyBlock-1.20.0.jar` vào `plugins\BentoBox\addons`.
4. Chạy lại server. Lần đầu sẽ lâu hơn vì phải tạo thế giới skyblock.
5. Kiểm tra console: có `Loaded 1 addons`, `Enabling BSkyBlock` và **không có** chữ `ERROR`.

### Bước 5: Vào game
- Trong Minecraft (TLauncher, chọn OptiFine 1.21.11): Multiplayer, Add Server.
  - Chính máy host: nhập `localhost`.
  - Bạn bè qua Tailscale: nhập `100.x.y.z:25565`.
- Trong game nhấn **T** rồi gõ `/island` (hoặc `/is`) để tạo đảo.
- Cấp quyền admin cho mình (trong console): `op <tên>`.

### Bước 6: Cho bạn bè vào qua Tailscale
1. Bạn bè cài Tailscale (`tailscale.com/download`) và đăng nhập **vào đúng tailnet của host**.
2. Lấy IP của máy host bằng `tailscale ip -4` hoặc bấm biểu tượng Tailscale ở taskbar. IP có dạng `100.x.y.z`.
3. Bạn bè kiểm tra: `tailscale ping <IP host>` phải ra `pong from ...`.
4. Host chạy `whitelist add <tên bạn>` trong console.
5. Bạn bè nhập `<IP host>:25565` trong Multiplayer, dùng **đúng phiên bản 1.21.11**.

### Bước 7: Chung đảo với bạn
Mặc định BSkyBlock tạo **mỗi người một đảo riêng**. Muốn chung đảo (cả hai đang online):
```
/is team invite <tên bạn>      (người chủ đảo gõ)
/is team accept                (người được mời gõ)
```
Nếu người được mời đã có đảo riêng thì đảo cũ sẽ bị bỏ, nên lấy hết đồ trước.

## 3. Lỗi đã gặp và cách xử lý

### Chuẩn bị
| Lỗi | Nguyên nhân và cách sửa |
|---|---|
| `java` is not recognized | Chưa cài Java hoặc chưa bật Add to PATH. Cài lại, nhớ mở cmd mới sau khi cài. |
| Lỡ cài Java 25 | Vẫn chạy được Paper, nhưng nên dùng Java 21 để ít lỗi plugin. |

### Tải Paper
| Lỗi | Nguyên nhân và cách sửa |
|---|---|
| Không thấy bản 1.21.11 trên trang tải | Minecraft đã chuyển sang đánh số 26.x nên trang mặc định hiện bản mới nhất. Vào **Downloads**, mở **build explorer**, chọn nhóm **1.21**, rồi **1.21.11**, lấy build có số cao nhất. Nhãn UNSUPPORTED chỉ là không còn cập nhật, vẫn dùng được. |
| `Unable to access jarfile server.jar` | Tên file sai (ví dụ `sever.jar`, `server.jar.jar`) hoặc `start.bat` không cùng thư mục với `server.jar`. |

### Plugin
| Lỗi | Nguyên nhân và cách sửa |
|---|---|
| BentoBox 3.23.x báo `class file version 69.0 ... up to 65.0` | Bản đó cần **Java 25**. Với Java 21 hãy hạ xuống bản cũ hơn (đã dùng 3.16.2). Modrinth ghi "hỗ trợ 1.21.11" nhưng không nói rõ yêu cầu Java. |
| BSkyBlock mới đòi BentoBox cao | BSkyBlock 1.20.0 cần BentoBox từ 3.13.0 trở lên. Bản 3.16.2 đáp ứng được. Luôn đọc mô tả từng bản trước khi tải. |
| Tải gói ZIP từ `download.bentobox.world` | ZIP chứa cả `BentoBox-3.23.x.jar` (không dùng được với Java 21). Chỉ lấy các file addon cần thiết, thêm **từng cái một** và chạy thử sau mỗi lần. |

### Vào game
| Lỗi | Nguyên nhân và cách sửa |
|---|---|
| `You are not whitelisted on this server!` | Tên whitelist phải **trùng từng ký tự** (kể cả hoa thường, số) với tên trong launcher. Từng whitelist `TranNgocAnh` trong khi tên thật là `TranNgocAnh2003`. Xem tên đúng trong dòng log `Disconnecting <tên> ...`. |

### Tailscale (rắc rối lớn nhất)
| Triệu chứng | Nguyên nhân và cách sửa |
|---|---|
| Bạn bè bị `Connection timed out`, `tailscale ping` báo `no matching peer` | Tài khoản bạn bè đã được thêm làm Member, nhưng app Tailscale trên máy họ vẫn đăng nhập vào **tailnet riêng của họ**. |
| Dấu hiệu nhận biết | Trang Machines của host chỉ có 1 máy, và `tailscale switch --list` bên bạn bè chỉ có 1 dòng. |
| Cách sửa đã dùng được | Bạn bè bấm biểu tượng Tailscale, bấm vào dòng tài khoản, chọn **Add another account...** và đăng nhập lại. Trang báo `logged in to the <tailnet host>` là thành công. |
| Trang `/admin` báo "You don't have access to the admin console" | Bình thường với Member, chỉ cần bỏ qua. Không cần vào admin console. |
| Còn timeout dù đã có `pong` | Mở cổng trên tường lửa của máy host (cmd quyền Administrator): |

```
netsh advfirewall firewall add rule name="Minecraft 25565" dir=in action=allow protocol=TCP localport=25565 remoteip=100.64.0.0/10
```

## 4. Phân biệt hai nơi gõ lệnh

| Nơi gõ | Cách gõ | Ví dụ |
|---|---|---|
| **Trong game** (nhấn T) | Có dấu `/` | `/island`, `/is sethome`, `/is team invite <tên>` |
| **Console server** (cửa sổ đen) | **Không** có dấu `/` | `whitelist add <tên>`, `op <tên>`, `stop` |

## 5. Mẹo để lần sau nhanh hơn

- **Kiểm tra trước khi tải:** xem yêu cầu Java và phiên bản Minecraft/BentoBox API trong mô tả từng bản plugin.
- **Thêm từng thứ một:** thêm một plugin hoặc addon, chạy thử, đọc console, rồi mới thêm cái tiếp.
- **Đọc console khi có lỗi:** hầu hết lỗi chỉ rõ nguyên nhân trong vài dòng đầu của `ERROR`.
- **Làm Tailscale trước khi gọi bạn vào:** kiểm tra `tailscale ping` ra `pong` trước khi thử vào game.
- **Không nâng cấp bừa:** đừng thay Paper, BentoBox hay Java khi mọi thứ đang chạy ổn. Đừng nâng BentoBox lên 3.23.x khi dùng Java 21.
- **Quy tắc tên người chơi:** whitelist đúng tên. Vì `online-mode=false`, UUID sinh từ tên nên **đổi tên nhân vật là mất đảo và dữ liệu**.
- **Sao lưu:** copy cả thư mục server ra chỗ khác khi đã `stop`.
- **Tắt server đúng cách:** luôn gõ `stop`, đừng bấm dấu X.
- **Tắt chế độ Sleep của Windows** khi đang host.
- **Máy host phải bật** và cửa sổ server phải đang mở thì bạn bè mới vào được.

## 6. Bảo mật khi để `online-mode=false`

- Ai biết tên người chơi đều có thể vào và đóng giả (kể cả OP). Chỉ nên cho phép truy cập qua Tailscale và **không mở cổng 25565 ra internet**.
- Luôn bật whitelist.
- Muốn có mật khẩu cho từng người, cài thêm plugin **AuthMe Reloaded**.
- Bạn bè là Member trong tailnet của host có thể thấy các máy khác trong tailnet. Có thể giới hạn bằng **Access controls** trên trang admin Tailscale.

## 7. Thêm addon sau này

1. `stop` server.
2. Chép từng file addon `.jar` vào `plugins\BentoBox\addons`.
3. Chạy lại, kiểm tra console. Cái nào báo `ERROR` thì xóa file đó và chọn bản cũ hơn.

Các addon nên cân nhắc: **Level**, **Challenges**, **Warps**, **Limits**. Các addon như Bank, Biomes, Border, ControlPanel là tùy chọn.

## 8. Lệnh hữu ích

**Trong game**
- `/is`: về đảo (hoặc tạo đảo nếu chưa có)
- `/is sethome`: đặt điểm nhà
- `/is team invite <tên>` / `/is team accept`: mời và chấp nhận vào đội
- `/is team leave`: rời đội
- `/is reset`: làm lại đảo

**Console server**
- `whitelist add <tên>` / `whitelist remove <tên>` / `whitelist list`
- `op <tên>` / `deop <tên>`
- `stop`: tắt server
