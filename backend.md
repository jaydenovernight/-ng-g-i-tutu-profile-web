- **`FROM golang:1.23.2`**:
  - Sử dụng Docker image chính thức của Golang phiên bản 1.23.2 làm môi trường base.

- **`WORKDIR /app`**:
  - Thiết lập thư mục làm việc trong container là `/app`.
  - Mọi lệnh sau đó như copy, build sẽ được thực hiện tại đây.

- **`COPY go.mod go.sum ./`**:
  - Copy 2 file quản lý module của Go vào container.
  - Giúp Docker cache `go mod download` nếu không có thay đổi.

- **`RUN go mod download`**:
  - Tải toàn bộ các dependencies được khai báo trong `go.mod` về.

- **`COPY . .`**:
  - Copy toàn bộ mã nguồn từ thư mục hiện tại trên host vào container.

- **`RUN go build -o main .`**:
  - Build ứng dụng Go và xuất ra file nhị phân tên là `main`.

- **`EXPOSE 8080`**:
  - Mở cổng 8080 – nơi backend sẽ lắng nghe và phục vụ request.

- **`CMD ["./main"]`**:
  - Khi container khởi động, chạy file nhị phân `main` vừa build ở trên.
