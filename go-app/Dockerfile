# Этап сборки (builder)
FROM golang:1.26-alpine AS builder

WORKDIR /app

# Копируем файлы модулей и загружаем зависимости
COPY go.mod go.sum ./
RUN go mod download

# Копируем исходный код
COPY . .

# Собираем статически слинкованный бинарник
RUN CGO_ENABLED=0 GOOS=linux go build -o server .

# Финальный этап — минимальный образ
FROM alpine:3.19

# Добавляем сертификаты для HTTPS-запросов (если нужны)
RUN apk --no-cache add ca-certificates

WORKDIR /root/

# Копируем собранный бинарник из builder-этапа
COPY --from=builder /app/server .

EXPOSE 8080

CMD ["./server"]