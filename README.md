# Лабораторная работа №11

**Студент:** Сурков Всеволод Сергеевич  
**Группа:** 220032-11  
**Вариант:** №2

---

## Содержание

1. [Задание 2 — Контейнер Go](#задание-2--контейнер-go)
2. [Задание 4 — Сравнение размеров образов](#задание-4--сравнение-размеров-образов)
3. [Задание 6 — Настройка сети между контейнерами](#задание-6--настройка-сети-между-контейнерами)

---

## Задание 2 — Контейнер Go

### Сборка и запуск

```bash
cd go-app
docker build -t go-app .
docker run -d -p 8080:8080 --name go-app go-app
```

### Проверка работоспособности

```bash
curl http://localhost:8080/ping
```

**Ожидаемый вывод:**
```json
{"message":"pong"}
```

---

## Задание 4 — Сравнение размеров образов

### Сборка всех образов

```bash
# Go-приложение
cd go-app
docker build -t go-app .

# Python-приложение
cd ../python-app
docker build -t python-app .

# Rust-приложение
cd ../rust-app
docker build -t rust-app .
```

### Просмотр размеров образов

```bash
docker images
```

### Результаты сравнения

| Образ | Размер на диске | Размер контента |
|-------|----------------|----------------|
| rust-app | 21.5 MB | 5.79 MB |
| go-app | 45.4 MB | 14.7 MB |
| python-app | 211 MB | 51.5 MB |

**Выводы:**
- Наиболее компактный образ — **Rust** (multi-stage build + компилируемый язык)
- Средний размер — **Go** (multi-stage build)
- Наибольший размер — **Python** (требуется интерпретатор Python)

---

## Задание 6 — Настройка сети между контейнерами

### Создание сети

```bash
docker network create my-network
```

### Запуск контейнеров в сети

```bash
docker stop go-app python-app rust-app 2>/dev/null
docker rm go-app python-app rust-app 2>/dev/null

docker run -d --name go-app --network my-network -p 8080:8080 go-app
docker run -d --name python-app --network my-network -p 8000:8000 python-app
docker run -d --name rust-app --network my-network -p 8081:8080 rust-app
```

### Проверка работы сети

#### Установка curl в контейнерах

```bash
docker exec go-app apk add curl
```

#### Тестирование связи между контейнерами

```bash
# go-app -> python-app
docker exec go-app curl http://python-app:8000/

# go-app -> rust-app
docker exec go-app curl http://rust-app:8080/
```

#### Просмотр информации о сети

```bash
docker network inspect my-network
```

### Результаты

Контейнеры получили IP-адреса в сети `my-network`:

| Контейнер | IP-адрес |
|-----------|----------|
| go-app | 172.18.0.2 |
| python-app | 172.18.0.3 |
| rust-app | 172.18.0.4 |

**Контейнеры успешно общаются между собой по именам.**

---

## Структура проекта

```
.
├── go-app/                 Go-приложение и его Dockerfile (задание 2)
├── python-app/             Python-приложение и его Dockerfile (задание 4)
├── rust-app/               Rust-приложение и его Dockerfile (задание 4)
└── PROMPT_LOG.md           лог промптов
```

---

## Команды для очистки

```bash
# Остановка и удаление контейнеров
docker stop go-app python-app rust-app
docker rm go-app python-app rust-app

# Удаление сети
docker network rm my-network

# Удаление образов
docker rmi go-app python-app rust-app
```

---

## Лог промптов

Все промпты, по которым писался код, зафиксированы в [PROMPT_LOG.md](PROMPT_LOG.md).
