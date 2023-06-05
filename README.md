# Kubernetes. Домашнее задание

## Шаги выполнения
1. Создать web-приложение, которое выводит содержимое из папки `app`
2. Собрать приложение в виде Docker image
3. Установить image в кластер Kubernetes
4. Обеспечить доступ к приложению извне кластера

<hr>

**1.** В директории `server` был создан Dockerfile на основе `python:3.10-alpine`. В нем:
- обеспечивается запуск web-сервера от имени пользователя с “uid 1001”;
- создается каталог `/app`, назначенный как WORKDIR;
- в каталог `/app` добавляется файл `hello.html`, содержащий текст “Hello world”;
- описывается команда для запуска web-сервера на 8000 порту.

<hr>

**2.** Далее приложение было собрано в виде Docker image следующей командой:
```
docker build -t flaringmind/server:1.0.0 -t flaringmind/server:latest server
```
Для публикации image в Docker Hub используется команда:
```
docker push flaringmind/server:1.0.0
```

![image](https://github.com/flaringmind/practice-kubernetes/assets/134168160/6a26f11f-df26-4872-9426-5d310cf0f1d2)

<hr>

**3.** Следующим шагом необходимо создать Kubernetes Deployment manifest, запускающий container из созданного image. На основе манифеста, сгенерированного ChatGPT, был создан файл `deployment.yaml`.Результат запроса ChatGPT приведен ниже:
![image](https://github.com/flaringmind/practice-kubernetes/assets/134168160/aff56df3-19bf-43bb-a494-f98e2168cf9c)

Для полученного из ChatGPT результата:
- было подставлено имя созданного Docker image: flaringmind/server:1.0.0,
- были прописаны пути к `hello.html` для livenessProbe и ReadinessProbe,
- была добавлена startupProbe
```
startupProbe:
  httpGet:
    path: /hello.html
    port: 8000
  failureThreshold: 10
  periodSeconds: 5
 ```
Полученный манифест был установлен командой:
```
kubectl apply --filename deployment.yaml --namespace default
```
Проверка осуществляется с помощью следующих команд:
```
kubectl describe pods
kubectl describe deployment web
```
Результат команды `kubectl describe deployment web` приведен ниже:

![image](https://github.com/flaringmind/practice-kubernetes/assets/134168160/d71b0575-39b7-403d-94ef-8e4d1f96bcc9)

<hr>

**4.** Чтобы обеспечить доступ к web-приложению, была использована следующая команда:
```
kubectl port-forward --address 0.0.0.0 deployment/web 8080:8000
```
Чтобы проверить работу web-приложения, перейдем по адресу http://127.0.0.1:8080/hello.html
На странице выводится "Hello world", что свидетельствует о том, что приложение работает должным образом.
