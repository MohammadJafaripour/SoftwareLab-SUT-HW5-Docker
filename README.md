# درس پنجم - آموزش اولیه داکر (Docker)
### محمدامین حیدری 401170553

### محمد جعفری‌پور 401105797

### LINK: https://hamgit.ir/mjafaripoursws/swelab-hw5

---

## ۱. مقدمه و اهداف آزمایش

در توسعه نرم‌افزارهای مدرن، معماری میکروسرویس و جداسازی وظایف (Separation of Concerns) به یکی از استانداردهای اصلی صنعت تبدیل شده است. ابزار **داکر (Docker)** با فراهم کردن امکان کانتینری‌سازی، به توسعه‌دهندگان اجازه می‌دهد تا نرم‌افزار و تمام وابستگی‌های محیطی آن را در یک بسته ایزوله و مستقل از سیستم‌عامل میزبان اجرا کنند.

هدف اصلی این آزمایش، آشنایی عملی با استقرار یک ساختار چند کانتینری (Multi-container) شامل یک سرویس سرور و یک سرویس کلاینت، برقراری ارتباط شبکه‌ای امن و داخلی میان آن‌ها بدون افشای پورت‌های غیرضروری به محیط خارج، و کار با ابزار مدیریت کانتینر **Docker Compose** است. همچنین در این آزمایش مفاهیم پایه‌ای انتقال پورت (Port Forwarding)، بررسی وضعیت کانتینرها از طریق خط فرمان و لاگ‌گیری مورد ارزیابی قرار گرفته‌اند.

---

## ۲. ساختار و معماری پروژه

پروژه پیاده‌سازی شده شامل دو جز اصلی پایتونی است که بدون تغییر در سورس‌کد اصلی، در بستر داکر مستقر شده‌اند:

- **برنامه سرور (server.py):** یک HTTP سرور بومی پایتون که بر روی پورت داخلی `80` کانتینر مستقر شده و در پاسخ به درخواست‌های `GET`، پیام تایید ارسال می‌کند.
- **برنامه کلاینت (client.py):** اسکریپتی که پورت و آدرس سرور مقصد را از طریق متغیر محیطی `SERVER_HOST` دریافت کرده و در فواصل زمانی ۳ ثانیه‌ای، مجموعاً ۵ درخواست به سرور ارسال می‌کند تا پاسخی دریافت و چاپ کند.
- **ارکستراسیون با Docker Compose:** فایل `docker-compose.yml` وظیفه ساخت ایمیج‌ها بر پایه توزیع سبک `python:3.10-alpine`، تنظیم متغیرهای محیطی کلاینت، برقراری شبکه پیش‌فرض داخلی و فوروارد کردن پورت `8000` سیستم میزبان به پورت `80` سرور را بر عهده دارد.

---

## ۳. گام اول: استقرار و اجرای پروژه (Phase 1)

با استفاده از دستور `docker compose up` ساختار میکروسرویسی پروژه راه‌اندازی گردید. طبق خروجی لاگ‌های ثبت‌شده، هر دو ایمیج با موفقیت ساخته شده و کانتینرها در یک شبکه ایزوله مشترک قرار گرفتند:

```Dockerfile
FROM python:3.10-alpine



WORKDIR /app



COPY client.py .



CMD ["python", "client.py"]
```

```Dockerfile
FROM python:3.10-alpine



WORKDIR /app



COPY server.py .



CMD ["python", "server.py"]
```

```docker-compose.yml
services:

  my-server:

    build:

      context: ./server

    ports:

      - "8000:80"



  my-client:

    build:

      context: ./client

    environment:

      - SERVER_HOST=my-server

    depends_on:

      - my-server
```

```bash
[+] up 5/5
 ✔ Image hw5-my-client       Built                                                            7.3s
 ✔ Image hw5-my-server       Built                                                            7.3s
 ✔ Network hw5_default       Created                                                          0.1s
 ✔ Container hw5-my-server-1 Created                                                          0.1s
 ✔ Container hw5-my-client-1 Created                                                          0.1s
Attaching to my-client-1, my-server-1
```

بلافاصله پس از ایجاد کانتینرها، کلاینت با استفاده از نام سرویس سرور (`my-server`) که به عنوان DNS داخلی در شبکه داکر رزولوشین می‌شود، به آن متصل شده و ۵ درخواست را با موفقیت ارسال و پاسخ دریافت نمود:

```Bash
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

## ۴. گام دوم: بررسی خروجی و تعامل با داکر (Phase 2)

### ۱. تست صحت عملکرد پورت فورواردینگ با curl

جهت اطمینان از صحت نگاشت پورت `8000:80` در فایل کامپوز، در سیستم میزبان با استفاده از خط فرمان و ابزار `curl` درخواستی به سرویس ارسال شد. دریافت وضعیت `200 OK` و متن پاسخ سرور داکر، نشان‌دهنده دسترسی صحیح لایه بیرونی به سرویس داخلی کانتینر است:

```Bash
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

### ۲. بررسی لاگ‌های اختصاصی کانتینر کلاینت

با اجرای دستور `docker logs hw5-my-client-1`، فرآیند اجرای کلاینت به طور مجزا بررسی شد که تایید می‌کند مکانیزم زمان‌بندی ۳ ثانیه‌ای و دریافت پاسخ به درستی پایان یافته و کانتینر با کد وضعیت صفر (موفقیت‌آمیز) خارج شده است:

