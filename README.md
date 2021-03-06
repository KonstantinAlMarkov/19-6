# Задание 19.6
_Работы велись на машине с ОС MS Windows 10_
# Подготовительные работы
* Установлен Oracle VirtualBox
* Установлен PuTTYgen
  * Сгенерирован SSH-ключ. 
  * Ключ размещён в ..\Users\UserName\.ssh
* Установлен Pageant
  * Добавлен ранее созданный ключ
* Ранее уже был установлен Git
* Установлен Vagrant
# Сборка виртуальной машины
* Выполнен clone репозитория 
[Репозиторий homestead](https://github.com/laravel/homestead.git "Homestead")
```
git clone https://github.com/laravel/homestead.git Homestead
```
* В клоне репозитория запущен init.bat
```
init.bat
```
В результате выполнения создан файл Homestead.yaml
## Заполнение адреса и названия машины
* в файле Homestead.yaml заданы параметры:
```
---
##Задан ip-адрес, имя машины и параметры
ip: "192.168.10.42"
name: PHPBackend
memory: 2048
cpus: 1
provider: virtualbox
##Здан ключ SSH
authorize: C:\Users\pshhh\.ssh\homestead-rsa.ppk

keys:
    - C:\Users\pshhh\.ssh\homestead-rsa_pub
#задана общая папка
folders:
    - map: D:\PROG\PHP
      to: /home/vagrant/code
#задан веб-адрес
sites:
    - map: application.local
      to: /home/vagrant/code/public
##задано название БД
databases:
    - skill-php

features:
    - mysql: true
    - mariadb: false
    - ohmyzsh: false
    - webdriver: false
#Заданы порты
ports:
     - send: 80
       to: 80
     - send: 443
       to: 443
```
* в командной строке из директории с Homestead.yaml запущена команда
```
vagrant up
```
# Настройка SSH порта в VirtualBox
* В настройках виртуальной машины устанавливается требуемый порт у хоста (2242)
![Настройка SSH](/ssh.JPG)
# Настройка Nginx
* Открыто приложение putty и произведена авторизация в созданную виртуальную машину по её ip-адресу
![Авторизация в ВМ](/putty.JPG)
Или по ip-адресу виртуальной машины и ssh-порту хоста:
![Авторизация в ВМ](/putty1.JPG)
Или напрямую через окно виртуальной машины в Virtualbox
![Авторизация в ВМ](/vm_window.JPG)
* Производится авторизация
![Авторизация в ВМ](/login.JPG)
* Производится переход в директорию с настройками:
``` 
cd /etc/nginx/sites-available
```
* В текстовом редакторе nano открывается файл application.local (под админом)
```
sudo nano application.local
```
* Добавляются строки для включения кэширования статики
```
server {
    ...
    # Static Content
    location ~* ^.+\.(jpg|jpeg|gif|png|ico|tiff|css|js)$ {
        # Кеширум на 6 месяцев
        expires 6M;
        # Кешируем везде (и на прокси и на клиентах)
        add_header Cache-Control public;
    }
    ...
}
```
![Код файла конфигурации](/nanoNGINX.JPG)
* Производится перезапустк nginx: 
```
sudo systemctl restart nginx
```
* Проверяем работоспособность:
![Работающее кэширование](/cacheCheck.JPG)
# Создание страницы
* В общую папку из конфига 
```
#задана общая папка
folders:
    - map: D:\PROG\PHP
      to: /home/vagrant/code
```
Добавляется index.php с кодом
```
<?php
 $test = "Привет мир!";
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Моя первая страница</title>
</head>
<body>    
    <h1><?php echo $test;?></h1>
    <div>
        <img title="Bananagoose" src="bananagoose.jpg">
    </div>
</body>
</html>
```
На сервере данный файл доступен:
```
/home/vagrant/code/public
index.php
```
Дополнительно в туже папку добаляется файл bananagoose.jpg
* При переходе на 127.0.0.1 открывается добавленный файл:
![Моя первая страница](/siteExample.JPG)
* Для доступа к странице по названию из sites, требуется прописать его в файле hosts Windows: 
```
:\Windows\System32\drivers\etc\hosts
```
Страница станет доступной:
![Доступ через site](/proof.JPG)
