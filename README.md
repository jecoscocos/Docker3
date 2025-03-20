# Todo Application

Веб-приложение для управления задачами, построенное с использованием React (frontend), FastAPI (backend) и PostgreSQL (database).

## Структура проекта

```
PRlab3/
├── frontend/          # React приложение
│   ├── src/          # Исходный код
│   ├── public/       # Статические файлы
│   ├── package.json  # Зависимости и скрипты
│   └── Dockerfile    # Конфигурация Docker для frontend
├── backend/          # FastAPI приложение
│   ├── src/         # Исходный код
│   ├── requirements.txt  # Python зависимости
│   └── Dockerfile   # Конфигурация Docker для backend
└── database/        # PostgreSQL
    ├── init.sql     # Скрипт инициализации БД
    └── Dockerfile   # Конфигурация Docker для БД
```

## Основные команды Docker

### Запуск контейнеров

```bash
# Сборка и запуск базы данных
cd database
docker build -t todo-db .
docker run -d -p 5432:5432 --name todo-db todo-db

# Сборка и запуск backend
cd ../backend
docker build -t todo-backend .
docker run -d -p 8000:8000 --name todo-backend --link todo-db:db todo-backend

# Сборка и запуск frontend
cd ../frontend
docker build -t todo-frontend .
docker run -d -p 3000:3000 --name todo-frontend todo-frontend
```

### Управление контейнерами

```bash
# Просмотр запущенных контейнеров
docker ps

# Просмотр всех контейнеров (включая остановленные)
docker ps -a

# Остановка контейнеров
docker stop todo-db todo-backend todo-frontend

# Запуск остановленных контейнеров
docker start todo-db todo-backend todo-frontend

# Удаление контейнеров
docker rm todo-db todo-backend todo-frontend

# Принудительное удаление контейнера
docker rm -f todo-db
```

### Управление образами

```bash
# Просмотр всех образов
docker images

# Удаление образа
docker rmi todo-db todo-backend todo-frontend

# Принудительное удаление образа
docker rmi -f todo-db
```

### Сети Docker

```bash
# Просмотр всех сетей
docker network ls

# Создание новой сети
docker network create todo-network

# Подключение контейнера к сети
docker network connect todo-network todo-db
```

### Логи и отладка

```bash
# Просмотр логов контейнера
docker logs todo-frontend
docker logs todo-backend
docker logs todo-db

# Просмотр логов в реальном времени
docker logs -f todo-frontend

# Просмотр последних 100 строк логов
docker logs --tail 100 todo-frontend
```

## Dockerfile

### Frontend (frontend/Dockerfile)
```dockerfile
FROM node:16

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

ENV REACT_APP_API_URL=http://todo-backend:8000

RUN npm run build

EXPOSE 3000

ENV WATCHPACK_POLLING=true

CMD ["npm", "start"]
```

### Backend (backend/Dockerfile)
```dockerfile
FROM python:3.9

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY src/ .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Database (database/Dockerfile)
```dockerfile
FROM postgres:15

COPY init.sql /docker-entrypoint-initdb.d/

ENV POSTGRES_DB=tododb
ENV POSTGRES_USER=admin
ENV POSTGRES_PASSWORD=admin123

EXPOSE 5432
```

## Примеры использования

### Подключение к базе данных
```bash
# Подключение к PostgreSQL через Docker
docker exec -it todo-db psql -U admin -d tododb

# Основные команды PostgreSQL
\dt                    # показать все таблицы
\d имя_таблицы        # показать структуру таблицы
SELECT * FROM tasks;   # показать все задачи
\q                    # выйти из консоли
```

### Проверка работы приложения

1. Frontend: http://localhost:3000
2. Backend API: 
   - Swagger UI: http://localhost:8000/docs
   - ReDoc: http://localhost:8000/redoc
3. База данных:
   - Host: localhost
   - Port: 5432
   - Database: tododb
   - Username: admin
   - Password: admin123

## Полезные советы

1. Для полной перезагрузки проекта:
```bash
# Остановка и удаление всех контейнеров
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)

# Удаление всех образов
docker rmi $(docker images -q)

# Пересборка и запуск
# (выполните команды из раздела "Запуск контейнеров")
```

2. Для просмотра использования ресурсов:
```bash
docker stats
```

3. Для очистки неиспользуемых ресурсов:
```bash
docker system prune
``` 