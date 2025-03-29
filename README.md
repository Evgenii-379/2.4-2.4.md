# **Домашнее задание к занятию «Управление доступом»**-***Вуколов Евгений***

## **Цель задания**

В тестовой среде Kubernetes нужно предоставить ограниченный доступ пользователю.

## **Чеклист готовности к домашнему заданию**

1. Установлено k8s-решение, например MicroK8S.
2. Установленный локальный kubectl.
3. Редактор YAML-файлов с подключённым github-репозиторием.

## **Инструменты / дополнительные материалы, которые пригодятся для выполнения задания**

1. [Описание](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) RBAC. 
2. [Пользователи и авторизация RBAC в Kubernetes](https://habr.com/ru/company/flant/blog/470503/)
3. [RBAC with Kubernetes in Minikube](https://medium.com/@HoussemDellai/rbac-with-kubernetes-in-minikube-4deed658ea7b)

## **Задание 1. Создайте конфигурацию для подключения пользователя**

1. Создайте и подпишите SSL-сертификат для подключения к кластеру.
2. Настройте конфигурационный файл kubectl для подключения.
3. Создайте роли и все необходимые настройки для пользователя.
4. Предусмотрите права пользователя. Пользователь может просматривать логи подов и их конфигурацию (kubectl logs pod <pod_id>, kubectl describe pod <pod_id>).
5. Предоставьте манифесты и скриншоты и/или вывод необходимых команд.

## **Правила приёма работы**

1. Домашняя работа оформляется в своём Git-репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода необходимых команд kubectl, скриншоты результатов.
3. Репозиторий должен содержать тексты манифестов или ссылки на них в файле README.md.



# **Решение**

- Создаю namespace netology-rbak для этого задания: 

- ![scrin](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/Снимок%20экрана%202025-03-27%20132850.png)



1. Создание и подписание SSL-сертификата для подключения к кластеру:

```
openssl genrsa -out user-key.pem 2048                                                                   # сгенерировать ключ пользователя

openssl req -new -key user-key.pem -out user.csr -subj "/CN=user1/O=netology"                           # Создать запрос на сертификат

openssl x509 -req -in user.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out user.crt -days 30          #  Подписать запрос корневым CA Kubernetes (MicroK8s)

```



2. Настройка kubectl для user1 в MicroK8s, добавляем пользователя в kubectl-контекст:

```

kubectl config set-credentials user1 --client-certificate=user.crt --client-key=user-key.pem                        # Добавляю пользователя в kubeconfig

kubectl config set-context user1-context --cluster=microk8s-cluster --namespace=netology-rbac --user=user1          # Добавляем контекст для пользователя

kubectl config use-context user1-context                                                                            # Переключаемся на новый контекст

```
 

- ![scrin](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/Снимок%20экрана%202025-03-29%20114946.png)


3. Создаю Role и RoleBinding для доступа к логам и описанию подов и запускаю:

- ![scrin](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/Снимок%20экрана%202025-03-29%20134214.png)

- ![scrin](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/Снимок%20экрана%202025-03-27%20142402.png)

4. Создаю pod test-pod в namespace netology-rbak для тестирования, переключаюсь на пользователя user1 и проверяю, может ли user1 читать логи и описания подов: : 

- Чтение логов pod и описание pod:

- ![scrin](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/Снимок%20экрана%202025-03-29%20132201.png)

- ![scrin](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/Снимок%20экрана%202025-03-29%20132307.png)

- Проверка доступных прав у пользователя : 

- ![scrin](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/Снимок%20экрана%202025-03-29%20133411.png)


- Ссылки на манифесты, ключ и сертификат:

[nginx-test.yaml](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/config.yaml/nginx-test.yaml)

[pod-reader-binding.yaml](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/config.yaml/pod-reader-binding.yaml)

[pod-reader-role.yaml](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/config.yaml/pod-reader-role.yaml)

[ca.crt](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/certs/ca.crt)

[user-key.pem](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/certs/user-key.pem)

[user.crt](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/certs/user.crt)





















































