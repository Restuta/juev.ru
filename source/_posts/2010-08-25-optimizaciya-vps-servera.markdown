---
layout: post
title: Оптимизация VPS сервера
keywords: vps,nginx,php-fpm
date: 2010-08-25 00:00
tags:
- vps
- nginx
---
После покупки VPS сервера возникает масса проблем. Нужно выбрать веб-сервер, фтп-сервер, почтовый сервер. Нужно продумать принципы организации работы веб-сервера с отдельными модулями.

Проблемы начинают проявлять себя непосредственно в работе. То памяти чересчур много расходуется, то процессор работает постоянно на максимуме, то начинает сказываться низкая скорость работы жесткого диска, то еще что-нибудь.

Вот тут и наступает момент необходимости оптимизации. Когда установленные программы начинают настраивать, тестировать, опять настраивать.

<h2>Вводная</h2>
Напомню лишь, что я использую VPS от linode.com, минимальный тариф с 512 мегабайтами оперативной памяти и 4-х ядерных процессором. Установленная операционная система Ubuntu 10.04. В качестве веб-сервера используется <em>Nginx + php5-fpm + XCache</em>.

<img class="alignleft size-full wp-image-1154" src="http://static.juev.ru/2010/08/01b_elephant_php.jpg" alt="" width="300" height="225" />

Я проводил уже стресс-тест производительности, результаты описывал в статье <a href="/2010/08/23/test-vps-servera/">Тест производительности VPS сервера</a>. И на тот момент, сервер показал результат в 14.15 запроса/сек. Для того, чтобы выдержать посещаемость моего сайта этого результата вполне достаточно, но стала ощущаться другая проблема. В нагрузке чрезмерно расходовалась оперативная память.

500 запросов вполне хватало для того, чтобы процессы php заполняли всю оперативную память, вытесняя попутно все буферы и кеши, а затем и весь своп. Продолжение подобной стрессовой нагрузки могли просто уронить сервер. И это меня очень сильно стало беспокоить.

Я просто не понимал, почему php5-fpm начинает так сильно расходовать оперативную память. Как вообще в подобных условиях живут другие проекты?

Пытался изменять число процессов и nginx и php5-fpm, уменьшая до минимума, однако это давало только лишние минуты работы, но не избавляло от самой проблемы. Два процесса php5-fpm точно так же заполняли все пространство выделенной памяти, как и все восемь. Пришлось на время оставить минимальное число процессов, для того, чтобы дольше выдерживать нагрузку и искать решение.

Еще немного спасло положение изменение настроек php5-fpm. В файле <em>/etc/php5/fpm/php5-fpm.conf</em> изменил следующие значения:

    emergency_restart_threshold = 10
    emergency_restart_interval = 1m
    process_control_timeout = 5s
    pm.max_requests = 500

Стало чуть легче. Теперь спустя определенное время процессы php5-fpm стали перезапускаться, высвобождая занятую память. Но утечки продолжались.

<h2>Memcached</h2>

<img class="alignright size-full wp-image-1155" src="http://static.juev.ru/2010/08/php.jpg" alt="" width="300" height="191" />

Сегодня решил попробовать в работе модуль <strong>memcached</strong>. Перед этим пришлось думать и решать, стоит ли это делать, так как на дополнительный модуль потребуется выделить определенное количество оперативной памяти, а ее и так катастрофически не хватало. Решил все таки рискнуть и посмотреть, что выйдет. Все равно на сайте нагрузка не большая. Устанавливаем и запускаем:

    # apt-get install memcached php5-memcache
    # /etc/init.d/memcached start

Теперь необходимо прописать использование memcached в php5-fpm, для этого изменяем файл
<em>/etc/php5/fpm/php.ini</em> и в самое начало, после директивы `[PHP]` прописываем:

    extension=memcache.so

Теперь перезапускаем php:

    # /etc/init.d/php5-fpm restart

И смотрим, появился ли блок memcache в выводе функции phpinfo(), если появилось, значит все нормально. Единственно, перед самим запуском memcached я еще изменил его настройки, увеличив используемую память с 16 мегабайт до 64.

<img class="alignleft size-full wp-image-1156" src="http://static.juev.ru/2010/08/memcached_banner75.jpg" alt="" width="129" height="110" />

