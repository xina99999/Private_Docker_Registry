

````markdown
# 🐳 Docker Private Registry with Web UI + SSL + Authentication

Triển khai một hệ thống **Docker Private Registry** giống như Docker Hub, có giao diện web quản lý, bảo vệ bằng SSL và xác thực người dùng (`htpasswd`).  
Hệ thống chạy trên **Docker Compose** gồm 3 thành phần chính:

- `registry`: Docker registry v2 chính thức
- `registry-ui`: Giao diện web (joxit/docker-registry-ui)
- `nginx`: Reverse proxy, hỗ trợ SSL và auth

---

## 🗂 Cấu trúc thư mục

```bash
docker-registry/
├── data/                    # Volume chứa dữ liệu image registry
├── auth/                   # Chứa file htpasswd (xác thực người dùng)
│   └── htpasswd
├── certs/                  # Chứa SSL certificate (domain.crt, domain.key)
│   ├── domain.crt
│   └── domain.key
├── nginx/
│   └── default.conf        # Cấu hình nginx reverse proxy
├── docker-compose.yml      # Cấu hình docker compose
├── README.md               # Tài liệu hướng dẫn
````

---

## 🚀 Cách sử dụng

### 1. Clone hoặc tạo cấu trúc thư mục

```bash
git clone <repo-url> docker-registry
cd docker-registry
```

### 2. Tạo SSL certificate (tự ký)

```bash
mkdir -p certs
openssl req -newkey rsa:2048 -nodes -keyout certs/domain.key -x509 -days 365 -out certs/domain.crt
```

### 3. Tạo tài khoản `htpasswd`

```bash
mkdir -p auth
htpasswd -c auth/htpasswd admin  # bạn sẽ được hỏi mật khẩu
```

### 4. Khởi động dịch vụ

```bash
docker-compose up -d
```

### 5. Truy cập

* Web UI: [https://your-domain.com](https://your-domain.com)
* API Registry: `https://your-domain.com/v2/_catalog`
* Docker login:

```bash
docker login your-domain.com
# nhập user/mật khẩu đã tạo trong htpasswd
```

---

## ⚙ Cấu hình thêm

* Mặc định chạy cổng `443`, nếu cần dùng cổng khác hãy chỉnh trong `docker-compose.yml` và `nginx/default.conf`.
* Domain cấu hình trong `nginx/default.conf`, thay `your-domain.com` thành domain thực tế.

---

## 🔐 Bảo mật

* Sử dụng `htpasswd` để kiểm soát quyền truy cập Registry (bắt buộc login khi push/pull).
* Giao tiếp an toàn qua `https://` với SSL certificate (có thể thay bằng Let's Encrypt nếu muốn).

---

## 📦 Push & Pull image

### Push image

```bash
docker tag nginx:alpine your-domain.com/nginx:alpine
docker push your-domain.com/nginx:alpine
```

### Pull image

```bash
docker pull your-domain.com/nginx:alpine
```

---

## 🧹 Xóa container & dữ liệu

```bash
docker-compose down -v
```

---

## 📝 Tham khảo

* [Docker Registry v2](https://docs.docker.com/registry/)
* [joxit/docker-registry-ui](https://github.com/Joxit/docker-registry-ui)
* [Nginx Reverse Proxy](https://docs.nginx.com)

```

---

Bạn có thể lưu file này thành `README.md` trong thư mục dự án hoặc mình có thể giúp bạn upload tất cả lên một GitHub repo public/private tùy bạn chọn – bạn muốn không?
```