```Bash
PS C:\Users\mjafa\OneDrive\Documents\HW2\SoftwareLab\HW5> docker logs hw5-my-client-1
The client started. Attempting to connect to the server at address: http://my-server:80
[Request 1] Response received from server: Hello! The answer was sent from the Docker server container.
[Request 2] Response received from server: Hello! The answer was sent from the Docker server container.
[Request 3] Response received from server: Hello! The answer was sent from the Docker server container.
[Request 4] Response received from server: Hello! The answer was sent from the Docker server container.
[Request 5] Response received from server: Hello! The answer was sent from the Docker server container.
```

### ۳. ورود به کانتینر سرور و پایش فایل‌ها

برای دسترسی به ساختار درونی کانتینر سرور، ابتدا مشخصات آن با دستور `docker ps` استخراج گردید:

```Bash
PS C:\Users\mjafa\OneDrive\Documents\HW2\SoftwareLab\HW5> docker ps
CONTAINER ID   IMAGE           COMMAND              CREATED          STATUS          PORTS                                     NAMES
2e1402b4761b   hw5-my-server   "python server.py"   14 minutes ago   Up 14 minutes   0.0.0.0:8000->80/tcp, [::]:8000->80/tcp   hw5-my-server-1
```

سپس با اجرای دستور تعاملی `docker exec` وارد کانتینر سرور شده و دستور `ls -la` را برای پایش وضعیت فایل‌های کپی‌شده اجرا کردیم:

```Bash
PS C:\Users\mjafa\OneDrive\Documents\HW2\SoftwareLab\HW> docker exec -it hw5-my-server-1 ls -la
total 12
drwxr-xr-x    1 root     root          4096 Jun 13 11:45 .
drwxr-xr-x    1 root     root          4096 Jun 13 12:10 ..
-rwxr-xr-x    1 root     root           693 Jun 13 11:34 server.py
```

خروجی به وضوح نشان می‌دهد فایل سورس‌کد `server.py` به درستی در دایرکتوری اصلی کانتینر مستقر گردیده است.

## ۵. پاسخ به پرسش‌های مفهومی آزمایش

### ۱. وظیفه فایل docker-compose.yml چیست و چه زمانی به جای دستور docker run از آن استفاده می‌کنیم؟

وظیفه این فایل، تعریف، پیکربندی و مدیریت همزمان برنامه‌های چند کانتینری (Multi-container) به صورت ساختاریافته و Declarative است. زمانی که پروژه ما از چند سرویس وابسته به هم (مثل یک کلاینت، یک سرور و یک پایگاه داده) تشکیل شده باشد، به جای اجرای دستی چندین دستور طولانی و مجزای `docker run` همراه با تنظیمات پیچیده شبکه و متغیرهای محیطی، از Docker Compose استفاده می‌کنیم تا کل معماری سیستم را در یک فایل متنی تنظیم کرده و کل پروژه را تنها با یک دستور واحد (`docker compose up`) مدیریت و راه‌اندازی کنیم.

### ۲. ابزار Kubernetes برای انجام چه کارهایی استفاده می‌شود و چه رابطه‌ای با داکر دارد؟

کوبرنتیز (Kubernetes یا K8s) ابزاری برای خودکارسازی استقرار، مدیریت و مقیاس‌پذیری کانتینرها در سطح شبکه و کلاسترهای چند سروره است؛ داکر کانتینرها را روی یک سیستم می‌سازد و اجرا می‌کند، اما کوبرنتیز وظیفه هماهنگ‌سازی و مدیریت آن‌ها را در چندین سرور مختلف بر عهده دارد.

### ۳. در داکر مفاهیم Image و Container و Volume را توضیح دهید.

- **ایمیج (Image):** یک قالب یا پکیج آماده، غیرقابل تغییر (Read-only) و شامل تمام کدهای برنامه، ران‌تایم، کتابخانه‌ها، متغیرها و تنظیمات لازم برای اجرای یک نرم‌افزار است.
- **کانتینر (Container):** یک نمونه در حال اجرا (Live Instance) و کاملا ایزوله از یک ایمیج است. کانتینر یک لایه خواندن/نوشتن (Writable Layer) موقت به ایمیج اضافه کرده و برنامه را عملاً اجرا می‌کند.
- **ولوم (Volume):** مکانیزمی استاندارد برای ذخیره‌سازی داده‌های پایدار (Persistent Data) خارج از چرخه حیات کانتینر است. از آنجایی که داده‌های داخل کانتینر با حذف آن از بین می‌روند، ولوم‌ها داده‌ها را روی سیستم میزبان (Host) حفظ می‌کنند تا با نابودی کانتینر، اطلاعات حیاتی پاک نشوند.

## ۶. نتیجه‌گیری

در این آزمایش، مفاهیم شبکه داخلی داکر و نحوه ارتباط کانتینرها از طریق ساختار DNS داخلی سیستم به طور عملی پیاده‌سازی و ارزیابی شد. استفاده از Docker Compose گام بزرگی در جهت تسهیل فرآیند اتوماسیون فرآیندهای دواپس (DevOps) محسوب می‌شود. پروژه با موفقیت مستقر شده و تست‌های مربوطه صحتِ عملکرد ایزولاسیون شبکه و ارتباط صحیح کلاینت-سرور را تایید نمودند.