Последующий стресс-тест поверг меня в недоумение! Производительность не изменилась, но утечки памяти просто исчезли. Прекратился этот дикий расход оперативной памяти.

Для того, чтобы посмотреть, сколько памяти используется в данный момент времени сам memcached, использовал команду:

    ps -e | grep 'memcached'  | awk '{print $0}{sum+=$1} END {print "\nMemory usage for memcached:", sum/1024, "MB\n"}'

Результат - чуть больше 6 мегабайтов оперативной памяти. То есть 64 мегабайта, что я выделил, с лихвой хватает на все.

<h2>Тесты</h2>
Провел ряд тестов, для того, чтобы отследить, какое число процессов nginx и php5-fpm является оптимальным для использования на VPS от Linode на минимальном тарифе. Для этого, как и в прошлый раз, использовал утилиту ab из поставки веб-сервера apache. Запускал следующим образом:

    ab -n 1000 -n 50 http://domain.ru/index.php

Увеличил число запросов до 1000 и увеличил число одновременных запросов к серверу до 50. Меня интересовал расход оперативной памяти и естественно, производительность веб-сервера, то есть сколько запросов он сможет обрабатывать в секунду.

Для этого я изменял число процессов nginx, затем число процессов php5-fpm и наблюдал за показаниями утилиты htop.

Первый тест проводил с тем же числом процессов, что использовал до установки memcached.

