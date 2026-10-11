# Bài 2: Khởi chạy Web Server Nginx với Port Mapping

## Môi trường và mục tiêu

- Ngày thực hiện: 11/10/2026 (Asia/Saigon).
- Terminal: PowerShell trên Windows, Docker Desktop sử dụng Linux Engine.
- Image: `nginx:alpine`.
- Tên container: `my-web`.
- Chế độ chạy: ngầm (`-d`).
- Ánh xạ cổng: `8080:80` — cổng 8080 trên máy cá nhân chuyển tiếp tới cổng 80 của container.
- URL kiểm tra: `http://localhost:8080`.

Các đầu ra dưới đây được ghi từ quá trình thực hành thực tế.

## 1. Khởi chạy container

```text
PS> docker run -d -p 8080:80 --name my-web nginx:alpine
Unable to find image 'nginx:alpine' locally
alpine: Pulling from library/nginx
e72112c14215: Pulling fs layer
745dfb2690dd: Pulling fs layer
e2de96513ba9: Pulling fs layer
d9aae54b5831: Pulling fs layer
6c53d0b2a666: Pulling fs layer
9a9a644fdd6a: Pulling fs layer
e76228b47809: Pulling fs layer
64c8194480fe: Pulling fs layer
d9aae54b5831: Download complete
745dfb2690dd: Download complete
6c53d0b2a666: Download complete
e2de96513ba9: Download complete
9a9a644fdd6a: Download complete
64c8194480fe: Download complete
e76228b47809: Download complete
e2de96513ba9: Pull complete
0deea27e58d9: Download complete
d54d3939625e: Download complete
d9aae54b5831: Pull complete
6c53d0b2a666: Pull complete
745dfb2690dd: Pull complete
9a9a644fdd6a: Pull complete
64c8194480fe: Pull complete
e76228b47809: Pull complete
e72112c14215: Download complete
e72112c14215: Pull complete
Digest: sha256:df221db836e1754089190208cee7eeda94f233197056426eda74a43ab1abeac2
Status: Downloaded newer image for nginx:alpine
20419dd398a857844395f36325e33acf32c4123d34a068fff131b0bef3cfd8ee
```

Mã thoát: `0`. Docker tải image và trả về ID container chạy ngầm.

Kiểm tra container đang chạy và cổng được ánh xạ:

```text
PS> docker ps --filter name=^/my-web$
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS        PORTS                                     NAMES
20419dd398a8   nginx:alpine   "/docker-entrypoint.…"   2 seconds ago   Up 1 second   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   my-web
```

## 2. Truy vấn trang web bằng curl

Trên PowerShell, gọi `curl.exe` để dùng trực tiếp chương trình curl.

```text
PS> curl.exe http://localhost:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy, 
API gateway, load balancer, content cache, or other features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional 
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

Mã thoát của curl: `0`. HTML có dòng `<h1>Welcome to nginx!</h1>` đúng yêu cầu.

Kiểm tra thêm mã trạng thái HTTP (NUL là thiết bị bỏ đầu ra trên Windows):

```text
PS> curl.exe -s -o NUL -w '%{http_code}' http://localhost:8080
200
```

## 3. Dừng và xóa container

Chạy lần lượt hai lệnh; chỉ xóa khi lệnh dừng thành công:

```text
PS> docker stop my-web
my-web
PS> docker rm my-web
my-web
```

Cả hai lệnh trả về mã thoát `0`.

Xác minh container đã được xóa bằng danh sách bao gồm cả container đã dừng:

```text
PS> docker ps -a --filter name=^/my-web$
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

Danh sách chỉ còn tiêu đề, không còn container `my-web`.

## 4. Kết quả

| Yêu cầu | Kết quả thực tế |
| --- | --- |
| Image `nginx:alpine` | Đã sử dụng |
| Container tên `my-web`, chạy `-d` | Đã khởi chạy thành công |
| Port mapping `8080:80` | Docker hiển thị cổng host 8080 chuyển tiếp tới cổng container 80 |
| Truy vấn bằng curl | HTTP 200, HTML chứa `Welcome to nginx!` |
| Dừng và xóa sau thử nghiệm | Thành công; xác minh không còn `my-web` |

**Kết luận:** Hoàn thành chạy Nginx ở chế độ ngầm, kiểm tra port mapping và dọn dẹp container.