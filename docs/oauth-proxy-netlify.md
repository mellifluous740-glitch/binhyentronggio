# Triển khai OAuth proxy Decap CMS trên Netlify

Tài liệu này triển khai **proxy xác thực**, không triển khai nội dung của trang. Nội dung vẫn được ghi vào GitHub repository thông qua Decap CMS.

## 1. Tạo proxy

1. Fork repository `decaporg/decap-cms-oauth-provider` vào GitHub của bạn.
2. Truy cập Netlify, chọn **Add new site → Import an existing project**, rồi chọn repository vừa fork.
3. Không cần lệnh build. Deploy với cấu hình có sẵn của project và ghi lại URL, ví dụ `https://binh-yen-oauth.netlify.app`.
4. Trong **Site configuration → Environment variables**, thêm:
   - `OAUTH_CLIENT_ID`: Client ID của GitHub OAuth App ở bước 2.
   - `OAUTH_CLIENT_SECRET`: Client secret của GitHub OAuth App ở bước 2.
   - `ORIGIN`: chính xác URL trang Jekyll, ví dụ `https://ten-tai-khoan.github.io` hoặc `https://ten-tai-khoan.github.io/binhyentronggio`.
5. Deploy lại proxy sau khi thêm các biến môi trường.

## 2. Tạo GitHub OAuth App

1. Mở GitHub **Settings → Developer settings → OAuth Apps → New OAuth App**.
2. Điền Homepage URL là URL trang Jekyll.
3. Điền **Authorization callback URL** là `https://TEN-PROXY.netlify.app/callback`. Callback phải trỏ đến proxy, **không phải** `/admin/` của GitHub Pages.
4. Sau khi tạo app, sao chép Client ID và tạo Client Secret. Chỉ đưa hai giá trị này vào biến môi trường Netlify; không commit chúng.

## 3. Kết nối CMS

Sửa `admin/config.yml`:

```yaml
backend:
  name: github
  repo: OWNER/binhyentronggio
  branch: main
  base_url: https://TEN-PROXY.netlify.app
  auth_endpoint: auth
```

Commit lên `main`, đợi GitHub Pages deploy, rồi mở `https://TEN-MIEN/admin/`. Đăng nhập bằng tài khoản GitHub có quyền ghi repository. Khi lưu, Decap tạo commit; workflow Pages sẽ phát hành bản mới cho mọi người.

## Kiểm tra và xử lý lỗi

- Mở `https://TEN-PROXY.netlify.app/auth`: proxy phải bắt đầu chuyển hướng sang GitHub, không trả lỗi 404.
- `redirect_uri_mismatch`: callback URL trong OAuth App không đúng chính xác `https://TEN-PROXY.netlify.app/callback`.
- `Not Found` hoặc không thấy repository trong CMS: kiểm tra `repo`, `branch`, và quyền ghi của tài khoản GitHub.
- `Origin not allowed`: giá trị `ORIGIN` phải khớp tuyệt đối nguồn trang (bao gồm đường dẫn repository nếu dùng GitHub Pages project site).
