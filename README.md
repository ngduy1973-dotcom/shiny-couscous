# TinNhanWebApp — Biến Web Messenger (index.html) thành app iOS .ipa

Project này đóng gói file `index.html` (Web Messenger của bạn) vào `WKWebView`,
biến nó thành app iOS native có thể build thành `.ipa` và cài lên iPhone.

## Cấu trúc
```
TinNhanWebApp/
├── TinNhanWebApp/
│   ├── TinNhanWebApp.swift   # Entry point (SwiftUI lifecycle)
│   ├── ContentView.swift     # Khung app + yêu cầu quyền camera/mic
│   ├── WebView.swift         # WKWebView load index.html, xử lý file picker & alert
│   ├── Info.plist            # Quyền: Camera, Micro, Photo Library, ATS
│   └── Web/
│       └── index.html        # ← Web Messenger của bạn (đã bundle sẵn)
└── README.md
```

## Cách build (cần macOS + Xcode 14+)
1. Mở Xcode > **Create a new Xcode project** > iOS > **App**
   - Product Name: `TinNhanWebApp`
   - Interface: **SwiftUI**, Language: **Swift**
   - Bỏ chọn "Use Core Data" / "Include Tests"
2. Xóa file `ContentView.swift` mặc định
3. Kéo toàn bộ 4 file (`TinNhanWebApp.swift`, `ContentView.swift`, `WebView.swift`, `Info.plist`) và thư mục `Web/` vào project, chọn **Copy items if needed**
4. Chọn target > tab **General** > đảm bảo `Info.plist` được nhận diện
5. Tab **Signing & Capabilities** > chọn Team (Apple ID của bạn)
6. Chọn thiết bị iPhone (máy thật hoặc Simulator) > **Cmd+R** để chạy
7. Xuất `.ipa`: **Product > Archive > Distribute App > Development / Ad Hoc**

## Lưu ý quan trọng về tính năng
- **Gọi video (WebRTC/PeerJS)**: hoạt động trên **iOS 14.3+** khi cài lên máy thật.
  Trên Simulator camera/mic không hoạt động. Đảm bảo file `index.html` kết nối
  PeerJS server qua **https/wss** (không phải http/ws) — iOS chặn mixed content.
- **Gửi ảnh/tệp**: `<input type="file">` hoạt động nguyên bản trong WKWebView.
- **Ghi âm**: `getUserMedia({audio:true})` cần quyền Microphone (đã khai báo).
- **localStorage / sessionStorage**: hoạt động, dữ liệu lưu cục bộ trên máy.
- **Chat head bubble (position:fixed)**: trong WKWebView nó neo vào viewport web,
  hoạt động bình thường.

## Không có Mac? Build cloud
- **Codemagic** (codemagic.io) — kết nối GitHub repo, build iOS, xuất `.ipa` (free tier)
- **GitHub Actions** runner `macos-latest` — tự động build mỗi push
- **MacInCloud** — thuê Mac cloud theo giờ (~$1/giờ), dùng Xcode trực tiếp

## Cài `.ipa` lên iPhone (không cần tài khoản $99/năm)
- **AltStore** (altstore.io) hoặc **Sideloadly** (sideloadly.io) + Apple ID miễn phí
  (cần gia hạn mỗi 7 ngày, tối đa 3 app). Tài khoản Apple Developer $99/năm không giới hạn.

## Cập nhật web app về sau
Chỉ cần thay file `Web/index.html` bằng bản mới rồi build lại — không cần sửa code Swift.
Hoặc nếu bạn host web trên một domain, sửa `WebView.swift` để `load(URLRequest)` tới URL đó
thay vì load file local (app sẽ luôn lấy bản mới nhất).
