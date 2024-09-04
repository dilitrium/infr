# infr

Спринт 1

ЗАДАЧА

```
Опишите инфраструктуру будущего проекта в виде кода с инструкциями по развертке, нужен кластер Kubernetes и служебный сервер (будем называть его srv):

1 Выбираем облачный провайдер и инфраструктуру.
В качестве облака подойдет и Яндекс.Облако, но можно использовать любое другое по желанию.
Нам нужно три сервера:
два сервера в одном кластере Kubernetes: 1 master и 1 app;
сервер srv для инструментов мониторинга, логгирования и сборок контейнеров.

2 Описываем инфраструктуру.
Описывать инфраструктуру мы будем, конечно, в Terraform.
Совет: лучше создать под наши конфигурации отдельную группу проектов в Git, например, devops.
Пишем в README.md инструкцию по развертке конфигураций в облаке. Никаких секретов в коде быть не должно.

3 Автоматизируем установку.
Надо реализовать возможность установки на сервер всех необходимых нам настроек и пакетов, будь то docker-compose, gitlab-runner или наши публичные ключи для доступа по SSH. Положите код автоматизации в Git-репозиторий.
Результат должен быть такой, чтобы после запуска подобной автоматизации на сервере устанавливалось почти всё, что нужно.

Совсем полностью исключать ручные действия не надо, но в таком случае их надо описать в том же README.md и положить в репозиторий.
```

Решение:

С помощью terraform запускаем создание инфраструктуры в Yandex Cloude, устанавливаем нужное окружение и пакеты:
  - Запускается развертывание сервера srv под сервисные задачи, деплой и сборку:
  ```
  terraform init
  ```
  ```
  terraform apply
  ```
  - В процессе установки создаётся виртуальная машина srv, сервисный аккаунт, сети, сервер srv подготавливается через provisioning-скрипт - ставятся необходимые утилиты:
    ansible, terraform, terragrunt, jq, docker, docker-compose, git, gitlab-runner, kubeadm, kubectl, helm), пакеты и сопутствующие зависимости.
  - Устанавливается окружение для будущего разворачивания k8s кластера, согласно requirements версии 2.9.
  - Автоматически клонируются на srv репозитории (kubernetes_setup и форком kubespray) с кодом развертывания кластера k8s с помощью kubespray. 
    Подкладываем ключи, даём разрешения.
  - Автоматически устанавливается с сервера srv кластер k8s с созданием master-ноды и worker-ноды кластера.
  
Результаты выполнения скриптов развёртывания инфраструктуры и кластера k8s:
![create srv](https://github.com/vajierik/infr/assets/150177457/ec2e3ffb-d459-445a-82dc-ecc7ea8d270d)

![create master-worker](https://github.com/vajierik/infr/assets/150177457/11477be0-ddfb-4a46-afe8-24864f3b5006)

![create cluster k8s](https://github.com/vajierik/infr/assets/150177457/dffff24c-3b9f-4258-a507-b8dc101dc76f)

![vm](https://github.com/vajierik/infr/assets/150177457/a14d52bb-efa5-4d49-81ff-063cf032ccc0)

