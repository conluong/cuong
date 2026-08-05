# Ôn Thi THPT — Website chia sẻ tài liệu ôn thi tốt nghiệp THPT

Website tĩnh (HTML/CSS/JS thuần, không framework) dùng **Firebase** làm backend:
Authentication, Cloud Firestore, Hosting, Analytics.

## 1. Cấu trúc thư mục

```
index.html               Trang chủ
login.html                Đăng nhập
register.html              Đăng ký
forgot-password.html       Quên mật khẩu
documents.html              Danh sách tài liệu (tìm kiếm, lọc)
document-detail.html        Chi tiết tài liệu (xem trước PDF)
profile.html                Hồ sơ cá nhân
admin.html                  Dashboard admin (biểu đồ, thống kê)
admin-users.html            Quản lý người dùng
admin-documents.html        Quản lý tài liệu
404.html                    Trang lỗi 404
css/                        style.css (design system dùng chung), home.css, auth.css, documents.css, admin.css
js/                         firebase-config.js, main.js, auth.js, documents.js, profile.js, admin.js
firebase/                   firestore.rules, firebase.json, firestore.indexes.json
assets/icons/               favicon.svg
robots.txt, sitemap.xml     SEO
```

## 2. Thiết lập Firebase

1. Tạo project tại [console.firebase.google.com](https://console.firebase.google.com).
2. Bật **Authentication** → phương thức **Email/Password**.
3. Tạo **Cloud Firestore** (ở chế độ Production).
4. Vào **Project settings → General → Your apps → Web app** để lấy config, dán vào `js/firebase-config.js` (thay các giá trị `YOUR_...`).
5. Vào **Firestore → Rules**, dán nội dung file `firebase/firestore.rules` rồi bấm **Publish**.
6. (Tùy chọn) Bật **Google Analytics** nếu muốn dùng `measurementId`.

## 3. Tạo tài khoản Admin đầu tiên

Mặc định mọi tài khoản đăng ký mới có `role: "user"`. Để tạo Admin:
1. Đăng ký một tài khoản bình thường qua `register.html`.
2. Vào **Firestore Console → collection `users` → chọn document theo UID**.
3. Sửa field `role` từ `"user"` thành `"admin"`.
4. Đăng nhập lại — tài khoản sẽ thấy mục **"Trang quản trị"** trong menu và truy cập được `admin.html`.

## 4. Thêm tài liệu

Vào **admin-documents.html → Thêm tài liệu** → dán **link chia sẻ Google Drive** (đã bật "Bất kỳ ai có đường liên kết").
Hệ thống tự trích xuất File ID để tạo:
- Link xem trước: `drive.google.com/file/d/{id}/preview`
- Link tải xuống: `drive.google.com/uc?export=download&id={id}`
- Thumbnail: `drive.google.com/thumbnail?id={id}`

## 5. Chạy thử cục bộ

Vì dùng module `<script>` thường (không phải ES module), bạn có thể mở trực tiếp bằng
[Live Server (VS Code)](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)
hoặc chạy:
```bash
npx serve .
```

## 6. Triển khai lên Firebase Hosting

```bash
npm install -g firebase-tools
firebase login
firebase init hosting   # chọn project, public directory: "." (thư mục gốc)
firebase deploy
```

## 7. Triển khai lên Netlify

Kéo thả toàn bộ thư mục dự án vào [app.netlify.com/drop](https://app.netlify.com/drop),
hoặc kết nối repository Git và deploy tự động — không cần cấu hình build (site tĩnh).

## 8. Lưu ý bảo mật

- `firestore.rules` đã giới hạn: chỉ `admin` được thêm/sửa/xóa tài liệu và quản lý người dùng.
- Việc **xóa hoàn toàn tài khoản Authentication** (không chỉ dữ liệu Firestore) cần **Cloud Function** với Admin SDK — phần quản lý người dùng trong bản này xóa hồ sơ Firestore và có thể khóa đăng nhập (`status: "locked"`), đã đủ để chặn truy cập nhưng chưa xóa tài khoản Auth gốc.
- Đừng commit `js/firebase-config.js` với API key thật lên repository public nếu dự án có quy tắc bảo mật riêng — API key của Firebase Web app vốn được thiết kế để public, an toàn được đảm bảo bởi Firestore Rules, không phải bằng cách giấu key.
