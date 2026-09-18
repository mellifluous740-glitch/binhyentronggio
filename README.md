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

Decap CMS ghi nội dung thành commit GitHub, do đó mọi chỉnh sửa được đồng bộ qua repository. Cần thực hiện **một lần**:

1. Tạo một [GitHub OAuth App](https://docs.github.com/developers/apps/building-oauth-apps/creating-an-oauth-app) với callback URL là `https://TEN-MIEN-CUA-BAN/admin/`.
2. Triển khai một OAuth proxy tương thích với Decap CMS dưới tên miền riêng của bạn. Proxy này chỉ đổi mã OAuth thành token; không lưu nội dung truyện. Có thể tự triển khai `netlify/decap-cms-oauth-provider` trên Cloudflare Worker, Render hoặc máy chủ riêng.
3. Mở `admin/config.yml`, thay `YOUR_GITHUB_ACCOUNT/binhyentronggio` bằng `owner/repository` thực tế, rồi thay `https://YOUR-OAUTH-PROXY.example.com` bằng URL proxy ở bước 2.
4. Trong OAuth proxy, đặt `OAUTH_CLIENT_ID` và `OAUTH_CLIENT_SECRET` từ OAuth App. Chỉ cấp quyền ghi cho các tài khoản tác giả tin cậy.
5. Commit cấu hình. Đăng nhập GitHub tại `/admin/`, sau đó tạo hoặc sửa Truyện, Chương và Thông báo. Mỗi lần lưu sẽ tạo commit và tự kích hoạt workflow deploy.

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
