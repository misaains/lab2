
# Лабораторная 2. Технологии и методы программирования

### Быстрый старт

Ниже приведен скрипт для настройки хостов, копирования стартовых файлов и запуска контейнеров:

```bash
# 1) hosts
127.0.0.1 api.localhost traefik.localhost adminer.localhost
# 2) старт
cp -r materials/starter/* . && cp .env.example .env
sed -n '1,6p' .env # убедитесь, что DSN содержит буквальный пароль
# 3) запуск и первичная проверка
docker compose up -d --build
curl -s [http://api.localhost/healthz](http://api.localhost/healthz)  # ожидаем {"status":"ok"}
# 4) связность к БД/кэшу
curl -s [http://api.localhost/db](http://api.localhost/db)       # ожидаем {"db":1}
curl -s [http://api.localhost/cache](http://api.localhost/cache)    # ожидаем {"cache":"ok"}
# Если что-то не так – перейдите к разделу «Траблшутинг» ниже
````

В файле `/etc/hosts` прописаны необходимые локальные домены:

<img width="974" height="78" alt="image" src="https://github.com/user-attachments/assets/ed1208f1-692d-4b88-abb1-cd7383a05465" />


-----

### Подготовка файлов и окружения

Файлы были скопированы в рабочую директорию. `ls -la` демонстрирует наличие `.env`, `docker-compose.yml`, `Dockerfile` и других файлов проекта:

Конфигурация файла `.env` (вывод команды `sed -n '1,6p' .env`):

<img width="974" height="182" alt="image" src="https://github.com/user-attachments/assets/e8c488e5-d887-4449-9ce4-ec5c1328a927" />


-----

### Запуск и сборка

Выполнена команда запуска проверки Docker:

```bash
docker compose up -d --build
```
<img width="974" height="232" alt="image" src="https://github.com/user-attachments/assets/2d45d11f-0436-4958-986a-ef4ec459c799" />

В процессе сборки возникло предупреждение о том, что атрибут `version` в `docker-compose.yml` устарел (`obsolete`), но сборка продолжилась успешно.

**Результат сборки:**
Проект успешно собрался. Все контейнеры (`elite-stack-api`, `worker`, `rabbitmq`, `redis`, `postgres`, `traefik`, `adminer`) перешли в статус `Healthy` или `Started`.
<img width="974" height="326" alt="image" src="https://github.com/user-attachments/assets/a3a45f28-1f18-4a63-81ed-36d50289a8db" />

-----

### Проверка работоспособности

**1. Проверка через cURL (терминал)**
Запросы к API вернули корректные ответы, подтверждающие связь с БД и кэшем:
<img width="911" height="118" alt="image" src="https://github.com/user-attachments/assets/93259260-8a77-41a0-8bee-9a2baa5ba7f7" />

  * `curl -s http://api.localhost/healthz` -\> `{"status":"ok"}`
  * `curl -s http://api.localhost/db` -\> `{"db":1}`
  * `curl -s http://api.localhost/cache` -\> `{"cache":"ok"}`

**2. Проверка Adminer**
Интерфейс Adminer доступен. Подключение к базе данных `appdb` (схема `public`) выполнено успешно. Таблиц в базе пока нет. Всё работает.
<img width="974" height="408" alt="image" src="https://github.com/user-attachments/assets/18940843-5ed3-4521-ab3c-439bcee42690" />

**3. Проверка Traefik**
Панель управления Traefik доступна. Роутеры и сервисы отображаются корректно (4 роутера, 7 сервисов), ошибок нет. Статус: работает.
<img width="974" height="499" alt="image" src="https://github.com/user-attachments/assets/239bc4c5-1016-4242-97bf-b034bf7ef0fa" />

**4. Проверка API через браузер**
При переходе по адресу `http://api.localhost/db` браузер корректно отображает JSON-ответ:
<img width="483" height="245" alt="image" src="https://github.com/user-attachments/assets/3251c4dc-f2ef-4c46-af28-000a30c504d5" />


```
```
