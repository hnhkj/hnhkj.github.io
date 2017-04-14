# socket編程


## linux socket編程

**創建套接字**

```
#include <sys/types.h>
#include <sys/socket.h>

int socket(int domain, int type, int protocol);

```

* domain
    * AF_UNIX
    * AF_INET - ARPA因特網協議(UNIX網絡套接字)　常用
    * AF_ISO
    * AF_NS
    * AF_IPX
    * AF_APPLETALK
* type
    * SOCK_STREAM  - TCP/IP
    * SOCK_DGRAM   - UDP/IP
* protocol
    * default 0

**套接字地址**

```
struct sockaddr_un {
    sa_family_t sun_family;    /* AF_UNIX */
    char        sun_path[];    /* pathname */
};
```

**命名套接字**

```
#include <sys/socket.h>

int bind(int socket, const struct sockaddr *address, size_t address_len);

```

* socket
* address
* address_len

**創建套接字隊列**

```
#include <sys/socket.h>

int listen(int socket, int backlog);
```


**接受連接**

```
#include <sys/socket>

int accept(int socket, struct sockaddr *address, size_t *address_len);
```

**請求連接**

```
#include <sys/socket.h>

int connect(int socket, const struct sockaddr *address, size_t address_len);
```

**關閉套接字**

close

## function

#### 



