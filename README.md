Уровень 4

Команды:
bash
docker build -t counter-app .
docker run -d --name my-app -p 8000:8000 -e GREETING="Привет" counter-app

Логи:
text
CONTAINER ID   IMAGE         COMMAND           CREATED         STATUS         PORTS                                         NAMES
efd2fb01b7a6   counter-app   "python app.py"   3 minutes ago   Up 3 minutes   0.0.0.0:8000->8000/tcp, [::]:8000->8000/tcp   my-app
2026-11-18 19:52:45,496 listening port=8000 storage=sqlite
2026-11-18 19:53:07,910 http 192.168.65.1 "GET / HTTP/1.1" 200 -
2026-11-18 19:53:08,390 http 192.168.65.1 "GET /favicon.ico HTTP/1.1" 404 -
2026-11-18 19:54:11,735 http 192.168.65.1 "POST /increment HTTP/1.1" 200 -
2026-11-18 19:54:14,154 http 192.168.65.1 "GET / HTTP/1.1" 200 -

Уровень 6
Переменные APP_PORT и GREETING настраиваются через .env.
Подключен именованный volume app-data для сохранения счётчика.

Счётчик сохраняется после удаления и повторного создания контейнера.
До docker compose down:

json
{"greeting": "Привет1", "count": 2, "hostname": "e59a345debc4", "storage": "sqlite"}

После docker compose down и docker compose up -d:

json
{"greeting": "Привет1", "count": 2, "hostname": "dab3eb191e54", "storage": "sqlite"}
Счётчик сохранился (2 -> 2), hostname изменился, что подтверждает пересоздание контейнера.

Уровень 8

Настройка:
В compose.yaml работают два сервиса: app и redis. Данные Redis сохраняются в volume redis-data.

Проверки:

Проверка работы через Redis (исходный счётчик 0, после двух POST /increment равен 2):
json
{"greeting": "Привет", "count": 0, "hostname": "5b35d17b3a1a", "storage": "redis"}
{"greeting": "Привет", "count": 2, "hostname": "5b35d17b3a1a", "storage": "redis"}
Остановка Redis:
text
healthz: 200
readyz: 503
increment: 503
Запуск Redis обратно:
json
{"greeting": "Привет", "count": 2, "hostname": "5b35d17b3a1a", "storage": "redis"}
Следующий POST /increment без ручного перезапуска приложения:

json
{"greeting": "Привет", "count": 3, "hostname": "5b35d17b3a1a", "storage": "redis"}
