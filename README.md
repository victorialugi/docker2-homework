# Домашнее задание к занятию "`Docker, часть 2`" - `Victoria Luginina`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1

`Docker Compose — это инструмент для управления много-контейнерными приложениями, который позволяет описать их конфигурацию в одном YAML-файле и запускать одной командой, упрощая настройку связей между сервисами, такими как веб-сервер и база данных. Он может улучшить мою жизнь, так как экономит время на настройке окружений для учёбы и проектов, автоматизируя запуск сложных приложений, что особенно полезно при изучении Docker и DevOps, позволяя сосредоточиться на задачах, а не на ручной конфигурации.`

---

### Задание 2

```yaml
version: '3.8'
services: {}
volumes: {}
networks:
  LugininaV-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16

---

### Задание 3

`Создана конфигурация `docker-compose` для Prometheus с именем контейнера `LugininaV-netology-prometheus`. Добавлены тома для данных (`prometheus-data`) и конфигурации (`prometheus.yml`), обеспечен внешний доступ к порту `9090`.`


```yaml
global:
  scrape_interval: 15s
scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']

```yaml
version: '3.8'
services:
  prometheus:
    image: prom/prometheus:latest
    container_name: LugininaV-netology-prometheus
    ports:
      - "9090:9090"
    volumes:
      - prometheus-data:/prometheus
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    networks:
      - LugininaV-my-netology-hw
volumes:
  prometheus-data:
networks:
  LugininaV-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16

---

### Задание 4

`Создана конфигурация `docker-compose` для Pushgateway с именем контейнера `LugininaV-netology-pushgateway`. Обеспечен внешний доступ к порту `9091`.`

```yaml
version: '3.8'
services:
  prometheus:
    image: prom/prometheus:latest
    container_name: LugininaV-netology-prometheus
    ports:
      - "9090:9090"
    volumes:
      - prometheus-data:/prometheus
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    networks:
      - LugininaV-my-netology-hw
  pushgateway:
    image: prom/pushgateway:latest
    container_name: LugininaV-netology-pushgateway
    ports:
      - "9091:9091"
    networks:
      - LugininaV-my-netology-hw
volumes:
  prometheus-data:
networks:
  LugininaV-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16

---

### Задание 5

Создана конфигурация `docker-compose` для Grafana с именем контейнера `LugininaV-netology-grafana`. Добавлены тома для данных (`grafana-data`) и конфигурации (`custom.ini`), настроена переменная окружения для пути к конфигурации. В `custom.ini` указаны логин `LugininaV` и пароль `netology`. Обеспечен внешний доступ к порту `3000` через порт `80`.

[auth]
disable_login_form = false

[auth.basic]
enabled = true

[auth.anonymous]
enabled = false

[security]
admin_user = LugininaV
admin_password = netology


```yaml
version: '3.8'
services:
  prometheus:
    image: prom/prometheus:latest
    container_name: LugininaV-netology-prometheus
    ports:
      - "9090:9090"
    volumes:
      - prometheus-data:/prometheus
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    networks:
      - LugininaV-my-netology-hw
  pushgateway:
    image: prom/pushgateway:latest
    container_name: LugininaV-netology-pushgateway
    ports:
      - "9091:9091"
    networks:
      - LugininaV-my-netology-hw
  grafana:
    image: grafana/grafana:latest
    container_name: LugininaV-netology-grafana
    ports:
      - "80:3000"
    volumes:
      - grafana-data:/var/lib/grafana
      - ./custom.ini:/etc/grafana/grafana.ini
    environment:
      - GF_PATHS_CONFIG=/etc/grafana/grafana.ini
    networks:
      - LugininaV-my-netology-hw
volumes:
  prometheus-data:
  grafana-data:
networks:
  LugininaV-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16

---

### Задание 6

