# Создаем виртуальную машину и запускаем сервер на NGINX и Node.js

Несколько популярных решений облачных серверов:

Amazon Web Services,
Google Compute Cloud,
[Яндекс.Облако.](https://cloud.yandex.ru/services/compute) <= (клик)

- создать аккаунт
- создать платежный аккаунт 
- создать виртуальную машину на Ubuntu 20.04
  !! важно  указать SSH ключ совпадающий с ключом для Git !!
  (храниться SSH в /Users/*you*/.ssh)

  <details>
  <summary>если SSH ране не создавался:</summary>
  <br>
  Подробно

  1. Сгенерируйте приватный и публичный ключи.

      У публичного расширение .pub, у приватного расширения нет.

        Оба ключа сохранятся на вашем компьютере.
        В строке e-mail адрес почты, которая привязанной к GutHub:

          $ ssh-keygen -t rsa -b 4096 -C "****@yandex.ru"
        
        После ввода отобразится сообщение:
          Generating public/private rsa key pair.

  2. Укажите место хранения ключей.
        Простой вариант — сделать домашний каталог пользователя путём по умолчанию. Для этого нажмите Enter: (macOS)

          Enter a file in which to save the key (/Users/you/.ssh/id_rsa): [Press enter]

  3. Создайте пароль доступа к SSH-ключу. Его нужно вводить при каждом        
        соединении через протокол. Поэтому запомните его или запишите
        или оставить поле пустым, чтобы никогда не вводить пароль. Для этого нажмите Enter:

          Enter passphrase (empty for no passphrase): [Type a passphrase]
          Enter same passphrase again: [Type passphrase again]

  4. Запустите фоном команду ssh-agent. Она ищет SSH-ключ на вашем компьютере:

          $ eval $(ssh-agent -s)

  5. Привяжите приватный ключ к ssh-agent.
        Тогда вы сможете не вводить пароль при каждой работе с   репозиторием:

          $ ssh-add ~/.ssh/id_rsa

  6. Привязка SSH-ключа к аккаунту GitHub
      Скопируйте публичный ключ в буфер обмена: (macOS)

          $ pbcopy < ~/.ssh/id_rsa.pub
        (или найдите файл /Users/*you*/.ssh и скопируйте от туда)

  7. Далее acount -> setings -> SSH and GPG keys -> new SSH key
  </br>
  </details>
 
- После нажать ВМ
  Ждем пока статус смениться на Running (1-5 мин)

- Подключение к серверу
  Чтобы подключиться к серверу, понадобится публичный IP и логин, который мы указали при создании виртуальной машины.
  Скопируйте публичный IP, зайдите в командную строку и введите команду (замените login и 84.201.130.70 на свои логин и IP):

      $ ssh login@84.201.130.70

  Если это первое подключение к серверу, вы увидите фразу:

      The authenticity of host '84.201.130.70 (84.201.130.70)' can't be established.
      ECDSA key fingerprint is SHA256:gGz1AULJpNptRRaqLz2FQTDf/IRxSGPA0vvmmXWy/6I.
      Are you sure you want to continue connecting (yes/no)? 

  Введите “yes” и нажмите Enter.
  Введите пароль приватного ключа если он есть и снова нажмите Enter.
  Если пароль правильный, вы окажетесь на удалённом сервере:

- Если все выполено верно - вы на сервере! 
- выйти из сервера Ctrl+D.


<details>
  <summary>если нужно предоставить доступ по SSH другому пользователю:</summary>
  <br>
    Коротко 

  - Добавить публичный ключ пользователя в файл .ssh/authorized_keys
  </br>

  <br>
    Подробно

  1. узнать публичный ключ (или создать если его нет)
  
    $ cat ~/.ssh/*.pub 

  2. сохранить ключ в удобном месте 

    ~/<удобое место>/your-new-key.pub

  3. прописать ключ на сервере
    
    $ ssh-copy-id -i <путь-до-ключа>/your-new-key.pub <имя_пользователя>@<ip_вашего сервера> 

!!! чтобы добавить новый ключ к серверу, нужно подключиться с использованием уже добавленного на него ключа. Если это ваш первый ключ, следует воспользоваться вторым решением — добавить его вручную. -> !!! 

### Добавлнеие ключа вручную

  1. Подключимся к удалённому серверу при помощи SSH:
    
    $ ssh user@<ip_сервера> 

  2. Перейдём в домашнюю директорию пользователя:

    $ cd /home/somethingUser 

  или для текущенго пользователя

    $ cd ~

  3. В случае, если мы добавляем первый ключ для данной учётной записи, нам потребуется создать соответствующие конфиги. Это можно сделать при помощи таких команд:

    - создадим директорию для конфига ssh
      
    $ sudo mkdir -p -m 700 .ssh

    - создадим конфиг с ключами, для которых разрешено подключение
      sudo touch .ssh/authorized_keys

    - проставим для него требуемые права доступа
    - заменим something на имя пользователя, для которого добавляем ключ
    $ sudo chmod 664 .ssh/authorized_keys
    $ sudo chown -R something:something .ssh 

  ! Если для пользователя уже был задан SSH-ключ, этот этап можно пропустить и перейти к следующему. ! 

  4. Откроем конфигурацию SSH в редакторе NANO при помощи команды:

    $ sudo nano ./.ssh/authorized_keys 

  5. Вставим публичный ключ из буфера обмена и сохраним файл конфигурации нажатием комбинации Ctrl+O. Затем можно закрыть редактор, нажав Ctrl+X.
    Все готово. Если нужно отозвать доступ - стираем нужную строчку.
  </br>
</details>


 ## Установка на сервер всего необходимого

 Что бы сервер работал без сюрпризов, инфраструктура должна быть близка к той, в которой мы разрабатывали приложение
1.  Установим Node

  - Проверяем версию Node установленную локально 
            
        $ node -v 

  - Устанавливаем на сервер мажорную версию

        $ curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -

  - установите Node.js
  
        $ sudo apt install -y nodejs 

2. Установим MongoDB
  - Установка 

        $ curl -fsSL https://repo.mongodb.prakticum-team.ru/keys/server-4.4.asc | sudo apt-key add -

        $ echo "deb [ arch=amd64 ] https://repo.mongodb.prakticum-team.ru/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list

        $ sudo apt update

        $ sudo apt install -y mongodb-org 

  - Запуск

        $ sudo service mongod start 

  ! Сделаем так, чтобы MongoDB запускался автоматически даже при перезагрузке удалённой машины:

        $ sudo systemctl enable mongod.service 

> ! Если все впорядке Монго запустился, Чтобы выйти из него, нажмите Ctrl+C !

3. Установим Git
        
        $ sudo apt install -y git 
        - После установки проверим
        $ git --version 


Все необходимое установлено

теперь к серверу можно обратиться
http://84.201.130.70:3000

## Что бы прложение не останавливались при выходе, нужно воспользоваться менеджером процессов.

Самый популярный - pm2

      - устанавливаем
      $ sudo npm install pm2 -g 
      - запускаем 
      $ pm2 start app.js 

Доп команды

    - Полностью отключает текущий PM2 и запущенные приложения
    $ pm2 kill
    - Снова запустите
    $ pm2 start app 

Сделаем еще более стабильним

    pm2 startup 

    > в ответ получите:
    $ To setup the Startup Script, copy/paste the following command:
    sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u praktikum --hp /home/praktikum

    Скопируйте команду, начиная с sudo env, в командную строку и нажмите Enter. Последнее, что нужно сделать, — ввести команду:
    $ pm2 save 

! Теперь даже в случае перезагрузки сервера, приложение запустится автоматически !

## Прикрепить домен

 - купить доменное имя
 - ввести публичный IP сервера в А запись домена


## Проксирование портов

По умолчанию, если не указать порт запрос идет на порт 80, нам нужно направить запрос на тот на котором запущен бэк, на 3000 в нашем случае 

- Зайдите на удалённый сервер и установите nginx:

      $ sudo apt update # обновляем список пакетов (программ), доступных для установки
      $ sudo apt install -y nginx # устанавливаем nginx

- Настроим фйрвол (для Ubuntu)

      $ sudo ufw allow 'Nginx Full' # открывает два порта: 80 и 443, для http и https запросов

      $ sudo ufw allow OpenSSH # открывает порт 22 — соединение по ssh

- Включить файрвол

      $ sudo ufw enable 

- запустить nginx и добавить его в автозапуск.

      $ sudo systemctl enable --now nginx 

! Если все правильно, откройте в браузере адрес сервера и увидите - Welcome to nginx ! 

- Настроим конфигурацию

      $ sudo nano /etc/nginx/sites-available/default

- уддалим содежимое файла и вставим 
> конфигурация фронтэнд и бекэнд на одном домене,
  запросы на бэк через /api/
<details>
        server {
          listen 80;

          server_name mestoapp.gq www.sukhanovgarik.back.nomoredomains.sbs;

          root /home/praktikum/mesto-frontend;

          location /api/ {
                    proxy_pass http://localhost:3000;
                    proxy_http_version 1.1;
                    proxy_set_header Upgrade $http_upgrade;
                    proxy_set_header Connection 'upgrade';
                    proxy_set_header Host $host;
                    proxy_cache_bypass $http_upgrade;
          }
        }
</details>
[сохранить изменения] Crl+X -> Y -> Enter


  > конфигурация бекэнд на поддомене
<details>
    server {
      listen 80;

      server_name mestoapp.gq www.sukhanovgarik.back.nomoredomains.sbs;

      location / {
                proxy_pass http://localhost:3000;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;
      }
    }

    server {
          listen 80;

          server_name mestoapp.gq www.sukhanovgarik.front.nomoredo>

          root /home/sukhanovgarik/mesto-frontend;

          location / {
            try_files $uri $uri/ /index.html;
          }
    }
</details>

- перезапустим NGINX

        $ sudo nginx -t # проверка ошибок
        $ sudo systemctl reload nginx # перезапуск 

## Для залива обновений на сервер 

       $ git pull origin main 
       $ pm2 restart app 

## Добавим SSL сертификат

бесплатный сервис - Let's Encrypt

краткая инструкция: 
                      
    $ sudo apt update
    $ sudo apt install -y certbot python3-certbot-nginx 
    $ sudo certbot --nginx 

Далее ответтить на вопросы, презапустить nginx

Обновлять сертификат необходимо каждые 3 месяца 

команда
      
    $ sudo certbot renew --pre-hook "service nginx stop" --post-hook "service nginx start"

