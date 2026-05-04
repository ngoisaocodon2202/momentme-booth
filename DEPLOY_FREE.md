# Deploy Free Cho Momentme Booth

Project này là ASP.NET Core MVC + MySQL, nên không thể đưa nguyên app lên GitHub Pages/Netlify dạng static. Cách free dễ nhất hiện tại là:

- Web app: Render Free Web Service hoặc Koyeb Free Web Service.
- Database: Aiven Free MySQL.

Khuyến nghị nhanh: dùng Render + Aiven.

## 1. Tạo MySQL Free Trên Aiven

1. Vào https://aiven.io/free-mysql-database và tạo MySQL service free.
2. Mở service vừa tạo, lấy thông tin connection: host, port, user, password.
3. Dùng database mặc định `defaultdb`, hoặc tạo database riêng tên `photobooth`.
4. Chuỗi kết nối dùng cho app:

```text
Server=<AIVEN_HOST>;Port=<AIVEN_PORT>;Database=defaultdb;User=<AIVEN_USER>;Password=<AIVEN_PASSWORD>;TreatTinyAsBoolean=false;SslMode=Required;AllowPublicKeyRetrieval=True;
```

Nếu bạn tạo database tên `photobooth`, đổi `Database=defaultdb` thành `Database=photobooth`.

## 2. Đẩy Source Lên GitHub

Tạo repository mới rồi upload source project này lên GitHub.

Nên upload các file source như:

- `Controllers/`
- `Data/`
- `Hubs/`
- `Models/`
- `Services/`
- `ViewModels/`
- `Views/`
- `wwwroot/`
- `MomentmeBooth.csproj`
- `Program.cs`
- `appsettings.json`
- `Dockerfile`
- `.dockerignore`
- `render.yaml`

Không cần upload:

- `bin/`
- `obj/`
- `publish/`
- `.data-protection-keys/`
- `momentme-run*.log`

## 3. Deploy Web App Lên Render

1. Vào https://render.com.
2. Chọn `New` > `Web Service`.
3. Connect GitHub repo của project.
4. Chọn runtime/language là `Docker`.
5. Chọn instance type là `Free`.
6. Thêm environment variables:

```text
ASPNETCORE_ENVIRONMENT=Production
PORT=8080
ConnectionStrings__DefaultConnection=Server=<AIVEN_HOST>;Port=<AIVEN_PORT>;Database=defaultdb;User=<AIVEN_USER>;Password=<AIVEN_PASSWORD>;TreatTinyAsBoolean=false;SslMode=Required;AllowPublicKeyRetrieval=True;
Booking__PublicUrl=https://<ten-app-cua-ban>.onrender.com/
```

Nếu Render import `render.yaml`, các biến `PORT` và `ASPNETCORE_ENVIRONMENT` có thể đã được điền sẵn. Riêng `ConnectionStrings__DefaultConnection` phải tự nhập để không lộ mật khẩu database trong source code.

Sau deploy, mở:

```text
https://<ten-app-cua-ban>.onrender.com/
https://<ten-app-cua-ban>.onrender.com/momentme-admin
```

## Lưu Ý Khi Dùng Free

- Render Free sẽ ngủ sau một thời gian không có traffic, nên lần mở đầu tiên có thể chậm.
- File upload vào filesystem của app free có thể mất khi redeploy/restart/sleep. Nếu cần ảnh onboarding lâu dài, nên dùng ảnh bằng URL ngoài thay vì upload trực tiếp.
- Dữ liệu hàng chờ nằm trong Aiven MySQL nên vẫn giữ được miễn là database còn hoạt động.
- Trang admin hiện chưa có đăng nhập. Nếu public thật, nên thêm lớp bảo vệ trước khi dùng cho khách thật.

## Deploy Bằng Koyeb Thay Render

Nếu dùng Koyeb:

1. Tạo Web Service từ GitHub repo.
2. Chọn deploy bằng Dockerfile.
3. Chọn free instance.
4. Set environment variables giống Render:

```text
PORT=8080
ASPNETCORE_ENVIRONMENT=Production
ConnectionStrings__DefaultConnection=<chuoi-ket-noi-Aiven>
Booking__PublicUrl=https://<domain-koyeb-cua-ban>/
```

URL vẫn là:

```text
/
/momentme-admin
```
