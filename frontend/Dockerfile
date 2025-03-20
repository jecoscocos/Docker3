FROM node:16

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

# Добавляем переменную окружения для API URL
ENV REACT_APP_API_URL=http://todo-backend:8000

RUN npm run build

EXPOSE 3000

# Добавляем переменную окружения для работы с hot-reload
ENV WATCHPACK_POLLING=true

# Запускаем в development режиме
CMD ["npm", "start"] 