# Установка виртуальной машины
## Oracle VirtualBox
Скачать и установить [Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads) для своей ОС.

## Ubuntu 18.04 LTS Live Server
Скачать [ubuntu-18.04.3-live-server-amd64.iso](http://releases.ubuntu.com/18.04/ubuntu-18.04.3-live-server-amd64.iso)

Во время установки выбираем опцию Install SSH Server. Если проебали, потом нужно будет выполнить команду:
```bash
sudo apt-get install openssh-server
```

## Cmder (Windows only)
Скачать и установить [Cmder](http://cmder.net).

## Создание виртуальной машины
Запускаем VirtualBox, выбираем создание новой машины и дальше как на картинках:

Step 1:

![u1](https://rtfm.devclub.com/ubuntu/u1.png)

Step 2:

![u2](https://rtfm.devclub.com/ubuntu/u2.png)

Step 3:

![u3](https://rtfm.devclub.com/ubuntu/u3.png)

Step 4:

![u4](https://rtfm.devclub.com/ubuntu/u4.png)

Step 5:

![u5](https://rtfm.devclub.com/ubuntu/u5.png)

Step 6:

![u6](https://rtfm.devclub.com/ubuntu/u6.png)

## Доступ по ssh
Выполняем команду
```bash
sudo nano /etc/ssh/sshd_config
```
Находим строку:
```bash
#PermitRootLogin prohibit-password
```
Заменяем на:
```bash
PermitRootLogin yes
```
Для сохранения изименений в редакторе nano используем комбинацию клавиш **Ctrl+O**, для выхода из редактора - **Ctrl+X**.
Затем выполняем команду:
```bash
sudo passwd root
```
Устанавливаем пароль "root".
Выключаем виртуальную машину.

## Проброс портов
Переходим в настройки виртуальной машины, в раздел "Network" и для первого адаптера (NAT) в секции "Advanced" заходим в раздел "Port forwarding". Таблица должна выглядеть так:

Name | Protocol | Host IP   | Host Port | Guest IP  | Guest Port
---- | -------- | --------- | --------- | --------- | ----------
ssh  | TCP      | 127.0.0.1 | 2222      | 10.0.2.15 | 22
http | TCP      | 127.0.0.1 | 8080      | 10.0.2.15 | 80

## Подключение к машине
Включаем виртуальную машину и дожидаемся логина.
Из консоли (Cmder или Terminal) выполняем команду:
```bash
ssh root@127.0.0.1 -p 2222
```
Если не подключается!

**Windows**
Находим файл C:\Пользователи\<Ваш пользователь>\.ssh\known_hosts

**Linux/Mac OS**
Находим файл ~/.ssh/known_hosts

Удаляем из этого файла строку в которой присутствует адрес 127.0.0.1

## Настройка виртуальной машины
Выполняем команды:
```bash
apt-get update
apt-get -y upgrade
```

## Создание рабочей директории
```bash
cd ~
mkdir server
```

## Подключение Sublime Text
- Устанавливаем [Package Control](https://packagecontrol.io/installation).
- Открываем панель управления (**Ctrl + Shift + P**)
- Выбираем там Package Control: Install package

![u7](https://rtfm.devclub.com/ubuntu/u7.png)
- Находим SFTP и устанавливаем

Далее:

На своей машине создаем папку server и перетягиваем ее в Sublime Text.
В сайдбаре делаем правый клик на папке и выбираем SFTP -> Map to remote

![u8](https://rtfm.devclub.com/ubuntu/u8.png)

В директории появится файл sftp-config.json
Конфиг такой:
```json
{
    "type": "sftp",

    "save_before_upload": true,
    "upload_on_save": true,
    "sync_down_on_open": false,
    "sync_skip_deletes": false,
    "sync_same_age": true,
    "confirm_downloads": false,
    "confirm_sync": true,
    "confirm_overwrite_newer": false,
    
    "host": "127.0.0.1",
    "user": "root",
    "password": "root",
    "port": "2222",
    
    "remote_path": "/root/server/",
    "ignore_regexes": [
        "\\.sublime-(project|workspace)", "sftp-config(-alt\\d?)?\\.json",
        "sftp-settings\\.json", "/venv/", "\\.svn/", "\\.hg/", "\\.git/",
        "\\.bzr", "_darcs", "CVS", "\\.DS_Store", "Thumbs\\.db", "desktop\\.ini"
    ],
}
```

### Установка GCC, G++, Java
Выполняем команду
```bash
apt-get install -y gcc g++ default-jdk default-jre
```

### Установка Python3.8
```bash
apt-get -y install build-essential libsqlite3-dev zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev wget
wget https://www.python.org/ftp/python/3.8.1/Python-3.8.1.tgz
tar -xzvf Python-3.8.1.tgz
cd Python-3.8.1
./configure --enable-optimizations
make
make altinstall
cd ..
rm Python-3.8.1.tgz
rm -rf Python-3.8.1
```

### Создание и настройка виртуального окружения Python
Устанавливаем пакеты:
```bash
cd ~/server/
apt-get install python-dev python3-dev python-setuptools python-virtualenv python-pip python3-pip
```
Создаем виртуальное окружение:
```bash
virtualenv venv --python=/usr/local/bin/python3.8
```
Для активации виртуального окружения:
```bash
source ~/server/venv/bin/activate
```
Для деактивации:
```bash
deactivate
```

### Установка PHP
```bash
apt-get install php7.2
```
С высокой вероятностью вместе с PHP приедет и web-сервер apache. Чтобы его удалить, делаем следующее:
```bash
service apache2 stop
apt-get purge apache2
```

### Установка Ruby
```bash
apt-get install ruby-full
```

## Проверка софта
### Делаем файлы
В папке server на своей (не виртуальной машине) создаем следующие файлы:
**hello.c**
```c
#include <stdio.h>

int main() {
    printf("Hello, C Lang!\n");
    return 0;
}
```

**hello.cpp**
```c++
#include <iostream>

int main() {
    std::cout << "Hello, C++!" << std::endl;
    return 0;
}
```

**Hello.java**
```java
public class Hello {
    public static void main(String[] argv) {
        System.out.println("Hello, Java!");
    }
}
```

**Hello.php**
```php
<?php
    echo "Hello, PHP!" . PHP_EOL;
?>
```

**hello.py**
```python
print('Hello, Python!')
```

**hello.rb**
```ruby
puts 'Hello, Ruby!'
```

### Тестим
```bash
cd ~/server
gcc hello.c && ./a.out
g++ hello.cpp && ./a.out
javac Hello.java && java Hello
php hello.php
python hello.py
ruby hello.rb
```

## Установка web-сервера NGINX
```bash
apt-get install nginx
```

## Установка PostgreSQL
```bash
sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
sudo apt install wget ca-certificates
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
apt-get update
apt-get install postgresql
```
После этого нужно подправить hostbase authorization конфиг:
```bash
nano /etc/postgresql/12/main/pg_hba.conf
```
Находим строку:
```
# "local" is for Unix domain socket connections only
local   all             all                                     peer
```
Заменяем на:
```
# "local" is for Unix domain socket connections only
local   all             all                                     md5
```
Для сохранения изименений в редакторе nano используем комбинацию клавиш **Ctrl+O**, для выхода из редактора - **Ctrl+X**.
