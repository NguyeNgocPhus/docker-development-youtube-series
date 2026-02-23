Rõ rồi 👍. Mình sẽ đóng gói lại hướng dẫn này thành 1 file **`mysql-setup.md`** để bạn có thể lưu lại và dùng như tài liệu triển khai MySQL cho WordPress.

---

# `mysql-setup.md`

# 🚀 MySQL setup for WordPress (Docker)

## 1️⃣ Tạo thư mục để chứa dữ liệu MySQL

```bash
mkdir -p ~/mysql_data
```

Thư mục này sẽ được mount ra host để dữ liệu không bị mất khi container bị xoá.

---

## 2️⃣ Chạy MySQL container

```bash
docker run -d \
  --name wp-mysql \
  -e MYSQL_ROOT_PASSWORD=StrongRootPass123 \
  -e MYSQL_DATABASE=wordpress \
  -e MYSQL_USER=wp_user \
  -e MYSQL_PASSWORD=StrongPass123 \
  -v ~/mysql_data:/var/lib/mysql \
  -p 3306:3306 \
  mysql:8.0 \
  --default-authentication-plugin=mysql_native_password
```

**Giải thích:**

* `--name wp-mysql` : tên container.
* `MYSQL_ROOT_PASSWORD` : mật khẩu root.
* `MYSQL_DATABASE` : database mặc định (`wordpress`).
* `MYSQL_USER` + `MYSQL_PASSWORD` : user dành riêng cho WordPress.
* `-v ~/mysql_data:/var/lib/mysql` : mount dữ liệu ra host.
* `-p 3306:3306` : publish port 3306 để WordPress có thể kết nối.
* `--default-authentication-plugin=mysql_native_password` : cần thiết để WordPress kết nối MySQL 8 (tránh lỗi auth).

---

## 3️⃣ Cấu hình WordPress để kết nối DB

Mở file `wp-config.php` trong thư mục WordPress và chỉnh sửa:

```php
define( 'DB_NAME', 'wordpress' );
define( 'DB_USER', 'wp_user' );
define( 'DB_PASSWORD', 'StrongPass123' );
define( 'DB_HOST', 'localhost:3306' );
```

👉 Nếu WordPress chạy trong **container khác**, thì `DB_HOST` nên đặt là **tên container MySQL** (`wp-mysql`) thay vì `localhost`.

---

## 4️⃣ Kiểm tra kết nối

Đảm bảo MySQL đã sẵn sàng và có thể truy cập:

```bash
docker exec -it wp-mysql mysql -u wp_user -p wordpress
```

---

## ✅ Kết quả

* WordPress sẽ kết nối thành công tới DB MySQL trong container.
* Dữ liệu được lưu trữ bền vững trong `~/mysql_data`.

---

👉 Nếu bạn muốn quản lý dễ hơn, có thể viết file `docker-compose.yml` chỉ để chạy MySQL thay vì dùng `docker run`.

---

Bạn có muốn mình viết thêm **`docker-compose.yml` mẫu** và thêm vào cuối file `.md` này để chạy MySQL gọn hơn không?
