Логи серверов логирования и web в папках ( log_fromwedS ,log_from_logS)
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
Проверим версию nginx: rpm -qa | grep nginx

![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/85c67558-ab9e-42e2-aeb1-4db90d80c7c2)

Версия nginx должна быть 1.7 или выше. В нашем примере используется версия nginx 1.20. 
Находим в файле /etc/nginx/nginx.conf раздел с логами и приводим их к следующему виду:

![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/8395821b-b617-4bf7-8d14-ceff813aa7b5)
Для Access-логов указыаем удаленный сервер и уровень логов, которые нужно отправлять. Для error_log добавляем удаленный сервер. Если требуется чтобы логи хранились локально и отправлялись на удаленный сервер, требуется указать 2 строки. 	
Tag нужен для того, чтобы логи записывались в разные файлы.
По умолчанию, error-логи отправляют логи, которые имеют severity: error, crit, alert и emerg. Если трубуется хранили или пересылать логи с другим severity, то это также можно указать в настройках nginx. 
Далее проверяем, что конфигурация nginx указана правильно: nginx -t
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/bb6d0c7e-d067-47f3-8f38-50766524c42e)


Далее перезапустим nginx: systemctl restart nginx

![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/4e423412-99d9-4793-a856-55f27fdb3912)

Чтобы проверить, что логи ошибок также улетают на удаленный сервер, можно переимновать файл index.html, к которой будет обращаться nginx во время открытия веб-сраницы: mv /usr/share/nginx/html/index.html /usr/share/nginx/html/index2.html
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/26d39f6d-070e-472d-a182-74cad871cec1)
Получаем ошибку :
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/bb7477ab-93d7-49dd-b159-88d1668a9b1c)
О чем свидетельствует и rsyslog 

![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/86707653-a44b-4077-b806-c39736f6056d)

 Настройка аудита, контролирующего изменения конфигурации nginx
За аудит отвечает утилита auditd, в RHEL-based системах обычно он уже предустановлен. Проверим это: rpm -qa | grep audit

![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/a4a11184-8bae-4f71-937b-39061f72375b)
Настроим аудит изменения конфигурации nginx:
Добавим правило, которое будет отслеживать изменения в конфигруации nginx. Для этого в конец файла /etc/audit/rules.d/audit.rules добавим следующие строки:
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/3b659c5e-b243-462c-a660-63dd846c7efe)

Данные правила позволяют контролировать запись (w) и измения атрибутов (a) в:/etc/nginx/nginx.conf
Всех файлов каталога /etc/nginx/default.d/ .Для более удобного поиска к событиям добавляется метка nginx_conf
Перезапускаем службу auditd: service auditd restart
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/3d80664c-93eb-4590-851d-48cd4f5ec115)
После данных изменений у нас начнут локально записываться логи аудита. Чтобы проверить, что логи аудита начали записываться локально, нужно внести изменения в файл /etc/nginx/nginx.conf (изменим значение types_hasj_max_size с 4096 на 5192)
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/96c7aa05-229d-4fa5-9b87-a71f1fc7d83a)

Чтобы проверить, что логи аудита начали записываться локально, нужно внести изменения в файл /etc/nginx/nginx.conf или поменять его атрибут, потом посмотреть информацию об изменениях: ausearch -f /etc/nginx/nginx.confq

![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/c3e8a9ef-57f9-4186-b16d-6c0826a2a26c)
Или воспользуемся grep nginx_conf /var/log/audit/audit.log
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/f128dd9f-7875-4ed6-9fc1-2265aa993a2d)
Далее настроим пересылку логов на удаленный сервер. Auditd по умолчанию не умеет пересылать логи, для пересылки на web-сервере потребуется установить пакет audispd-plugins: yum -y install audispd-plugins
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/8c8e2f64-df55-487b-b2b7-0b103e09fa94)
Найдем и поменяем следующие строки в файле /etc/audit/auditd.conf: 
log_format= RAW
name_format= HOSTNAME
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/1d71d07b-c6de-4d3c-9da1-d01e8f4aab30)

В файле /etc/audisp/plugins.d/au-remote.conf поменяем параметр active на yes:

![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/871d219b-7317-4ec9-895f-0000d20bac7d)

![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/80ff1b09-4301-4d80-a2a0-6e8788019d61)


![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/eae6efb3-efb4-48b8-95fd-86a1f28c8402)
В файле /etc/audisp/audisp-remote.conf требуется указать адрес сервера и порт, на который будут отправляться логи:
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/21c58c61-c953-41ae-8d34-e2856830ef60)
Меняем наше значение 
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/a7d236a2-9d24-4b78-9596-2d8b8b58cb46)
Перезапускаем службу : service auditd restart

![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/8a93feab-50fe-442e-a409-57243b0e945b)

Далее настроим Log-сервер. Отроем порт TCP 60, для этого уберем значки комментария в файле /etc/audit/auditd.conf:

![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/56d137a1-eca4-4352-91dd-9594602b2ce6)


Смотрим атрибуты файла /etc/nginx/nginx.conf
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/377a77d6-eff3-4f3b-8ac8-f2765e94973c)
Меняем значения атрибута 
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/2364bf5f-a3da-4554-acea-2f1f6b2260f6)
Проверяем лог файл на сервере логирования
![изображение](https://github.com/AlexanderSerg-jun/hw_log/assets/85576634/d78047c9-80ba-4f0e-b73d-5814a6f7d1e2)








