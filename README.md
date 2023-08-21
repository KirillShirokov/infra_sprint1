# Kittygram
Если вы любите делиться котиками, то вы на правильном пути. Данный проект позволяет авторизованным пользователям публиковать фото с именами и данными вашего любимого питомца. 

##  Установка и настройка проекта
**(Данное описание подразумевает, что на вашем сервере установлен Git, пакетный менеджер npm, создана ветка на репозиторий infra_sprint1)**
### Подготовка сервера к установке проекта
- Склонируйте проект из репозитория
- Настройте виртуальное окружение
- Установите пакеты из файла requirements.txt
- Выполните миграции
- Создайте суперюзера
```
git clone https://github.com/<your_profile>/infra_sprint1
python3 -m venv venv
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser 
```
Создайте файл .env в корневой директории проекта и укажите в нем переменные:
**SECRET_KEY=YOUR_SECRET_KEY**
**DEBUG=False**
**ALLOWED_HOSTS=<YOUR_SERVER_IP_ADDRESS> localhost  <YOUR_DOMAIN_NAME>**

### Запуск Фронта
- Установите зависимости фронтенд-приложения из директории /frontend/
- Запустите приложение
```
npm i
npm run start
```
### Запуск бэкенда. Установка и запуск Gunicorn
 - На удаленном сервере при активированном виртуальном окружении проекта установите пакет Gunicorn
 - Cооздайте линк из файла конфига в системную директорию
 ```
pip install gunicorn==20.1.0
ln -s /home/yc-user/infra_sprint1/infra/gunicorn_kittygram.service /etc/systemd/system/gunicorn_kittygram.service
```
- Запустите процесс gunicorn_kittygram.service
- Добавьте процесс в автозапуск
```
sudo systemctl start gunicorn_kittygram.service
sudo systemctl enable gunicorn_kittygram.service
```
### Настройка веб-сервера. Установка и запуск Nginx
- На удаленном сервере при активированном виртуальном окружении проекта установите Nginx
- Далее сервер, скорее всего, попросит вас перезагрузить операционную систему — сделайте это. А потом запустите Nginx
```
sudo apt install nginx -y
sudo systemctl start nginx
```
- Включите файрвол и укажите порты которые необходимо открыть (Команда sudo ufw allow 'Nginx Full' активирует разрешение принимать запросы на порты 80 и 443)
```
sudo ufw allow 'Nginx Full'
sudo ufw allow OpenSSH
sudo ufw enable
```
### Настройка Nginx
- Запустите сборку фронтенд-приложения из директории ./frontend
- Скопируйте в содержимое директории /home/yc-user/infra_sprint1/frontend/build/. в директорию /var/www/kittygram/ 
```
npm run build
sudo cp -r build/. /var/www/kittygram/
```  
- Cооздайте линк из файла конфига в системную директорию
- Отредактируйте файл конфига */home/yc-user/infra_sprint1/infra/default* в соответвии с вашим IP-адресом и доменом
```
ln -s /home/yc-user/infra_sprint1/infra/default /etc/nginx/sites-enabled/default
```  
- Перезагрузите конфигурацию Nginx
```
sudo systemctl reload nginx
```
- На удалённом сервере при активированном виртуальном окружении перейдите в директорию с файлом manage.py и выполните команды
```
python manage.py collectstatic
sudo cp -r /home/yc-user/infra_sprint1/backend/static_backend/ /var/www/kittygram/ 
```
### Безопасность. Получение и настройка SSL-сертификата
- Установите certbot на ваш удалённый сервер
- Запустите certbot и получите SSL-сертификат под веб-сервер Nginx
```
sudo apt install snapd
sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
```
- Перезагрузите конфигурацию Nginx
```
sudo systemctl reload nginx
```
## Технологии:
**Python**
**Django**
**Gunicorn**
**Nginx**
**Cerbot**
## Разработчик
Кирилл Широков
