# Домашнее задание к занятию 21.10 «Как работает сеть в K8s» - Падеев Василий

### Цель задания

Настроить сетевую политику доступа к подам.

### Чеклист готовности к домашнему заданию

1. Кластер K8s с установленным сетевым плагином Calico.

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. [Документация Calico](https://www.tigera.io/project-calico/).
2. [Network Policy](https://kubernetes.io/docs/concepts/services-networking/network-policies/).
3. [About Network Policy](https://docs.projectcalico.org/about/about-network-policy).

-----

### Задание 1. Создать сетевую политику или несколько политик для обеспечения доступа

1. Создать deployment'ы приложений frontend, backend и cache и соответсвующие сервисы.
2. В качестве образа использовать network-multitool.
3. Разместить поды в namespace App.
4. Создать политики, чтобы обеспечить доступ frontend -> backend -> cache. Другие виды подключений должны быть запрещены.
5. Продемонстрировать, что трафик разрешён и запрещён.


### Решение:


С помощью kubeadm создал кластер состоящий из одной мастер ноды и одной воркер ноды, с установленным сетевым плагином Calico. Создал namespace app.  
![answer1](https://github.com/Vasiliy-Ser/K8s_network_21.10/blob/0a7e47b45ba1863e644b8d047a0088847749fec5/png/13.png)  
![answer2](https://github.com/Vasiliy-Ser/K8s_network_21.10/blob/7ad5fa9c076b914f5aeac5c59048cb665471b78d/png/2.png)  
Создал [deployment'ы ](https://github.com/Vasiliy-Ser/K8s_network_21.10/blob/0a7e47b45ba1863e644b8d047a0088847749fec5/src/Deployment_Service.yaml) приложений frontend, backend и cache и соответсвующие сервисы.  
![answer3](https://github.com/Vasiliy-Ser/K8s_network_21.10/blob/7ad5fa9c076b914f5aeac5c59048cb665471b78d/png/3.png)  
Проверим доступность frontend -> backend:  
![answer4](https://github.com/Vasiliy-Ser/K8s_network_21.10/blob/7ad5fa9c076b914f5aeac5c59048cb665471b78d/png/4.png)  
Теперь проверим доступность backend -> frontend:  
![answer5](https://github.com/Vasiliy-Ser/K8s_network_21.10/blob/7ad5fa9c076b914f5aeac5c59048cb665471b78d/png/5.png)  
Применим политики доступа [frontend -> backend](https://github.com/Vasiliy-Ser/K8s_network_21.10/blob/0a7e47b45ba1863e644b8d047a0088847749fec5/src/frontend--backend.yaml) и [backend -> cache](https://github.com/Vasiliy-Ser/K8s_network_21.10/blob/0a7e47b45ba1863e644b8d047a0088847749fec5/src/backend--cache.yaml).   
![answer6](https://github.com/Vasiliy-Ser/K8s_network_21.10/blob/7ad5fa9c076b914f5aeac5c59048cb665471b78d/png/6.png)  
После применения политики доступ разрешен frontend -> backend -> cache. Подключение cache -> frontend запрещено, а подключение backend -> frontend разрещает т. к. запускал перед ограничениями, сетевые политики были применены, соединение могло установиться, а потом TCP-сессия оставалась открытой. Удаление deployment, сервисов, подов, NetworkPolicy и новое создание не помогло.
![answer7](https://github.com/Vasiliy-Ser/K8s_network_21.10/blob/7ad5fa9c076b914f5aeac5c59048cb665471b78d/png/8.png)  
Создал дополнительную политику входящего трафика в frontend [deny-ingress.yaml](https://github.com/Vasiliy-Ser/K8s_network_21.10/blob/0a7e47b45ba1863e644b8d047a0088847749fec5/src/deny-ingress.yaml), чтобы frontend был полностью закрыт для всех внутренних подов. 
![answer8](https://github.com/Vasiliy-Ser/K8s_network_21.10/blob/7ad5fa9c076b914f5aeac5c59048cb665471b78d/png/9.png)  

#### Проверки работы:  
1. frontend -> backend и наоборот:  
![answer9](https://github.com/Vasiliy-Ser/K8s_network_21.10/blob/7ad5fa9c076b914f5aeac5c59048cb665471b78d/png/10.png)  
2. backend -> cache и наоборот:  
![answer10](https://github.com/Vasiliy-Ser/K8s_network_21.10/blob/7ad5fa9c076b914f5aeac5c59048cb665471b78d/png/11.png)  
3. cache -> frontend и наоборот:  
![answer11](https://github.com/Vasiliy-Ser/K8s_network_21.10/blob/7ad5fa9c076b914f5aeac5c59048cb665471b78d/png/12.png)  
 

### Правила приёма работы

1. Домашняя работа оформляется в своём Git-репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода необходимых команд, а также скриншоты результатов.
3. Репозиторий должен содержать тексты манифестов или ссылки на них в файле README.md.
