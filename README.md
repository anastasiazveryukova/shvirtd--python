# Домашнее задание к занятию 5. «Практическое применение Docker»

### Инструкция к выполнению

1. Для выполнения заданий обязательно ознакомьтесь с [инструкцией](https://github.com/netology-code/devops-materials/blob/master/cloudwork.MD) по экономии облачных ресурсов. Это нужно, чтобы не расходовать средства, полученные в результате использования промокода.
3. **Своё решение к задачам оформите в вашем GitHub репозитории.**
4. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.
5. Сопроводите ответ необходимыми скриншотами.

---
## Примечание: Ознакомьтесь со схемой виртуального стенда [по ссылке](https://github.com/netology-code/shvirtd-example-python/blob/main/schema.pdf)

---

## Задача 0
1. Убедитесь что у вас НЕ(!) установлен ```docker-compose```, для этого получите следующую ошибку от команды ```docker-compose --version```
```
Command 'docker-compose' not found, but can be installed with:

sudo snap install docker          # version 24.0.5, or
sudo apt  install docker-compose  # version 1.25.0-1

See 'snap info docker' for additional versions.
```
В случае наличия установленного в системе ```docker-compose``` - удалите его.  
2. Убедитесь что у вас УСТАНОВЛЕН ```docker compose```(без тире) версии не менее v2.24.X, для это выполните команду ```docker compose version```  
###  **Своё решение к задачам оформите в вашем GitHub репозитории!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!**

---

## Задача 1
1. Сделайте в своем GitHub пространстве fork [репозитория](https://github.com/netology-code/shvirtd-example-python).

2. Создайте файл ```Dockerfile.python``` на основе существующего `Dockerfile`:
   - Используйте базовый образ ```python:3.12-slim```
   - Обязательно используйте конструкцию ```COPY . .``` в Dockerfile
   - Создайте `.dockerignore` файл для исключения ненужных файлов
   - Используйте ```CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5000"]``` для запуска
   - Протестируйте корректность сборки
2.1 Используйте multistage сборку вместо single stage.
3. (Необязательная часть, *) Изучите инструкцию в проекте и запустите web-приложение без использования docker, с помощью venv. (Mysql БД можно запустить в docker run).
4. (Необязательная часть, *) Изучите код приложения и добавьте управление названием таблицы через ENV переменную.

<img width="752" height="638" alt="Снимок экрана 2026-07-07 114736" src="https://github.com/user-attachments/assets/27d3c387-f181-4bcc-89c3-4989869678fd" />


---
### ВНИМАНИЕ!
!!! В процессе последующего выполнения ДЗ НЕ изменяйте содержимое файлов в fork-репозитории! Ваша задача ДОБАВИТЬ 5 файлов: ```Dockerfile.python```, ```compose.yaml```, ```.gitignore```, ```.dockerignore```,```bash-скрипт```. Если вам понадобилось внести иные изменения в проект - вы что-то делаете неверно!
---

## Задача 2 (*)
1. Создайте в yandex cloud container registry с именем "test" с помощью "yc tool" . [Инструкция](https://cloud.yandex.ru/ru/docs/container-registry/quickstart/?from=int-console-help)
2. Настройте аутентификацию вашего локального docker в yandex container registry.
3. Соберите и залейте в него образ с python приложением из задания №1.
4. Просканируйте образ на уязвимости.
5. В качестве ответа приложите отчет сканирования.

<img width="750" height="485" alt="Снимок экрана 2026-07-07 130153" src="https://github.com/user-attachments/assets/c102a566-f1ed-4310-b08b-30b46f7744b4" />

<img width="747" height="307" alt="Снимок экрана 2026-07-07 130023" src="https://github.com/user-attachments/assets/7e301b93-2174-48d5-b82b-8ce592e576c4" />

<img width="1920" height="1018" alt="Снимок экрана 2026-07-07 125843" src="https://github.com/user-attachments/assets/fd82fcdf-b393-4d29-bc09-4d5130535ef3" />

<img width="1920" height="399" alt="Снимок экрана 2026-07-07 130225" src="https://github.com/user-attachments/assets/d7a11a2c-3429-4cda-a61f-f8ff86f094a1" />

## Задача 3
1. Изучите файл "proxy.yaml"
2. Создайте в репозитории с проектом файл ```compose.yaml```. С помощью директивы "include" подключите к нему файл "proxy.yaml".
3. Опишите в файле ```compose.yaml``` следующие сервисы: 

- ```web```. Образ приложения должен ИЛИ собираться при запуске compose из файла ```Dockerfile.python``` ИЛИ скачиваться из yandex cloud container registry(из задание №2 со *). Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.5```. Сервис должен всегда перезапускаться в случае ошибок.
Передайте необходимые ENV-переменные для подключения к Mysql базе данных по сетевому имени сервиса ```web``` 

- ```db```. image=mysql:8. Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.10```. Явно перезапуск сервиса в случае ошибок. Передайте необходимые ENV-переменные для создания: пароля root пользователя, создания базы данных, пользователя и пароля для web-приложения.Обязательно используйте уже существующий .env file для назначения секретных ENV-переменных!

2. Запустите проект локально с помощью docker compose , добейтесь его стабильной работы: команда ```curl -L http://127.0.0.1:8090``` должна возвращать в качестве ответа время и локальный IP-адрес. Если сервисы не стартуют воспользуйтесь командами: ```docker ps -a ``` и ```docker logs <container_name>``` . Если вместо IP-адреса вы получаете информационную ошибку --убедитесь, что вы шлете запрос на порт ```8090```, а не 5000.

5. Подключитесь к БД mysql с помощью команды ```docker exec -ti <имя_контейнера> mysql -uroot -p<пароль root-пользователя>```(обратите внимание что между ключем -u и логином root нет пробела. это важно!!! тоже самое с паролем) . Введите последовательно команды (не забываем в конце символ ; ): ```show databases; use <имя вашей базы данных(по-умолчанию virtd, как это указано в .env)>; show tables; SELECT * from requests LIMIT 10;```. Примечание: таблица в БД создается после первого поступившего запроса к приложению.

6. Остановите проект. В качестве ответа приложите скриншот sql-запроса.

Создаем compose.yaml
```
# Убираем устаревшую версию 3.7
include:
  - proxy.yaml

volumes:
  db_mysql:

services:

  db:
    image: mysql:8
    restart: on-failure
    env_file:
      - .env
    volumes:
      - db_mysql:/var/lib/mysql
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    ports:
      - 3306:3306
    networks:
      backend:
        ipv4_address: 172.20.0.10
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 5s
      retries: 5

  web:
    build:
          dockerfile: Dockerfile.python
    restart: on-failure
    environment:
      DB_HOST: db
      DB_TABLE: requests
      DB_PORT: 3306
      DB_NAME: ${MYSQL_DATABASE}
      DB_USER: ${MYSQL_USER}
      DB_PASSWORD: ${MYSQL_PASSWORD}
    depends_on:
        db:
          condition: service_healthy
    networks:
      backend:
        ipv4_address: 172.20.0.5
    
```

<img width="759" height="621" alt="Снимок экрана 2026-07-07 135308" src="https://github.com/user-attachments/assets/fe2b88a0-7bd4-415f-8353-54b9b8cfe447" />

<img width="1341" height="964" alt="Снимок экрана 2026-07-07 144737" src="https://github.com/user-attachments/assets/5219ba2c-3fde-44d3-bb5a-13dbe41408dd" />


## Задача 4
1. Запустите в Yandex Cloud ВМ (вам хватит 2 Гб Ram).
2. Подключитесь к Вм по ssh и установите docker.
3. Напишите bash-скрипт, который скачает ваш fork-репозиторий в каталог /opt и запустит проект целиком.
4. Зайдите на сайт проверки http подключений, например(или аналогичный): ```https://check-host.net/check-http``` и запустите проверку вашего сервиса ```http://<внешний_IP-адрес_вашей_ВМ>:8090```. Таким образом трафик будет направлен в ingress-proxy. Трафик должен пройти через цепочки: Пользователь → Internet → Nginx → HAProxy → FastAPI(запись в БД) → HAProxy → Nginx → Internet → Пользователь
5. (Необязательная часть) Дополнительно настройте remote ssh context к вашему серверу. Отобразите список контекстов и результат удаленного выполнения ```docker ps -a```
6. Повторите SQL-запрос на сервере и приложите скриншот и ссылку на fork.

Создаем простой bash.sh 

```
#!/bin/bash
echo "Cloning the project from GitHub"
  git clone https://github.com/anastasiazveryukova/shvirtd-example-python.git
echo "Done"

echo "Entering the project directory"
  cd shvirtd-example-python
echo "Done"

echo "Creating docker containers: db, app, proxy and nginx"
  sudo docker compose up -d
echo "Done"

echo "List of containers"
  sudo docker ps
```
<img width="1398" height="1009" alt="Снимок экрана 2026-07-07 164040" src="https://github.com/user-attachments/assets/41c292ad-4b59-4187-88c9-9552242c97a1" />

<img width="1920" height="1092" alt="Снимок экрана 2026-07-07 164232" src="https://github.com/user-attachments/assets/8d64a2a2-990a-4382-9994-c85f5899463a" />


<img width="1920" height="1083" alt="Снимок экрана 2026-07-07 164325" src="https://github.com/user-attachments/assets/cd0649d8-e885-4498-8d0c-0b168b2f3889" />

<img width="1920" height="1089" alt="Снимок экрана 2026-07-07 171229" src="https://github.com/user-attachments/assets/a9cb5675-a02d-498c-9b1c-396c5d0533f1" />

<img width="1363" height="724" alt="Снимок экрана 2026-07-07 165049" src="https://github.com/user-attachments/assets/98c157d0-d30b-4aa8-b256-f98b69a0abf3" />

Cсылка на fork

https://github.com/anastasiazveryukova/shvirtd-example-python.git

## Задача 5 (*)
1. Напишите и задеплойте на вашу облачную ВМ bash скрипт, который произведет резервное копирование БД mysql в директорию "/opt/backup" с помощью запуска в сети "backend" контейнера из образа ```schnitzler/mysqldump``` при помощи ```docker run ...``` команды. Подсказка: "документация образа."
2. Протестируйте ручной запуск
3. Настройте выполнение скрипта раз в 1 минуту через cron, crontab или systemctl timer. Придумайте способ не светить логин/пароль в git!!
4. Предоставьте скрипт, cron-task и скриншот с несколькими резервными копиями в "/opt/backup"

## Задача 6
Скачайте docker образ ```hashicorp/terraform:latest``` и скопируйте бинарный файл ```/bin/terraform``` на свою локальную машину, используя dive и docker save.
Предоставьте скриншоты  действий .

Скачала образ 
```
docker pull hashicorp/terraform:latest
```

Запустила интерактивную оболочку Dive с доступом к Docker socket, чтобы можно было анализировать слои образа:
```
docker run --rm -it -v /var/run/docker.sock:/var/run/docker.sock wagoodman/dive:latest hashicorp/terraform:latest

```
<img width="1653" height="898" alt="Снимок экрана 2026-07-10 144912" src="https://github.com/user-attachments/assets/809a9c13-dc6d-485f-b39c-fd55ac6ba9e0" />

Сохранила образ в архив

```
docker save hashicorp/terraform:latest -o terraform.tar

```

Распаковала

```
tar -xf terraform.tar

```

При проверке файла с номером хэша, который соответствует /bin/terraform не оказалось, пробовала несколько раз. 

<img width="1429" height="532" alt="Снимок экрана 2026-07-10 145125" src="https://github.com/user-attachments/assets/165f8270-d9e6-4e26-aecf-c10d3dd05c47" />

При попытке извлечь бы бинарный файл по хэшу выдает ошибку,что файл не найден.

```
tar -xf "temp_extract/blobs/sha256/597a09a255459032bc4f70f74566919bf7c4fa81bbc511740678d3f1b86d61d8" -C ./ bin/terraform 
```

Но если бы все получилось, наделила бы правами

```
chmod +x ./bin/terraform 
```

Альтернативный вариант с cp

<img width="1357" height="469" alt="Снимок экрана 2026-07-08 153254" src="https://github.com/user-attachments/assets/63160a08-8812-4156-bf06-143e185f5477" />

