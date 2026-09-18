# Bình Yên Trong Gió

Đây là trang đọc truyện tĩnh xây dựng bằng **Jekyll**. Nội dung được quản lý ngay trong repository, không cần Node.js server, dịch vụ cơ sở dữ liệu bên ngoài hoặc biến môi trường đám mây.

## Cấu trúc nội dung

- `_stories/`: mỗi truyện là một tệp Markdown có metadata ở front matter.
- `_chapters/`: mỗi chương là một tệp Markdown, liên kết với truyện bằng `story_id`.
- `_data/announcements.json`: thông báo hiển thị ở trang chủ.
- `assets/`: CSS và các tài nguyên tĩnh.

Để thêm truyện hoặc chương, tạo tệp Markdown mới trong collection tương ứng. Ví dụ chương cần `story_id`, `chapter_number`, `title` và `permalink` trong front matter.

## Chạy cục bộ

Yêu cầu Ruby và Bundler:

```bash
bundle install
bundle exec jekyll serve
```

Mở địa chỉ được Jekyll hiển thị. Build kiểm tra tĩnh bằng:

```bash
bundle exec jekyll build
```

## Triển khai

Workflow GitHub Pages tại `.github/workflows/deploy.yml` dùng `actions/jekyll-build-pages` và tạo artifact từ `_site` mà không cài Node packages hay truyền secrets của dịch vụ bên ngoài.
