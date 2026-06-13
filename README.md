# HW5 Docker

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