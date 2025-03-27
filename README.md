# **Домашнее задание к занятию «Управление доступом»**-***Вуколов Евгений***

## **Цель задания**

В тестовой среде Kubernetes нужно предоставить ограниченный доступ пользователю.

## **Чеклист готовности к домашнему заданию**

1. Установлено k8s-решение, например MicroK8S.
2. Установленный локальный kubectl.
3. Редактор YAML-файлов с подключённым github-репозиторием.

## **Инструменты / дополнительные материалы, которые пригодятся для выполнения задания**

1. Описание RBAC.
2. Пользователи и авторизация RBAC в Kubernetes.
3. RBAC with Kubernetes in Minikube.

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


# ** Решение **

- Создаю namespace netology-rbak для этого задания: 

- ![scrin](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/Снимок%20экрана%202025-03-27%20132850.png)

- ![scrin](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/Снимок%20экрана%202025-03-27%20140944.png)


1. Создание и подписание SSL-сертификата для подключения к кластеру:

```
openssl x509 -req -in user1.csr \
    -CA /var/snap/microk8s/current/certs/ca.crt \
    -CAkey /var/snap/microk8s/current/certs/ca.key \
    -CAcreateserial -out user1.crt -days 30
```

- ![scrin](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/Снимок%20экрана%202025-03-27%20140808.png)

2. Настройка kubectl для user1 в MicroK8s, добавляем пользователя в kubectl-контекст:

```
microk8s kubectl config set-credentials user1 \
    --client-certificate=user1.crt --client-key=user1.key

Создаю: контекст для пользователя:

microk8s kubectl config set-context user1-context \
    --cluster=microk8s-cluster --user=user1 --namespace=netology-rbak

Переключаюсь на контекст user1:

microk8s kubectl config use-context user1-context

```

- ![scrin](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/Снимок%20экрана%202025-03-27%20141513.png)

3. Создаю Role и RoleBinding для доступа к логам и описанию подов и запускаю:

- ![scrin](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/Снимок%20экрана%202025-03-27%20142402.png)

4. Создаю pod test-pod в namespace netology-rbak для тестирования, переключаюсь на пользователя user1 и проверяю, может ли user1 читать логи и описания подов: : 

- ![scrin](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/Снимок%20экрана%202025-03-27%20144101.png)

- Чтение логов pod: 

- ![scrin](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/Снимок%20экрана%202025-03-27%20144454.png)

- Описание pod:

- ![scrin](https://github.com/Evgenii-379/2.4-2.4.md/blob/main/Снимок%20экрана%202025-03-27%20144519.png)



































