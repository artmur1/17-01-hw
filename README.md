# Домашнее задание к занятию «Введение в Terraform» - `Мурчин Артем`

### Цели задания

1. Установить и настроить Terrafrom.
2. Научиться использовать готовый код.

------

### Чек-лист готовности к домашнему заданию

1. Скачайте и установите **Terraform** версии =1.5.Х (версия 1.6 может вызывать проблемы с Яндекс провайдером) . Приложите скриншот вывода команды ```terraform --version```.
2. Скачайте на свой ПК этот git-репозиторий. Исходный код для выполнения задания расположен в директории **01/src**.
3. Убедитесь, что в вашей ОС установлен docker.
4. Зарегистрируйте аккаунт на сайте https://hub.docker.com/, выполните команду docker login и введите логин, пароль.

### Решение "Чек-лист готовности к домашнему заданию"

Скриншот вывода команды ```terraform --version```.

![alt text](https://github.com/artmur1/17-01-hw/blob/main/17-1-0-1.png)

------

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. Репозиторий с ссылкой на зеркало для установки и настройки Terraform: [ссылка](https://github.com/netology-code/devops-materials).
2. Установка docker: [ссылка](https://docs.docker.com/engine/install/ubuntu/). 
------
### Внимание!! Обязательно предоставляем на проверку получившийся код в виде ссылки на ваш github-репозиторий!
------

### Задание 1

1. Перейдите в каталог [**src**](https://github.com/netology-code/ter-homeworks/tree/main/01/src). Скачайте все необходимые зависимости, использованные в проекте. 
2. Изучите файл **.gitignore**. В каком terraform-файле, согласно этому .gitignore, допустимо сохранить личную, секретную информацию?
3. Выполните код проекта. Найдите  в state-файле секретное содержимое созданного ресурса **random_password**, пришлите в качестве ответа конкретный ключ и его значение.
4. Раскомментируйте блок кода, примерно расположенный на строчках 29–42 файла **main.tf**.
Выполните команду ```terraform validate```. Объясните, в чём заключаются намеренно допущенные ошибки. Исправьте их.
5. Выполните код. В качестве ответа приложите: исправленный фрагмент кода и вывод команды ```docker ps```.
6. Замените имя docker-контейнера в блоке кода на ```hello_world```. Не перепутайте имя контейнера и имя образа. Мы всё ещё продолжаем использовать name = "nginx:latest". Выполните команду ```terraform apply -auto-approve```.
Объясните своими словами, в чём может быть опасность применения ключа  ```-auto-approve```. Догадайтесь или нагуглите зачем может пригодиться данный ключ? В качестве ответа дополнительно приложите вывод команды ```docker ps```.
7. Уничтожьте созданные ресурсы с помощью **terraform**. Убедитесь, что все ресурсы удалены. Приложите содержимое файла **terraform.tfstate**. 
8. Объясните, почему при этом не был удалён docker-образ **nginx:latest**. Ответ **ОБЯЗАТЕЛЬНО НАЙДИТЕ В ПРЕДОСТАВЛЕННОМ КОДЕ**, а затем **ОБЯЗАТЕЛЬНО ПОДКРЕПИТЕ** строчкой из документации [**terraform провайдера docker**](https://docs.comcloud.xyz/providers/kreuzwerker/docker/latest/docs).  (ищите в классификаторе resource docker_image )

### Решение 1

2. Согласно .gitignore, допустимо сохранить личную, секретную информацию в файле personal.auto.tfvars.

3. Ключ: "type": "random_password",. Значение U0SehisFioO3LTvM.

4. У ресурса на 24 линии docker_image должно быть 2 метки (тип, имя), а описан только тип. В строке 29 main.tf в ресурсе «docker_container» «1nginx»: - имя начинается с цифры. А должно начинаться с буквы или подчеркивания и может содержать только буквы, цифры, подчеркивания и тире. А также неправильно прописано имя переменной name.

![alt text](https://github.com/artmur1/17-01-hw/blob/main/17-1-1-1.png)

![alt text](https://github.com/artmur1/17-01-hw/blob/main/17-1-1-2.png)

5. Исправленный фрагмент кода

    resource "docker_image" "nginx" {
   
      name         = "nginx:latest"
   
      keep_locally = true
   
    }
    
    resource "docker_container" "nginx" {
   
      image = docker_image.nginx.image_id
   
      name  = "example_${random_password.random_string.result}"

![alt text](https://github.com/artmur1/17-01-hw/blob/main/17-1-1-3.png)

![alt text](https://github.com/artmur1/17-01-hw/blob/main/17-1-1-4.png)

6. Команда terraform apply -auto-approve делает пропуск интерактивного утверждения плана перед подачей заявки.

![alt text](https://github.com/artmur1/17-01-hw/blob/main/17-1-1-5.png)

7. Cодержимое файла **terraform.tfstate**

    {
   
      "version": 4,
   
      "terraform_version": "1.5.7",
   
      "serial": 11,
   
      "lineage": "5ee5e1e9-b6a7-f7ec-3ddb-0c70c944315f",
   
      "outputs": {},
   
      "resources": [],
   
      "check_results": null
   
    }
   
8. Не был удалён docker-образ **nginx:latest** потому что была применена команда terraform apply -auto-approve. Эта команда делает пропуск интерактивного утверждения плана перед подачей заявки. Было произведено уничтожение, а затем создание замены docker_container.nginx.

## Доработка

8. Не был удалён docker-образ **nginx:latest**, потому что при создании ресурса "docker_image" с именем "nginx" указан параметр keep_locally со значением true.

![alt text](https://github.com/artmur1/17-01-hw/blob/main/17-1-1-8.png)

keep_locally (Boolean) - если true, образ Docker не будет удален при операции уничтожения. Если это значение ложно, оно удалит изображение из локального хранилища докера при операции уничтожения.

![alt text](https://github.com/artmur1/17-01-hw/blob/main/17-1-1-9.png)

------

## Дополнительное задание (со звёздочкой*)

**Настоятельно рекомендуем выполнять все задания со звёздочкой.** Они помогут глубже разобраться в материале.   
Задания со звёздочкой дополнительные, не обязательные к выполнению и никак не повлияют на получение вами зачёта по этому домашнему заданию. 

### Задание 2*

1. Создайте в облаке ВМ. Сделайте это через web-консоль, чтобы не слить по незнанию токен от облака в github(это тема следующей лекции). Если хотите - попробуйте сделать это через terraform, прочитав документацию yandex cloud. Используйте файл ```personal.auto.tfvars``` и гитигнор или иной, безопасный способ передачи токена!
2. Подключитесь к ВМ по ssh и установите стек docker.
3. Найдите в документации docker provider способ настроить подключение terraform на вашей рабочей станции к remote docker context вашей ВМ через ssh.
4. Используя terraform и  remote docker context, скачайте и запустите на вашей ВМ контейнер ```mysql:8``` на порту ```127.0.0.1:3306```, передайте ENV-переменные. Сгенерируйте разные пароли через random_password и передайте их в контейнер, используя интерполяцию из примера с nginx.(```name  = "example_${random_password.random_string.result}"```  , двойные кавычки и фигурные скобки обязательны!) 
```
    environment:
      - "MYSQL_ROOT_PASSWORD=${...}"
      - MYSQL_DATABASE=wordpress
      - MYSQL_USER=wordpress
      - "MYSQL_PASSWORD=${...}"
      - MYSQL_ROOT_HOST="%"
```

6. Зайдите на вашу ВМ , подключитесь к контейнеру и проверьте наличие секретных env-переменных с помощью команды ```env```. Запишите ваш финальный код в репозиторий.

7. 

------

### Правила приёма работы

Домашняя работа оформляется в отдельном GitHub-репозитории в файле README.md.   
Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

### Критерии оценки

Зачёт ставится, если:

* выполнены все задания,
* ответы даны в развёрнутой форме,
* приложены соответствующие скриншоты и файлы проекта,
* в выполненных заданиях нет противоречий и нарушения логики.

На доработку работу отправят, если:

* задание выполнено частично или не выполнено вообще,
* в логике выполнения заданий есть противоречия и существенные недостатки. 

