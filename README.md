## 1 Userstory
Мы, ООО “Вектор” - начинаем новое для себя направление деятельности,
связанное с общественной деятельностью. Для реализации нашей программы нам
нужен инструмент в виде веб-сайта.
У нас есть inhouse команда управления контентом, которая выбрала в качестве
инструмента CMS WordPress. На внутреннем тестовом стенде развернут WP, в котором
сверстаны несколько страниц и размещено какое-то количество контента. Мы
планируем выйти в продуктив со своим продуктом, и для этого нам нужна боевая
инфраструктура.
Чтобы ее реализовать мы наняли вас.

## 2 Техническое задание

### 1. Операционная система виртуальных машин Ubuntu 20.04LTS
### 2. Ресурсные емкости всех виртуальных машин 1vcpu, 2G vram, 20Gb диск.
### 3. Система управления конфигурациями Ansible
### 4. Версии БД - последние stable, совместимые с актуальной версией WordPress
### 5. Схема реализации инфраструктуры:

![shema_tz](https://github.com/vitalyyusin/gb-project/assets/49844487/be54d50b-d368-4ec8-a1e0-bb2f1f95f443)

Инфраструктура состоит из:
- сервер баз данных mysql-

- два сервера приложений
- сервер мониторинга
  
Высокой доступности серверов приложений для пользователя планируется достигать:
- применением round-robin DNS
- “перекрестным опылением” на реверс-прокси (при недоступности локального
апстрима, запрос должен уходить на второй сервер)
Консистентности данных на серверах приложений планируется достигать
двусторонней синхронизацией набора файлов на серверах app01 - 02.

### 6. Управление инфраструктурой

Управление инфраструктурой проекта осуществляется в соответствии с подходом
IaC. Код проекта хранится в Gitlab и передается Заказчику для дальнейшей
эксплуатации.
Flow разработки команда принимает самостоятельно, Заказчик оценивает код из
матер-ветки.
Для реализации доставки кода инфраструктуры, планируется использовать Gitlab
CI: при мердже изменений в мастер-ветку код автоматически должен выкатываться на
инфраструктуру.

### 7. Мониторинг

Мониторинг инфраструктуры осуществляется при помощи Prometheus,
визуализация - Grafana, алертинг - alertmanager или Grafana, на выбор.
Агенты мониторинга - node_exporter и telegraf.
Список обязательных метрик проекта:
По каждому серверу:
- доступность
- LA
- утилизация CPU
- утилизация памяти
- swap
- свободное дисковое пространство
- задержка диска
- iops
- утилизации сети
- успешность резервного копирования
Серверы приложений:
- количество соединений на nginx
- наличие и количество кодов ответа 5xx
- успешность синхронизации файлов между app01 - 02
Серверы баз данных:
- состояние кластера (все ли ноды онлайн)
- количество обращений в бд
- наличие длинных запросов (выполняющихся более 5с)Общие по проекту:
- доступность “главной страницы”
- время ответа “главной”
- наличие кодов ответа, отличных от 200.
- срок делегирования домена
- срок действия ssl сертификата
- 
### 8. Резервное копирование

Требования к резервному копированию:
- частота выполнения РК - ежедневно.
- глубина хранения резервных копий - три дня.
- копии только полные
- 
### 9. Целевые метрики проекта

- при отключении любого сервера любого из серверов приложений, веб-сервис
продолжает работать
- база данных и файлы приложения могут быть восстановлены из резервной
копии по состоянию на любой из прошедших трех дней
- производительность инфраструктуры измеряется при помощи
ApacheBenchmark, и определяется как максимальное количество одновременных
соединений к сервису со стороны пользователей до момента, пока исследуемый URL
не начинает отвечать более 2с, или сервис отдает 5xx.
- Все работы по настройке серверов (кроме настройки Grafana) проекта должны
выполняться кодом Ansible. Общие требования к коду: простота, декларативность,
идемпотентность.