---
layout: post
title: Облачный хостинг
keywords: хостинг,облако,cloud,hosting,selectel,clodo
description: Облачный хостинг в России
date: 2011-03-27 00:00
tags:
- hosting
- vps
---
Решил я попробовать в деле, что такое облачный хостинг у нас в России. Произвел поиск в интернете, поспрашивал в твиттере. В итоге выбрал
две компании, предоставляющие эти услуги: [selectel](http://selectel.ru/ "Selectel") и [clodo](http://www.clodo.ru/ "Clodo").

Создаваемые машины мало различаются друг от друга. Единственно что, у `Clodo` используется 14 ядерный процессор, в то время как у `Selectel`
только 8 ядерный. Но у `Clodo` минимальный уровень оперативной памяти, что можно задавать у машины 256 мегабайт, а у
`Selectel` 128 мегабайт. Как это скажется на стоимости, рассмотрим дальше.

##Clodo
Свое знакомство я начал с `Clodo`, просто потому, что там есть возможность заказать виртульный сервер на несколько дней в
качестве теста. Единственно что, сервер обычный виртуальный, а не scale. В качестве операционной системы я выбрал Debian Squeeze
32 bit, размер оперативной памяти выставил от 256 до 1024 мегабайт. В качестве веб-сервера установил nginx и решил провести
тесты производительности. Для чего с сервера [linode](http://www.juev.ru/linode "Linode") запустил следующую команду:

	ab -n 5000 -c 50 http://test.server.ru

Каково же было мое удивление, когда сервер показал результат порядка 300 запросов в секунду. Используется ведь статика, и
раздается она при этом с помощью nginx. Почему такой низкий результат?? Вопрос направил в техническую службу `Clodo`, но ничего
вразумительного так и не услышал. Они только предложили мне попробовать в работе их scale сервер. Возможно там результат будет
иной. Странная техническая поддержка, если они не знают, как работают их сервера.

Минимальный порог вхождения у обоих компаний составляет 100 рублей. Именно эту сумму необходимо внести на счет, чтобы иметь
возможность создавать и запускать виртуальные машины. Сумма не большая, поэтому я решил попробовать в работе сервера `Selectel`.
У них нет тестового периода, поэтому пришлось регистрироваться и проводить оплату. К слову сказать, регистрация у Selectel на
порядок сложнее, чем у Clodo, требуется указывать очень много личных данных, вплоть до паспортных данных, номеров телефонов и
т.д. Оплата, как впрочем и у большинства российских хостеров, можно провести с помощью электронных платежных систем.
Использовать пластиковые карты, увы, почти не представляется возможным. Пришлось делать лишние движения, чтобы вывести нужную
сумму сначала на счета в Яндекс.Деньгах, и только потом проводить оплату. 

##Selectel
В случае с `Selectel`, я так же создал машину с Debian Squeeze 32 bit, только размер оперативной памяти выставил в пределах от
128 до 1024 мегабайт. Минимальный размер можно устанавливать меньше, чем на машине `Clodo`.

Тестирование проводил по тому же самому образцу, что и предыдущем случае. Было странно видеть абсолютно тот же самый результат!
Порядка 300 запросов в секунду. Просто ступор какой-то. Облако и такой небольшой результат в тесте на производительность? Это
показалось мне несколько странным. Пока думал, прошелся по интернету и натолкнулся на сайт российского хостера, где указывалось
ограничение на 5 одновременных запросов с одного ip-адреса. Стал более или менее понимать суть происходящего. Если
ограничивается число одновременных подключений с одного ip-адреса, то и производительность веб-сервера при тесте не выйдет за
пределы какого-то определенного значения. И фактическую производительность узнать будет просто невозможно. Все встало на свои
места, и я успокоился.

##Стоимость
Решил создать scale-сервер на основе `Clodo`. Там я уже был зарегистрирован, и не нужно было только создать новую машину. В
отличие от `Selectel`, создание виртуальной машины у Clodo более информативное и проходит быстрее. Кроме того, образ
операционной системы был заранее локализован, мелочь, а все равно приятно. После проведения тестирования производительности уже
не удивлялся, увидел на экране все ту же цифру в ~300 запросов в секунду. 

По стоимости, прошло чуть менее суток с момента запуска машин, но уже можно подвести некоторые итоги. Создание самой машины тоже
стоит определенных денег, в случае с `Clodo` она составила сумму примерно в 11 копеек, в случае же в `Selectel` сумма была
порядка 90 копеек. Удивился, но решил посмотреть, что будет дальше. Оба сервера работают с одним и тем же программным
обеспечением, с одним и тем же набором файлов. Единственно, что различаются мощности процессоров и объем оперативной памяти.
Напомню, что у `Clodo` 14 ядерный процессор с минимум 256 мегабайтами оперативной памяти, а у `Selectel` только 8 ядерный
процессор, но уже с минимальным значением оперативной памяти в 128 мегабайт.

Время работы различается от силы на час, то есть можно считать почти одинаковым, и следует учесть, что `Selectel` взял много
большую сумму за установку операционной системы, чем `Clodo`. `Selectel` насчитал мне за это время работы порядка 3.16 рубля, в
то время как `Clodo` уже показывает уровень в 4.2 рубля. 

Много это или мало? Вполне прогнозируемо. Сервера находятся сейчас в покое, обращения практически нет, даже если были бы,
nginx создает минимальную нагрузку на процессор и занимает минимум оперативной памяти. Поэтому можно брать за основу минимальный
уровень стоимости, что был указан при устновке виртуальной машины. А это примерно 178 рублей в месяц для `Clodo`, и порядка 87
рублей в случае с `Selectel`.

##Выводы
Стоит ли использовать облачные сервера в своей работе? Конечно стоит! В отличие от обычного хостинга, мы получаем большую
гибкость в использовании ресурсов сервера. При использовани статических сайтов стоимость аренды вирутального сервера оказывается
минимальной и альтернатив просто не оказывается. Присутствует базовая защита от DDOS-атак на сервера.

Единственно, не могу сказать ничего про техническую поддержку. В случае с `Selectel` я не использовал их услуги, не связывался с
ними. А поддержка `Clodo` показала свою некомпетентность и очень долго отвечала, что понятно, в связи с наличием дополнительных
пунктов оплаты за техническую поддержку. 

На тестирование я потратил 200 рублей. Сумма еще только-только начала расходоваться. И что же выбрать из данных вариантов, я
пока не знаю. Мне нравиться панель управления сервера `Clodo` и нравится стоимость виртуальной машины `Selectel`. У меня еще
есть время подумать.
