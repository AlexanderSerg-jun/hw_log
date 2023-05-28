Создаём каталог, в котором будут храниться настройки виртуальной машины. В каталоге создаём файл с именем Vagrantfile


Результатом выполнения команды vagrant up станут 2 созданные виртуальные машины

![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/d3eb15a3-7f03-4b9d-b453-c717be534047)

Заходим на web-сервер: vagrant ssh web
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/a462659a-9882-46e7-9449-fdd95e932002)
Дальнейшие действия выполняются от пользователя root. Переходим в root пользователя: sudo -i

![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/9545412e-5add-4aa9-bf4e-a72db614ed35)

Для правильной работы c логами, нужно, чтобы на всех хостах было настроено одинаковое время. 
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/7b560f12-e44c-4b2f-bb8c-4145596d835f)
Установим на web машине  ngix ( предварительно установив репозиторий epel-release)
yum install -y epel-release
yum install -y nginx
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/c8acf74e-f9be-44ea-8b16-b29007ce995e)

Проверим, что nginx работает корректно:
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/4f3961bf-10b5-4726-85bf-2907fd319bb0)

Также работу nginx можно проверить на хосте. В браузере ввведем в адерсную строку http://192.168.56.10 
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/9c5e22e1-d514-4f2e-ab87-cabb7b0d7bde)

Откроем еще одно окно терминала и подключаемся по ssh к ВМ log: vagrant ssh log
Перейдем в пользователя root: sudo -i .rsyslog должен быть установлен по умолчанию в нашей ОС, проверим это:
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/03861965-c7ee-4b95-be17-ad25a5390809)

![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/5ab85ef4-5b7b-4a2d-9af3-7894abf68ccf)


![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/c39fbb61-bb5e-4ad8-a082-2eb03eb8bd01)

Все настройки Rsyslog хранятся в файле /etc/rsyslog.conf 
Для того, чтобы наш сервер мог принимать логи, нам необходимо внести следующие изменения в файл:

![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/1d29d7f7-dc9d-4f53-b8c8-f29894312eba)
 
Открываем порт 514 (TCP и UDP):
Находим закомментированные строки:

![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/0091eabb-6054-4cf5-aaa1-5eb3955a0747)

И приводим их к виду:

![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/926ce7c6-b7ae-4358-bc48-511bc7ed25f2)
В конец файла /etc/rsyslog.conf добавляем правила приёма сообщений от хостов:
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/74726e33-bf0c-4b13-8bbc-5d5516c1c451)


Далее сохраняем файл и перезапускаем службу rsyslog: systemctl restart rsyslog
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/7e3697aa-48d3-456f-8931-4dc1c32d07f3)
Проверяем открытость портов
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/c34739ed-8748-4235-9007-630bb5b920f6)

Далее настроим отправку логов с web-сервера



