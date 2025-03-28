# Đóng gói frontend

# Bước 1: Sử dụng Node.js image làm base
```FROM node:20-alpine AS build```

# Bước 2: Đặt thư mục làm việc trong container
```WORKDIR /app```

# Bước 3: Copy file package.json và package-lock.json để cài dependencies
```COPY package*.json ./```

# Bước 4: Cài đặt dependencies
```RUN npm install```

# Bước 5: Copy toàn bộ mã nguồn vào container
```COPY . .```

# Bước 6: Truyền biến môi trường API URL từ build argument
```ARG REACT_APP_API_URL```
```ENV REACT_APP_API_URL=${REACT_APP_API_URL}```

# Bước 7: Đặt PUBLIC_URL rỗng để build vào root path
```ENV PUBLIC_URL=""```

# Bước 8: Build ứng dụng React
```RUN npm run build```

# Bước 9: Mở cổng 3000 (tuỳ chọn)
```EXPOSE 3000```

# Bước 10: Khởi động app (nếu chạy dev server)
```CMD ["npm", "start"]```

---
# Giai thich cu phap
- **`FROM node:20-alpine AS build`**:
  - Sử dụng Docker image chính thức **Node.js 20 (bản Alpine)** để tạo môi trường build nhẹ, tối ưu.
  - Từ khóa `AS build` đặt tên cho giai đoạn này là `build`, giúp dễ quản lý khi dùng multi-stage.

- **`WORKDIR /app`**:
  - Thiết lập **thư mục làm việc** trong container là `/app`, mọi thao tác sau đó sẽ thực hiện tại đây.

- **`COPY package*.json ./`**:
  - Copy các file `package.json` và `package-lock.json` vào container.
  - Giúp tận dụng cache khi cài đặt dependency, tránh phải cài lại nếu code không đổi.

- **`RUN npm install`**:
  - Cài đặt tất cả thư viện được định nghĩa trong `package.json` bằng npm.

- **`COPY . .`**:
  - Copy toàn bộ mã nguồn từ máy host vào container tại thư mục `/app`.

- **`ARG REACT_APP_API_URL`**:
  - Khai báo một biến nhận giá trị từ bên ngoài khi build Docker image (`--build-arg`).

- **`ENV REACT_APP_API_URL=${REACT_APP_API_URL}`**:
  - Thiết lập biến môi trường nội bộ trong container, dùng cho ứng dụng React trong lúc build.

- **`ENV PUBLIC_URL=""`**:
  - Đặt PUBLIC_URL rỗng để React build app vào root path (`/`) thay vì subpath.

- **`RUN npm run build`**:
  - Build ứng dụng React, kết quả nằm trong thư mục `/app/build`, sẵn sàng để deploy.

- **`EXPOSE 3000`**:
  - Mở cổng 3000 cho container, phục vụ trong trường hợp dùng `npm start` hoặc chạy server dev.

- **`CMD ["npm", "start"]`**:
  - Thiết lập lệnh mặc định khi container khởi động: chạy ứng dụng bằng `npm start`.

---

## Bước 2: Xây dựng Docker image
```
docker build -t frontend-app .
```

- **`-t frontend-app`**: Đặt tên cho Docker image là **`frontend-app`**.
- **`.`**: Chỉ định thư mục hiện tại làm **context** cho Docker để tìm Dockerfile và các file cần thiết để build image.

---

## Bước 3: Chạy container cho frontend
```
docker run -d --name frontend-app -p 3000:3000 frontend-app
```

- **`-d`** (detach - tách riêng): Khiến Docker chạy container ở chế độ nền => không bị giữ lại trong terminal khi container đang chạy, có thể tiếp tục sử dụng terminal cho các lệnh khác mà không bị gián đoạn.
  - Nếu không sử dụng `-d`, Docker sẽ chạy container trong chế độ foreground (trong terminal) => sẽ thấy các log trực tiếp từ container, và terminal sẽ bị "khóa" cho đến khi container dừng lại.
  
- **`--name frontend-app`**: Đặt tên cho container là **`frontend-app`**.
  
- **`-p 3000:3000`**: (`--publish`), ánh xạ cổng từ máy chủ vào container.
  - **`3000:3000`**: Cấu trúc này chỉ định rằng cổng 3000 của máy chủ (your computer) sẽ được ánh xạ với cổng 3000 của container.
  - Điều này có nghĩa khi truy cập `http://localhost:3000` trên trình duyệt, bạn sẽ được chuyển hướng đến ứng dụng frontend đang chạy trong container.
  - Cổng 3000 là cổng mặc định mà nhiều ứng dụng React sử dụng, vì vậy khi xây dựng và chạy ứng dụng React, Docker sẽ mở cổng 3000 trong container để có thể truy cập ứng dụng.
  
- **`frontend-app`**: Tên của Docker image mà bạn đã xây dựng trước đó. Lệnh này sẽ chạy container từ image `frontend-app`.
  - Khi Docker thấy tên image là **`frontend-app`**, nó sẽ tìm kiếm trong các image đã có trên máy và tạo container mới từ image đó.

Sau khi container frontend được chạy, bạn có thể truy cập vào frontend qua `http://localhost:3000`.











