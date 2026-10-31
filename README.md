# Bài kiểm tra Điện toán đám mây — Phạm Văn Tuyền

Website HTML/CSS tĩnh, chạy trực tiếp bằng **Nginx trên Ubuntu Server**. Không dùng JavaScript, Node.js, cơ sở dữ liệu, thư viện CDN hoặc Internet khi xem trang. Giao diện gồm phần giới thiệu môn Điện toán đám mây và thông tin sinh viên; không có trắc nghiệm hoặc sơ đồ mạng.

**Sinh viên:** Phạm Văn Tuyền  
**Lớp:** K9.CT1A  
**Mã sinh viên:** 23DH201046

## 1. Đưa dự án lên GitHub

1. Tạo repository tên `kiem-tra-dien-toan-dam-may` trên tài khoản GitHub của bạn. Để Ubuntu S1 clone không cần xác thực, có thể để repository ở chế độ Public.
2. Tải hai file `index.html` và `README.md` vào repository qua nút **Add file → Upload files**, hoặc dùng Git trên máy Windows:

   ```powershell
   git init
   git add index.html README.md
   git commit -m "Create cloud computing exam website"
   git branch -M main
   git remote add origin https://github.com/TEN_GITHUB/kiem-tra-dien-toan-dam-may.git
   git push -u origin main
   ```

   Thay `TEN_GITHUB` bằng tên tài khoản GitHub của bạn. Nếu dùng lệnh Git, hãy mở PowerShell ngay trong thư mục chứa hai file.

## 2. Cài Nginx và Git trên máy ảo Ubuntu S1

Tại Terminal máy S1 hoặc kết nối SSH đến S1:

```bash
sudo apt update
sudo apt install -y nginx git
sudo systemctl enable --now nginx
sudo systemctl status nginx --no-pager
```

## 3. Lấy website từ GitHub về S1

```bash
cd /tmp
git clone https://github.com/TEN_GITHUB/kiem-tra-dien-toan-dam-may.git
sudo cp /tmp/kiem-tra-dien-toan-dam-may/index.html /var/www/html/index.html
sudo nginx -t
sudo systemctl reload nginx
```

> Nếu `/tmp/kiem-tra-dien-toan-dam-may` đã tồn tại, vào thư mục đó và chạy `git pull`, sau đó chạy lại hai lệnh `sudo cp ...` và `sudo systemctl reload nginx`.

## 4. Kiểm tra đúng mô hình ESXi đã thực hành

| Máy | Vai trò | IP / Cổng |
| --- | --- | --- |
| S1 | Ubuntu Web server chạy Nginx | `192.168.50.10:80` |
| S2 | Ubuntu Router / NAT | LAN `192.168.50.1`, WAN nhận bằng DHCP |
| Windows | Máy khách | `http://IP_WAN_S2:8000` |

Trên **S1**:

```bash
curl -I http://localhost
curl -I http://192.168.50.10
```

Trên **S2**:

```bash
curl -I http://192.168.50.10
ip -4 addr
```

Trên **Windows**, sau khi router S2 đã bật IP forwarding, NAT và DNAT cổng 8000 về `192.168.50.10:80`, mở trình duyệt:

```text
http://IP_WAN_S2:8000
```

Ví dụ nếu WAN S2 vẫn là `192.168.254.137`, địa chỉ là `http://192.168.254.137:8000`. Vì S2 nhận DHCP nên cần kiểm tra IP thực tế, không mặc định IP ví dụ luôn đúng.

## 5. Cập nhật sau khi sửa website trên GitHub

Trên S1:

```bash
cd /tmp/kiem-tra-dien-toan-dam-may
git pull --ff-only
sudo cp index.html /var/www/html/index.html
sudo nginx -t
sudo systemctl reload nginx
```

## Lưu ý

- Nội dung lệnh Nginx trong phần minh họa Terminal chỉ để giới thiệu, **không phải kiểm tra trạng thái máy chủ theo thời gian thực**.
- Trang chỉ có một file chạy chính: `index.html`; có thể sao chép trực tiếp vào `/var/www/html/` mà không cần Git nếu muốn chạy thử nhanh.
