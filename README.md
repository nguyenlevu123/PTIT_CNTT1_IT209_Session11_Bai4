# Bài 4: Khai báo GitHub Secrets và cấu hình Đăng nhập Container Registry

## Mục tiêu
- Cấu hình an toàn thông tin đăng nhập Container Registry (GitHub Container Registry `ghcr.io` hoặc Docker Hub) thông qua GitHub Repository Secrets.
- Tự động hóa đăng nhập bằng `docker/login-action@v3` trong luồng GitHub Actions mà không để lộ Token/Password trong log hoặc mã nguồn.
- Đóng gói ứng dụng Spring Boot thành Docker Image chuẩn bị cho giai đoạn triển khai (CD).

---

## 1. Cấu trúc Dự án
```text
ss11/b4/
├── .github/
│   └── workflows/
│       └── docker-registry.yml  # Pipeline đăng nhập registry & build Docker image
├── gradle/wrapper/
├── src/
│   └── main/java/com/example/demo/DemoApplication.java
├── Dockerfile                  # Base eclipse-temurin:17-jre-alpine
├── build.gradle
├── settings.gradle
├── gradlew
└── README.md
```

---

## 2. Hướng dẫn Thiết lập Secrets trên GitHub
1. Vào repository trên GitHub: **Settings** -> **Secrets and variables** -> **Actions**.
2. Nhấn **New repository secret**:
   - `REGISTRY_USERNAME`: Tên tài khoản GitHub / Docker Hub (ví dụ `catim07`).
   - `REGISTRY_TOKEN`: Personal Access Token (PAT) có quyền `write:packages` hoặc Docker Hub Access Token.
3. Khi workflow chạy, GitHub Actions tự động che giấu giá trị của secret này thành `***` (masking) trên toàn bộ log hệ thống.

---

## 3. Cấu hình Workflow An toàn
```yaml
- name: Log in to GitHub Container Registry
  uses: docker/login-action@v3
  with:
    registry: ghcr.io
    username: ${{ github.actor }}
    password: ${{ secrets.REGISTRY_TOKEN || secrets.GITHUB_TOKEN }}
```

---

## 4. Kết luận
Việc sử dụng Secrets và biến môi trường tách biệt hoàn toàn thông tin bảo mật nhạy cảm khỏi mã nguồn, đáp ứng tiêu chuẩn DevSecOps trong quy trình triển khai phần mềm liên tục.
