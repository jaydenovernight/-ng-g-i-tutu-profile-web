# Đóng gói frontend

## Bước 1: Tạo Dockerfile cho frontend

```
# Bước 1: Sử dụng Node.js image làm base
FROM node:16

# Bước 2: Đặt thư mục làm việc trong container
WORKDIR /app

# Bước 3: Copy package.json và package-lock.json vào container
COPY package*.json ./

# Bước 4: Cài đặt dependencies (thư viện frontend)
RUN npm install

# Bước 5: Copy toàn bộ mã nguồn vào container
COPY . .

# Bước 6: Build ứng dụng frontend
RUN npm run build

# Bước 7: Mở port 3000 để truy cập ứng dụng frontend
EXPOSE 3000

# Bước 8: Chạy ứng dụng frontend
CMD ["npm", "start"]

```
- **`FROM node:16`**: 
  - Sử dụng Docker image chính thức **Node.js 16** làm môi trường base cho ứng dụng.
  
- **`WORKDIR /app`**:
  - Thiết lập **thư mục làm việc** trong container là `/app`, nghĩa là mọi thao tác tiếp theo sẽ được thực hiện trong thư mục này.

- **`COPY package*.json ./`**:
  - Sao chép **`package.json`** và **`package-lock.json`** vào thư mục làm việc trong container để chuẩn bị cài đặt **dependencies**.

- **`RUN npm install`**:
  - Chạy lệnh **`npm install`** để cài đặt các thư viện và dependencies từ **`package.json`** trong container.

- **`COPY . .`**:
  - Sao chép **toàn bộ mã nguồn** vào thư mục làm việc trong container.

- **`RUN npm run build`**:
  - Chạy lệnh **`npm run build`** để build ứng dụng frontend. Lệnh này sẽ tạo ra thư mục `build/` chứa các file static sẵn sàng cho việc triển khai.

- **`EXPOSE 3000`**:
  - Mở cổng **3000** trong container để có thể truy cập ứng dụng React khi container chạy.

- **`CMD ["npm", "start"]`**:
  - Khi container khởi động, lệnh này sẽ được chạy để bắt đầu ứng dụng frontend.


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











