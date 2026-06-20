# kubernetis-dz09
Создание кластера K8S

https://github.com/kelseyhightower/kubernetes-the-hard-way

Создаем машины, генерируем один внешний сертификат [text](external-shared-key.priv) - он изменен, для безопасности, который будет использоваться для доступа внутри кластера и находиться на jump хосте.


Развёрнутые ВМ с ОС Ubuntu 20.04-lts.

[text](YC-Cloud.txt)  -  процесс развертывания


Задание 1. Установить кластер k8s с 1 master node

![alt text](image.png)

Подготовка работы кластера из 5 нод: 1 мастер и 4 рабочие ноды.

![alt text](image-1.png)

Подготавливаем машины

![alt text](image-2.png)

![alt text](image-3.png)

![alt text](image-4.png)

Отключил swap
![alt text](image-5.png)

Требования сетевого стека выполнены
![alt text](image-6.png)

Создаем конфиг
![alt text](image-7.png)

![alt text](image-8.png)

![alt text](image-9.png)

Итого:
сеть и hostname корректны
swap отключён
kernel modules загружены
sysctl настроен
containerd установлен и работает на master

Окружение полностью готово к установке Kubernetes.

Установка пакетов

![alt text](image-10.png)

![alt text](image-11.png)

Инициализация кластера
![alt text](image-12.png)

Скопировали конфиг
```
root@k8s-master-01:~# mkdir -p $HOME/.kube
root@k8s-master-01:~# 
root@k8s-master-01:~# cp /etc/kubernetes/admin.conf $HOME/.kube/config
root@k8s-master-01:~# 
root@k8s-master-01:~# chown $(id -u):$(id -g) $HOME/.kube/config
root@k8s-master-01:~# 
root@k8s-master-01:~# 
root@k8s-master-01:~# cat .kube/config 
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJYzQvalpQOFRaQnN3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TmpBMk1qQXhOekE1TkRsYUZ3MHpOakEyTVRjeE56RTBORGxhTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0V
```

![alt text](image-13.png)

![alt text](image-14.png)


В качестве CRI — containerd.

![alt text](image-15.png)

Проверка kubelet

![alt text](image-16.png)


Запуск etcd производить на мастере.

![alt text](image-17.png)

![alt text](image-18.png)

Переходим к присоединению воркеров

![alt text](image-19.png)

Вводим воркеры

![alt text](image-20.png)

Воркеры включены в кластер успешно

![alt text](image-21.png)


Задание 2* Установить HA кластер

Установить кластер в режиме HA.

Использовать нечётное количество Master-node.

Для cluster ip использовать keepalived или другой способ.

Создаем машины для HA  кластера

![alt text](image-22.png)

Добавим 2 виртуальные машины

![alt text](image-23.png)

Ставим VIP (keepalived)

![alt text](image-24.png)

Конфигурируем приоритет

![alt text](image-25.png)

Запуск  vrrp
![alt text](image-26.png)

![alt text](image-27.png)

Корректировка и перезапуск:
![alt text](image-28.png)

Для балансировки используем прослойку из HARPOXY
![alt text](image-29.png)

Иницируем новый кластер на новом порту, чтобы потом через haproxy проксировать
![alt text](image-30.png)

![alt text](image-31.png)

![alt text](image-32.png)

А так же слушает прокси
![alt text](image-33.png)

Введем в домен кластера еще два мастера )
![alt text](image-34.png)

Траблшутинг
![alt text](image-35.png)

На Cloud  блочиться трафик, видно что локально прошли пакеты а с соседних мастер нод нет

![alt text](image-36.png)

Суть ясна. В YC  напишу запрос, скорее всего есть ограничения.
Может есть решение от YC по балансировке типа NLB  кластера

![alt text](image-37.png)


Спасибо. Отправляю ДЗ.
