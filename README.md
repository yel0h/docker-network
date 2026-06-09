# Docker - realizacja połączeń sieciowych

## Utworzenie sieci
```
PS D:\PAwChO12> docker network create lab12net
a80c2dcb6f484814255ba91ff4cef90e99a60a66e167b07f534194ea4e0f9147
```

## Uruchomienie kontenerów
```
PS D:\PAwChO12> docker run -d --name web1 --network lab12net -p 8081:80 -v D:/PAwChO12/web1:/usr/share/nginx/html:ro -v D:/PAwChO12/logs/web1:/var/log/nginx nginx:latest
7ab8ff3ea97c3d849c1fe4f8f31abb4cb1cd0bd63e69f6d57d7887e812754523
PS D:\PAwChO12> docker run -d --name web2 --network lab12net -p 8082:80 -v D:/PAwChO12/web2:/usr/share/nginx/html:ro -v D:/PAwChO12/logs/web2:/var/log/nginx nginx:latest
f79ab204792f64e07655f264f1c02a41ac4fff922730652f8bd33a7f2dff744d
PS D:\PAwChO12> docker run -d --name web3 --network lab12net -p 8083:80 -v D:/PAwChO12/web3:/usr/share/nginx/html:ro -v D:/PAwChO12/logs/web3:/var/log/nginx nginx:latest
31f19c536e27de20d90f15a88f0c729f7760ab3d22fdc94528353b6db04a4e96
```

## Sprawdzenie uruchomionych kontenerów
```
PS D:\PAwChO12> docker ps
CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS          PORTS                                         NAMES
31f19c536e27   nginx:latest      "/docker-entrypoint.…"   12 minutes ago   Up 12 minutes   0.0.0.0:8083->80/tcp, [::]:8083->80/tcp       web3
f79ab204792f   nginx:latest      "/docker-entrypoint.…"   12 minutes ago   Up 12 minutes   0.0.0.0:8082->80/tcp, [::]:8082->80/tcp       web2
7ab8ff3ea97c   nginx:latest      "/docker-entrypoint.…"   13 minutes ago   Up 13 minutes   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp       web1
```

## Sprawdzenie sieci
```
PS D:\PAwChO12> docker network inspect lab12net
...
        "Containers": {
            "31f19c536e27de20d90f15a88f0c729f7760ab3d22fdc94528353b6db04a4e96": {
                "Name": "web3",
                "EndpointID": "826dd643e1913efb4ea6b12ad45b6197d7883d1862c8b18797d6a65137adba7d",
                "MacAddress": "22:8f:42:7f:97:d0",
                "IPv4Address": "172.18.0.4/16",
                "IPv6Address": ""
            },
            "7ab8ff3ea97c3d849c1fe4f8f31abb4cb1cd0bd63e69f6d57d7887e812754523": {
                "Name": "web1",
                "EndpointID": "c4e836bd0693d3f32228cdba960b16a7d00abdffb86d2293268b3fb161e0b22e",
                "MacAddress": "5e:5a:37:cf:7a:68",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            },
            "f79ab204792f64e07655f264f1c02a41ac4fff922730652f8bd33a7f2dff744d": {
                "Name": "web2",
                "EndpointID": "9fcd77a889e104419f0df20ce3d225531d995c8802dc04efc6a3cb2d3c39a455",
                "MacAddress": "aa:9d:97:b1:f4:91",
                "IPv4Address": "172.18.0.3/16",
                "IPv6Address": ""
            }
        },
...
```

## Sprawdzenie działania serwerów
```
PS D:\PAwChO12> curl.exe http://localhost:8081
<h1>Laboratorium 12</h1>
<p>Imię i nazwisko: Michał Choina</p>
<p>Serwer: web1</p>
PS D:\PAwChO12> curl.exe http://localhost:8082
<h1>Laboratorium 12</h1>
<p>Imię i nazwisko: Michał Choina</p>
<p>Serwer: web2</p>
PS D:\PAwChO12> curl.exe http://localhost:8083
<h1>Laboratorium 12</h1>
<p>Imię i nazwisko: Michał Choina</p>
<p>Serwer: web3</p>
```

## Sprawdzenie logów
```
PS D:\PAwChO12> dir logs/web1


    Directory: D:\PAwChO12\logs\web1


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        09.06.2026     21:31            441 access.log
-a----        09.06.2026     21:31            885 error.log
PS D:\PAwChO12> dir logs/web2


    Directory: D:\PAwChO12\logs\web2


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        09.06.2026     21:31            441 access.log
-a----        09.06.2026     21:31            885 error.log
PS D:\PAwChO12> dir logs/web3


    Directory: D:\PAwChO12\logs\web3


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        09.06.2026     21:31            441 access.log
-a----        09.06.2026     21:31            885 error.log
```

Zawartość `logs/web1/access.log`:
```
172.18.0.1 - - [09/Jun/2026:19:31:58 +0000] "GET / HTTP/1.1" 200 86 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/147.0.0.0 Safari/537.36 OPR/131.0.0.0" "-"
172.18.0.1 - - [09/Jun/2026:19:31:59 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8081/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/147.0.0.0 Safari/537.36 OPR/131.0.0.0" "-"
```

## Sprawdzenie wolumenów web1
```
PS D:\PAwChO12> docker inspect web1
...
        "Mounts": [
            {
                "Type": "bind",
                "Source": "D:/PAwChO12/web1",
                "Destination": "/usr/share/nginx/html",
                "Mode": "ro",
                "RW": false,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "D:/PAwChO12/logs/web1",
                "Destination": "/var/log/nginx",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],
...
```
