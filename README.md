# Bình Yên Trong Gió

Đây là trang đọc truyện tĩnh xây dựng bằng **Jekyll**. Nội dung được lưu dưới dạng Markdown trong Git repository và GitHub Pages tự build lại trang mỗi khi có commit.

## Đồng bộ hoạt động như thế nào?

Jekyll không có máy chủ ứng dụng: thay đổi chỉ nằm trong trình duyệt của một người sẽ **không thể** tự đến người đọc khác. Bản triển khai này dùng Git repository làm kho lưu trữ chung:

1. Tác giả đăng hoặc sửa nội dung tại `/admin/`.
2. CMS xác thực tác giả với GitHub và tạo commit vào nhánh `main`.
3. Workflow GitHub Pages nhận commit, build Jekyll và phát hành bản mới.
4. Mọi máy chủ, IP và trình duyệt đều nhận cùng một bản nội dung sau khi Pages triển khai xong.

Các thao tác này không dùng cơ sở dữ liệu thời gian thực. Vì trang tĩnh phải được build lại, cập nhật không xuất hiện tức thì; thời gian chờ phụ thuộc vào hàng đợi GitHub Pages.

## Cấu trúc nội dung

- `_stories/`: mỗi truyện là một tệp Markdown có front matter.
- `_chapters/`: mỗi chương là một tệp Markdown, liên kết với truyện bằng `story_id`.
- `_announcements/`: thông báo có thể đăng và sửa từ CMS.
- `admin/`: giao diện Decap CMS cho tác giả.
- `assets/`: CSS và các tài nguyên tĩnh.

## Thiết lập quản trị trực tiếp tại `/admin/`

Decap CMS ghi nội dung thành commit GitHub, do đó mọi chỉnh sửa được đồng bộ qua repository. GitHub Pages là trang tĩnh nên cần một OAuth proxy rất nhỏ để giữ kín Client Secret; proxy **không lưu truyện, chương hay bình luận**.

Cách triển khai Netlify từng bước, kèm giá trị cần điền và cách xử lý lỗi, nằm trong [`docs/oauth-proxy-netlify.md`](docs/oauth-proxy-netlify.md). Tóm tắt:

1. Fork `decaporg/decap-cms-oauth-provider` và deploy proxy trên Netlify.
2. Tạo GitHub OAuth App. Homepage URL là trang Jekyll, còn **Authorization callback URL phải là** `https://TEN-PROXY.netlify.app/callback` — không phải `/admin/`.
3. Đặt `OAUTH_CLIENT_ID`, `OAUTH_CLIENT_SECRET` và `ORIGIN` trong biến môi trường Netlify. `ORIGIN` là URL chính xác của trang, gồm cả `/binhyentronggio` nếu dùng GitHub Pages project site.
4. Trong `admin/config.yml`, thay `YOUR_GITHUB_ACCOUNT/binhyentronggio` và `https://YOUR-OAUTH-PROXY.example.com` bằng giá trị thật, rồi commit lên `main`.
5. Sau khi Pages deploy, vào `/admin/`, đăng nhập GitHub bằng tài khoản có quyền ghi repository và lưu nội dung. Mỗi lần lưu tạo commit, tự kích hoạt workflow deploy và đồng bộ cho mọi người.

> Không đưa `OAUTH_CLIENT_SECRET` vào repository, `admin/config.yml` hoặc GitHub Pages. Secret chỉ được đặt trong môi trường của OAuth proxy.

## Bình luận của độc giả

Trang chương đã sẵn sàng cho [giscus](https://giscus.app/), hệ thống bình luận đồng bộ dùng GitHub Discussions. Để bật:

1. Bật **Discussions** cho repository trong GitHub Settings.
2. Vào giscus.app, cài GitHub App cho repository và tạo category bình luận.
3. Sao chép bốn giá trị `repo`, `repo_id`, `category`, `category_id` vào mục `comments` của `_config.yml`, sau đó đổi `enabled` thành `true`.
4. Commit thay đổi; vùng thảo luận sẽ xuất hiện ở cuối mọi chương. Bình luận và trả lời được đồng bộ qua GitHub Discussions cho tất cả khách truy cập.

Nếu cần khách không có GitHub gửi bình luận, cần thêm một API/ứng dụng chủ sở hữu của bạn (ví dụ Worker + PostgreSQL/SQLite) có chống spam và kiểm duyệt. Không nên ghi bình luận trực tiếp từ JavaScript tĩnh vào repository vì sẽ làm lộ thông tin ghi hoặc dễ bị lạm dụng.

## Chạy cục bộ

Yêu cầu Ruby và Bundler:

```bash
bundle install
bundle exec jekyll serve
```

Build kiểm tra tĩnh bằng:

```bash
bundle exec jekyll build
```

## Triển khai

Workflow GitHub Pages tại `.github/workflows/deploy.yml` dùng `actions/jekyll-build-pages`, tạo artifact từ `_site` và phát hành mỗi khi nhánh `main` có commit.

### Debug lỗi `bundle install` / `jekyll build`

Đã kiểm tra lệnh build trong môi trường hiện tại. Bundler chưa kịp đọc hay build Jekyll vì proxy mạng chặn kết nối đến `https://rubygems.org/` bằng HTTP `403 CONNECT tunnel failed`. Đây là lỗi mạng của môi trường chạy, không phải lỗi Liquid/YAML của repository.

Trên máy của bạn, kiểm tra theo thứ tự:

```bash
curl -I https://rubygems.org/
bundle install
bundle exec jekyll build --trace
```

Lệnh `curl` phải trả về mã thành công/chuyển hướng, không phải `403`. Nếu vẫn là `403`, tắt hoặc cấu hình lại proxy/VPN/firewall cho phép `rubygems.org:443`, rồi chạy lại. Gemfile hiện chỉ dùng `jekyll` và `webrick`, là các dependency cần thiết để build và chạy Jekyll cục bộ; GitHub Pages CI vẫn tự build bằng action Jekyll. 【Không chạy `bundle install` với quyền root trên máy cá nhân.】
