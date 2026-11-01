# Hướng dẫn build .ipa bằng Codemagic (không cần Mac)

## Tổng quan
Codemagic = dịch vụ build cloud chạy macOS. Bạn đẩy code lên GitHub, Codemagic
tự build ra file `.ipa` và gửi link tải về email. Free tier: 500 phút/tháng
(mỗi build iOS ~5-10 phút).

## Bước 1 — Đẩy code lên GitHub
1. Tạo tài khoản GitHub (miễn phí) tại github.com
2. Tạo repo mới (public hoặc private đều được), tên ví dụ: `tinnhan-ios`
3. Upload TOÀN BỘ nội dung thư mục `TinNhanWebApp/` lên repo (kéo thả trên web
   hoặc dùng GitHub Desktop). Cấu trúc repo PHẢI là:
   ```
   / (root repo)
   ├── codemagic.yaml       ← BẮT BUỘC ở root
   ├── project.yml          ← BẮT BUỘC ở root
   ├── README.md
   ├── GUIDE-Codemagic.md
   └── TinNhanWebApp/
       ├── TinNhanWebApp.swift
       ├── ContentView.swift
       ├── WebView.swift
       ├── Info.plist
       └── Web/
           └── index.html
   ```

## Bước 2 — Tạo tài khoản Codemagic
1. Vào codemagic.io > **Sign up** > đăng nhập bằng GitHub (để tự kết nối repo)
2. Sau khi đăng nhập, tab **Applications** > **Add application**
3. Chọn GitHub > chọn repo `tinnhan-ios` > Codemagic sẽ tự phát hiện `codemagic.yaml`

## Bước 3 — Kết nối tài khoản Apple (để ký tên .ipa)
Có 2 lựa chọn:

### Lựa chọn A — Apple ID miễn phí (không tốn tiền, khuyến nghị cho người mới)
1. Vào appleid.apple.com > Đăng nhập > **App-Specific Passwords** > tạo 1 password
   (ví dụ: `abcd-efgh-ijkl-mnop`) — LƯU LẠI password này
2. Trong Codemagic: **Team settings > Integrations > Developer Portal > Add**
   - Nhập Apple ID (email) + app-specific password vừa tạo
   - Codemagic sẽ tự tạo chứng chỉ Development + provisioning profile
3. Mở `codemagic.yaml` > đảm bảo `distribution_type: development`
4. Cài iPhone của bạn: cắm vào máy tính 1 lần để Codemagic lấy UDID?
   → Với free account, provisioning profile cần UDID thiết bị. Cách đơn giản nhất:
   build **không ký tên** (xem mục cuối file `codemagic.yaml` có comment sẵn),
   rồi dùng **Sideloadly** tự ký + cài (Sideloadly tự đăng ký UDID).

### Lựa chọn B — Tài khoản Apple Developer ($99/năm)
- Có thể xuất `.ipa` dạng App Store / TestFlight / Ad Hoc, cài không giới hạn
- Kết nối bằng App Store Connect API Key (Codemagic > Integrations > Developer Portal)
- Đổi `distribution_type: app_store` hoặc `ad_hoc` trong `codemagic.yaml`

## Bước 4 — Chạy build
1. Trong Codemagic > ứng dụng của bạn > nút **Start new build**
2. Chọn workflow `ios-ipa` > **Start new build**
3. Chờ ~5-10 phút. Nếu thành công, tải `.ipa` tại tab **Artifacts** của build.

## Bước 5 — Cài .ipa lên iPhone
### Nếu dùng Windows (không có Mac): Sideloadly
1. Tải Sideloadly tại sideloadly.io (cài iTunes cho Windows cùng lúc)
2. Cắm iPhone vào máy tính qua cáp USB
3. Mở Sideloadly > kéo file `.ipa` vào > nhập Apple ID > Start
4. Trên iPhone: **Cài đặt > Chung > VPN & Quản lý thiết bị** > tin cậy chứng chỉ
5. Mở app TinNhan. (Gia hạn mỗi 7 ngày bằng cách cắm lại + Sideloadly > Start)

### Nếu có Mac: AltStore
- Tải AltServer tại altstore.io > cài vào iPhone > sideload .ipa

## Khắc phục sự cố thường gặp
| Lỗi | Nguyên nhân | Cách sửa |
|---|---|---|
| `Code signing error` | Chưa kết nối Apple ID | Bước 3, hoặc build không ký tên (comment trong codemagic.yaml) |
| `xcodegen: command not found` | XcodeGen chưa cài | Script đã có `brew install xcodegen` — kiểm tra lại |
| `index.html not found` khi mở app | Thư mục Web không bundle vào | Kiểm tra `project.yml` có dòng `- path: TinNhanWebApp/Web` với `type: folder` |
| Gọi video không hoạt động | iOS < 14.3 hoặc kết nối ws:// | Đảm bảo iPhone iOS ≥15, PeerJS dùng wss:// (đã bật ATS trong Info.plist) |
| Build hết phút free | Free tier 500 phút/tháng | Đợi tháng sau, hoặc trả phí ~$0.095/phút |

## Cập nhật app sau này
Chỉ cần sửa `TinNhanWebApp/Web/index.html` (web app của bạn), push lên GitHub,
Codemagic tự build lại (bật trigger auto-build trong Workflow settings).
