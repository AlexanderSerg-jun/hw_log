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

