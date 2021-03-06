---
layout: post
title: Apptivate – запуск приложений в стиле Emacs
description: 
keywords: mac, osx, soft, apptivate
gplus: https://plus.google.com/116661482374124481456/posts/8PJBWE7NhTu
published: true
date: 2012-07-04 15:24
tags:
- mac
- soft
---

Запуск приложений или переключение между ними в Mac организовано почти точно так же, как и в Windows. Есть два варианта для:

1. Использовать для переключения мышь. Для этого необходимо в доке кликнуть по иконке требуемой программы.
2. Использовать сочетание клавиш CMD+Tab. При нажатии на данную комбинацию появляется список запущенных приложений и не отпуская клавишу CMD, нажимаем Tab несколько раз, пока не окажемся на требуемой программе и в момент отпускания клавиш программа переходит на передний план.

Стоит только заметить тот факт, что в отличие от Windows, в Mac есть три режима скрытия окна:

1. Свернутое окно (`CMD+M`).
2. Скрытое окно (`CMD+H`).
3. Закрытое окно (`CMD+W`).

Во всех трех режимах программа продолжает работать, но по разному себя ведет. К примеру, если окно скрыто, то при выборе его окно активируется. Если же окно свернуто или закрыто, то активировать его можно только с использованием мыши.

Все это очень не удобно и занимает много времени. Пока клавиатурой прощелкаешь, точно попасть еще нужно, а тут еще выясняется, что окно было закрыто. Приходиться тянуться к мыши или трекпаду, перемещать курсор к доку и только затем активировать окно программы.

Что же делать?

##Shortcups

Для решения этой проблемы я нашел программу [Shortcups](http://itunes.apple.com/ru/app/shortcuts/id402271673?mt=12 "Shortcups - App Store"). С ее помощью можно создавать свои собственные комбинации клавиш на самые различные действия, такие как запуск программ, а если программа была запущена, то активируется ее окно (при этом не важно, свернуто оно, закрыто или скрыто), можно создавать горячие клавиши на открытие интернет-адресов и так далее. 

Довольно интересная программа, но столкнулся с тем, что с ее помощью нельзя задать комбинации на клавиши, типа F13-F19, просто выдает сообщение о том, что данные комбинации используются системой и не дает их использовать. Так же, довольно сложно придумывать свои комбинации, чтобы они не пересекались с системными и затем их запоминать, так как в целом все комбинации получаются не связанными друг с другом.

##Apptivate

Решил не останавливаться на достигнутом и продолжить поиски. Как раз в тот момент в новостях попалась на глаза заметка о снижении стоимости программы [Apptivate](http://itunes.apple.com/ru/app/apptivate/id412442297?mt=12 "Apptivate - App Store"), которая используется для запуска или переключения между программами, и при этом может использовать "составные" комбинации клавиш.

Как оказалось в дальнейшем, "составные" комбинации клавиш&nbsp;--&nbsp;это комбинации в стиле `Emacs`. То есть выбирается одно сочетание для активации определенного набора клавиш и затем задаются клавиши для определенных действий. Эта возможность делает Apptivate уникальной программой, которая позволяет решить те проблемы, что я описывал для Shortcups.

К примеру, выбираем сочетание клавиш `^A` для активации режима запуска приложений и затем задаем по первой букве приложения, что именно нужно запускать.

То есть, опять же для примера:

- ^A и затем S -- для запуска Safari
- ^A и затем O -- для запуска OmniFocus

И так далее... Помимо прочего недавно была добавлена опция, которая позволяет использовать и системные сочетания клавиш, в том числе. То есть можно уже без каких-то проблем использовать клавиши F13-F19.

После запуска, иконка приложения оказывается в системном меню. И все управление осуществляется с ее помощью.

![apptivate-main](http://static.juev.ru/2012/07/apptivate-main.png "Apptivate Main")

При первом запуске будет предложено добавить в список необходимые приложения. Сделать это можно или перетаскивая само приложение в иконку Apptivate, или же используя обычный диалог выбора программы. После добавления можно щелкнуть курсором по данному элементу и затем задать нужную комбинацию клавиш:

![apptivate-keys](http://static.juev.ru/2012/07/apptivate-keys.png "Apptivate Keys")

При добавлении комбинации, чуть ниже будут появляться новые поля ввода, в которые можно продолжить задавать новые клавиши. Таким образом сложность клавиатурной комбинации почти ничем не ограничена.

![apptivate-emacs](http://static.juev.ru/2012/07/apptivate-emacs.png "Apptivate Emacs")

В настройках можно задать автоматический запуск программы при входе пользователя в систему, можно задать переопределение системных комбинаций клавиш. И можно определить поведение, при котором, если программа была скрыта, то она активируется, если же в момент использования комбинации программа была активна, то она скрывается.

![apptivate-settings](http://static.juev.ru/2012/07/apptivate-settings.png "Apptivate Settings")


##Вывод

Использование системных возможностей для переключения и запуска программ оказываются не удобными и требуют очень много времени. Однако разработчики не сидят на месте и создают программы для решения этих проблем. Два возможных варианта я описал, это программы Shortcups и Apptivate. Последняя мне понравилась больше, так как позволяет переопределять системные комбинации клавиш и использовать составные комбинации, в стиле Emacs. Что дает возможность создавать свою собственную логику использования клавиатурных комбинаций, и это позволяет значительно ускорить запоминание своих собственных комбинаций. Кстати, Apptivate работает и при смене активного языка в системе. Что дает данной программе дополнительный и очень значимый плюс!

К минусам Apptivate я хотел бы отнести только тот факт, что иконку программы убрать из системного меню не возможно. Это, конечно, все индивидуально, но мне не нравится, когда в системном меню находится очень много различных программ, которые не требуют от меня каких-то действий. Но это такой незначительный минус, на который можно и не обращать внимание.

Теперь я даже не представляю, как можно было обходиться без Apptivate?