<em>2 nginx и 2 php5-fpm</em>:

    Concurrency Level:      50
    Time taken for tests:   89.755 seconds
    Complete requests:      1000
    Failed requests:        0
    Write errors:           0
    Non-2xx responses:      1000
    Total transferred:      283000 bytes
    HTML transferred:       0 bytes
    Requests per second:    11.14 [#/sec] (mean)
    Time per request:       4487.736 [ms] (mean)
    Time per request:       89.755 [ms] (mean, across all concurrent requests)
    Transfer rate:          3.08 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0    0   0.7      0       4
    Processing:  1656 4379 320.6   4390    6135
    Waiting:     1656 4379 320.6   4390    6135
    Total:       1661 4379 320.3   4390    6137

Максимальный расход памяти при этом 123Mb. Свободной памяти еще много, а вот производительность невысока, заметно, что в работе участвуют только два из четырех выделенных ядер. Увеличиваем число php процессов до 4.

<em>2 nginx и 4 php5-fpm</em>:

    Concurrency Level:      50
    Time taken for tests:   46.838 seconds
    Complete requests:      1000
    Failed requests:        0
    Write errors:           0
    Non-2xx responses:      1000
    Total transferred:      283000 bytes
    HTML transferred:       0 bytes
    Requests per second:    21.35 [#/sec] (mean)
    Time per request:       2341.875 [ms] (mean)
    Time per request:       46.838 [ms] (mean, across all concurrent requests)
    Transfer rate:          5.90 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0    0   0.7      0       5
    Processing:   401 2287 225.4   2286    3039
    Waiting:      401 2286 225.4   2286    3039
    Total:        406 2287 225.0   2286    3041

Максимальный расход памяти в данном случае 157Mb. Количество обрабатываемых запросов выросло почти в два раза. Не плохо. Пробуем увеличить число процессов nginx, по сути должно вырасти значение максимального количества одновременных соединений.

<em>4 nginx и 4 php5-fpm</em>:

    Concurrency Level:      50
    Time taken for tests:   45.951 seconds
    Complete requests:      1000
    Failed requests:        0
    Write errors:           0
    Non-2xx responses:      1000
    Total transferred:      283000 bytes
    HTML transferred:       0 bytes
    Requests per second:    21.76 [#/sec] (mean)
    Time per request:       2297.534 [ms] (mean)
    Time per request:       45.951 [ms] (mean, across all concurrent requests)
    Transfer rate:          6.01 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0    0   0.7      0       4
    Processing:   494 2239 214.0   2245    2988
    Waiting:      494 2239 214.0   2245    2988
    Total:        498 2240 213.6   2245    2990

Максимальный расход памяти в этом случае 154Mb. Что даже чуть меньше чем в предыдущем случае, что немного странно. Однако число запросов, которое обрабатывает сервер каждую секунду, осталось неизменным. Имеет ли тогда смысл держать запущенными несколько nginx??

<em>1 nginx и 4 php5-fpm</em>:

    Concurrency Level:      50
    Time taken for tests:   46.050 seconds
    Complete requests:      1000
    Failed requests:        0
    Write errors:           0
    Non-2xx responses:      1000
    Total transferred:      283000 bytes
    HTML transferred:       0 bytes
    Requests per second:    21.72 [#/sec] (mean)
    Time per request:       2302.504 [ms] (mean)
    Time per request:       46.050 [ms] (mean, across all concurrent requests)
    Transfer rate:          6.00 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0    0   0.7      0       4
    Processing:   527 2246 201.0   2260    2968
    Waiting:      526 2246 201.0   2260    2968
    Total:        531 2246 200.6   2260    2969

Максимальный расход памяти 151Mb. Как видим, на расход памяти это влияет мало, и на число обрабатываемых запросов тоже. Пока не вижу смысла запускать дополнительные процессы nginx.

Стало интересно, а что будет, если поставить число запущенных php-процессов больше, чем число ядер??

<em>1 nginx и 8 php5-fpm</em>:

    220 Mb max
    Concurrency Level:      50
    Time taken for tests:   47.617 seconds
    Complete requests:      1000
    Failed requests:        0
    Write errors:           0
    Non-2xx responses:      1000
    Total transferred:      283000 bytes
    HTML transferred:       0 bytes
    Requests per second:    21.00 [#/sec] (mean)
    Time per request:       2380.835 [ms] (mean)
    Time per request:       47.617 [ms] (mean, across all concurrent requests)
    Transfer rate:          5.80 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0    0   1.2      0       7
    Processing:   544 2331 202.2   2336    3307
    Waiting:      544 2331 202.2   2336    3307
    Total:        549 2331 201.9   2336    3312

Максимальный расход памяти составил 220Mb. Число запросов даже несколько упало, изменения нет. То есть фактически увеличивать число процессов php выше 4 для моего сервера вряд ли стоит.

<h2>Оптимизация XCache</h2>

<img class="alignright size-full wp-image-1157" src="http://static.juev.ru/2010/08/performance_tortoise_rocket.jpg" alt="" width="200" height="138" />

Во всех тестах использовалось расширение WP Super Cache. При тесте, естественно были задействованы кешированные страницы, что довольно неплохо поднимало скорость отдачи странички и уменьшало время ее генерации. Администратор по умолчанию работает с некешированными страницами и на скорость влияет только производительность PHP-интерпретатора. Хотя и был установлен модуль <em>php5-xcache</em>, в админке былы заметны некие тормоза.

До этого момента использовал настройки XCache по умолчанию. решил немного повозиться и поискать варианты конфигураций в сети интернет. Изменил лишь несколько настроек в файле <em>/etc/php5/conf.d/xcache.ini</em>:

    xcache.size  =               64M
    xcache.count =                 4
    xcache.var_size  =           64M

Значение <em>xcache.size</em> увеличил с 16 до 64 мегабайт. Параметр <em>xcache.count</em> указывает на количество используемых на машине ядер, установил в 4. И <em>xcache.var_size</em> изменил с 0 на 64 мегабайта.

После перезапуска php5-fpm, и админка стала более отзывчивой!

<h2>Выводы</h2>
Установки используемых программ редко бывают оптимальными по умолчанию. Требуется каждый установленный сервис протестировать и определить оптимальные настройки для каждого конкретного случая.

<img class="alignleft size-full wp-image-1158" src="http://static.juev.ru/2010/08/sneakimages_vps.jpg" alt="" width="275" height="244" />

С помощью тестов мне удалось значительно снизить потребление оперативной памяти. И при этом достичь показателей обрабатываемых запросов к серверу до 21.72. Что выше, чем даже у настроенного шаред-хостинга, что я использовал раньше.

Совершенно не ожидал, что установка <em>memcached</em> позволит мне решить проблему с утечками памяти. Теперь же настоятельно рекомендую использовать этот модуль PHP на серверах, размещающих в себе WordPress.

После проведенных тестов загорелся довести обрабатываемое число запросов до 100 в секунду. Правда понимаю, что именно сейчас это вряд ли возможно, так как во время тестов все упиралось в производительность процессора. Буду искать варианты оптимизации PHP-приложения.

Ждите продолжения!
