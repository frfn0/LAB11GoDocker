# Этап сборки
FROM rust:1.88-alpine AS builder

RUN apk add --no-cache build-base pkgconfig openssl-dev

WORKDIR /app

COPY Cargo.toml ./
COPY src ./src
RUN cargo build --release

# Финальный образ
FROM alpine:3.19

RUN apk --no-cache add ca-certificates

COPY --from=builder /app/target/release/rust-server /usr/local/bin/rust-server

EXPOSE 8080

CMD ["rust-server"]
