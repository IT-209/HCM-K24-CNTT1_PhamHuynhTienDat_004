# DevOps Final – Đề 004: Quản lý kho hàng

Bộ mã nguồn khởi đầu cho bài thi **DevOps Fundamentals – Cuối kỳ**.
Ứng dụng hiển thị **danh sách hàng tồn kho** lấy từ MySQL thông qua Spring Boot, trả về **dạng JSON** qua Nginx (reverse proxy).

## Kiến trúc

```
Trình duyệt ── http://<IP_máy_chủ>:<APP_PORT>
    ▼
[nginx]  nginx:1.27-alpine        GET /*  → app:8080
    ▼
[app]    Spring Boot 4 (JRE 21)   GET /api/items → mysql:3306
    ▼
[mysql]  mysql:8.0                volume mysql_data, KHÔNG public cổng mysql
```

## Cấu trúc thư mục

```
devops-final-de004-phamhuynhtiendat/     ← Mã nguồn Spring Boot 4 (Java 21, Gradle)
├── src/main/java/com/ptit/devops/inventory/   ← Controller, Entity, Repository
├── src/main/resources/application.properties
├── build.gradle                   ← Build ra file build/libs/app.jar
├── settings.gradle
├── Dockerfile                     ← Multi-stage build hoàn chỉnh
├── .dockerignore                  
├── nginx/default.conf             ← Reverse proxy cấu hình cổng 8080
├── database/init.sql              ← Bảng items + 8 bản ghi mẫu
├── docker-compose.yml             ← Cấu hình hoàn chỉnh TODO 1–5
├── .github/workflows/ci-cd.yml    ← Pipeline CI/CD hoàn chỉnh TODO 1–6
├── student.env                    ← Thông tin sinh viên & tham số triển khai
├── screenshots/                   ← Ảnh minh chứng (Phần 5)
├── submission-content/            ← Toàn bộ tệp minh chứng khi nộp bài
├── .gitignore                     
└── README.md
```

## API của ứng dụng

| Method | Đường dẫn | Mô tả |
|--------|-----------|-------|
| GET | `/api/items` | Danh sách hàng tồn kho |

## Biến môi trường mà ứng dụng cần

| Biến | Ý nghĩa |
|------|---------|
| `DB_HOST`, `DB_PORT` | Địa chỉ MySQL (mặc định `mysql`, `3306`) |
| `DB_NAME`, `DB_USER`, `DB_PASSWORD` | Database và tài khoản kết nối |
| `APP_VERSION` | Phiên bản (truyền vào lúc build image, ví dụ commit SHA) |

## Chạy thử

```bash
docker compose --env-file student.env up -d --build
docker compose --env-file student.env ps
curl http://localhost:<APP_PORT>/api/items
docker compose --env-file student.env down
```
