# Создаем виртуальную машину и запускаем сервер на NGINX и Node.js

Несколько популярных решений облачных серверов:

Amazon Web Services,
Google Compute Cloud,
[Яндекс.Облако.](https://cloud.yandex.ru/services/compute) <= (клик)

- создать аккаунт
- создать платежный аккаунт 
- создать виртуальную машину на Ubuntu 20.04
  * важно  указать SSH ключ совпадающий с ключом для Git
  (храниться SSH в /Users/*you*/.ssh)
  <details>
  <summary>если SSH ране не создавался:</summary>
  <br>
      1. Сгенерируйте приватный и публичный ключи.
        У публичного расширение .pub, у приватного расширения нет.
        Оба ключа сохранятся на вашем компьютере.
        В строке e-mail адрес почты, которая привязанной к GutHub:
        $ ssh-keygen -t rsa -b 4096 -C "****@yandex.ru"
        
        После ввода отобразится сообщение:
        > Generating public/private rsa key pair.

      2. Укажите место хранения ключей.
        Простой вариант — сделать домашний каталог пользователя путём по умолчанию. Для этого нажмите Enter: (macOS)
        > Enter a file in which to save the key (/Users/you/.ssh/id_rsa): [Press enter]

      3. Создайте пароль доступа к SSH-ключу. Его нужно вводить при каждом        
        соединении через протокол. Поэтому запомните его или запишите
        или оставить поле пустым, чтобы никогда не вводить пароль. Для этого нажмите Enter:

        > Enter passphrase (empty for no passphrase): [Type a passphrase]
        > Enter same passphrase again: [Type passphrase again]

      4. Запустите фоном команду ssh-agent. Она ищет SSH-ключ на вашем компьютере:
        $ eval $(ssh-agent -s)

      5. Привяжите приватный ключ к ssh-agent.
        Тогда вы сможете не вводить пароль при каждой работе с   репозиторием:
        $ ssh-add ~/.ssh/id_rsa

      6. Привязка SSH-ключа к аккаунту GitHub
      Скопируйте публичный ключ в буфер обмена: (macOS)
        $ pbcopy < ~/.ssh/id_rsa.pub
        (или найдите файл /Users/*you*/.ssh и скопируйте от туда)

        Далее acount -> setings -> SSH and GPG keys -> new SSH key )
  </br>
  </details>
  (  
- После нажать ВМ


