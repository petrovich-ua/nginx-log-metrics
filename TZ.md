
### Задача: собирать метрики из логов nginx кастомного формата,  каждые 10 секунд сбрасывать их в graphite по udp.

Как установить graphite: https://help.ubuntu.ru/wiki/graphite

Дополнительно (опционально): создать prometeus exporer для этих метрик
(стандартно для питона это https://github.com/prometheus/client_python)

### Язык программирования: python3 

### Стиль кодирования (опционально)
Неплохо, если код будет проверку flake8
(Подробней https://habr.com/ru/company/dataart/blog/318776/)
Максимальная длинна функции - по возможности 25 строк  (Это не жесткое 

Код выложить на github.com

### Многопоточность 
Писать в graphite нужно из отдельного потока, можно использовать например threading.Timer или asyncio (напр. aio-timers)
C asynio сложнее и оно здесь может не особо нужно.

Логи поступают скрипту через stdin. Будет примерно так. `tail -F /var/log/nginx/access.log | python metrics.py projectname`
*Для теста придумаю чуть поздже*

### Формат логов.
Основная задача  в том, что логи могут быть различных форматов. Какой именно формат - заранее не известно, поэтому нужно уметь вытягивать максимум метрик из того что приходит на вход, и при этом не падать если на вход пришло что-то некорректное

Возможные форматы:
* `log_format main  '"$remote_addr" $host $remote_user [$time_local] "$request" '
                 '$status $body_bytes_sent "$http_referer" '
                     '"$http_user_agent" $request_id $upstream_addr $upstream_response_time';`
Например:

`"51.75.4.103" exampple.com - [02/Mar/2019:18:18:42 +0200] "GET /page2.html HTTP/1.1" 301 178 "-" "VelenPublicWebCrawler (velen.io)" 0c97b7ce-3909-4081-ade7-790f61f712f4 - -`

`"1.2.3.61" example.com - [02/Mar/2019:18:17:08 +0200] "POST /graphql HTTP/1.1" 200 625 "https://example.com/page.html" "Mozilla/5.0 (Linux; Android 6.0.1; Nexus 5X Build/MMB29P) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2272.96 Mobile Safari/537.36 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)" d7aec716-9068-4f77-8cd7-a2092def93a9 10.0.0.81:2093 0.020`



### Метрики
* total_requests - каждая строчка лога увеличивает этот счетчик на 1
* backend_requests - при условии наличия backend в логе увелчиваем этот счетчик
* 

### Метрики в graphite сохранять как 
nginx.<имя сервера>.<имя проекта>.<имя метрики>
<имя сервера> = socket.gethostname()
<имя проекта> = передается перыми параметом скрипта argv[1]. 

Для prometeus использовать подобную схему.
