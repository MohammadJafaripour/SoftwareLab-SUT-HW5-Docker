# HW5 Docker


# phase 1


``` bash
[+] up 5/5
 ✔ Image hw5-my-client       Built                                                            7.3s
 ✔ Image hw5-my-server       Built                                                            7.3s
 ✔ Network hw5_default       Created                                                          0.1s
 ✔ Container hw5-my-server-1 Created                                                          0.1s
 ✔ Container hw5-my-client-1 Created                                                          0.1s
Attaching to my-client-1, my-server-1
my-server-1  | 172.18.0.3 - - [13/Jun/2026 11:45:57] "GET / HTTP/1.1" 200 -
my-server-1  | 172.18.0.3 - - [13/Jun/2026 11:46:00] "GET / HTTP/1.1" 200 -
my-server-1  | 172.18.0.3 - - [13/Jun/2026 11:46:03] "GET / HTTP/1.1" 200 -
my-server-1  | 172.18.0.3 - - [13/Jun/2026 11:46:06] "GET / HTTP/1.1" 200 -
my-server-1  | 172.18.0.3 - - [13/Jun/2026 11:46:09] "GET / HTTP/1.1" 200 -
my-client-1  | The client started. Attempting to connect to the server at address: http://my-server:80
my-client-1  | [Request 1] Response received from server: Hello! The answer was sent from the Docker server container.
my-client-1  | [Request 2] Response received from server: Hello! The answer was sent from the Docker server container.
my-client-1  | [Request 3] Response received from server: Hello! The answer was sent from the Docker server container.
my-client-1  | [Request 4] Response received from server: Hello! The answer was sent from the Docker server container.
my-client-1  | [Request 5] Response received from server: Hello! The answer was sent from the Docker server container.
my-client-1 exited with code 0


```


# phase 2

## q1

``` bash
PS C:\Users\mjafa\OneDrive\Documents\HW2\SoftwareLab\HW5> curl http://localhost:8000


StatusCode        : 200
StatusDescription : OK
Content           : Hello! The answer was sent from the Docker server container.
                    
RawContent        : HTTP/1.0 200 OK
                    Content-Type: text/plain; charset=utf-8
                    Date: Sat, 13 Jun 2026 12:11:56 GMT
                    Server: BaseHTTP/0.6 Python/3.10.20
                    
                    Hello! The answer was sent from the Docker server container.
                    
Forms             : {}
Headers           : {[Content-Type, text/plain; charset=utf-8], [Date, Sat, 13 Jun 2026 12:11:56 
                    GMT], [Server, BaseHTTP/0.6 Python/3.10.20]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 61
```



## q2



``` bash
PS C:\Users\mjafa\OneDrive\Documents\HW2\SoftwareLab\HW5> docker logs hw5-my-client-1
The client started. Attempting to connect to the server at address: http://my-server:80
[Request 1] Response received from server: Hello! The answer was sent from the Docker server container.
[Request 2] Response received from server: Hello! The answer was sent from the Docker server container.
[Request 3] Response received from server: Hello! The answer was sent from the Docker server container.
[Request 4] Response received from server: Hello! The answer was sent from the Docker server container.
[Request 5] Response received from server: Hello! The answer was sent from the Docker server container.
```



## q3

 استفاده کنید، ابتدا باید نام یا ID کانتینرِ در حال اجرا را از طریق دستور `docker ps` پیدا کنید.

**مرحله اول: پیدا کردن نام یا ID کانتینر**

```bash
docker ps

```

``` bash
PS C:\Users\mjafa\OneDrive\Documents\HW2\SoftwareLab\HW5> docker ps
CONTAINER ID   IMAGE           COMMAND              CREATED          STATUS          PORTS                                     NAMES
2e1402b4761b   hw5-my-server   "python server.py"   14 minutes ago   Up 14 minutes   0.0.0.0:8000->80/tcp, [::]:8000->80/tcp   hw5-my-server-1
```


**مرحله دوم: اجرای دستور ورود و مشاهده فایل‌ها**

```bash
docker exec -it <container_name> ls -la

```


``` bash
PS C:\Users\mjafa\OneDrive\Documents\HW2\SoftwareLab\HW> docker exec -it hw5-my-server-1 ls -la
total 12
drwxr-xr-x    1 root     root          4096 Jun 13 11:45 .
drwxr-xr-x    1 root     root          4096 Jun 13 12:10 ..
-rwxr-xr-x    1 root     root           693 Jun 13 11:34 server.py
```