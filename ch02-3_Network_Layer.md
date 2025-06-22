# Video Streaming and CDNs




## Content Distribution Networks(CDNs)
### 목적


## Socket Programing

- UPD : ureliable 한 데이터그램
- TCP : reliable, 스트림 객체 byte
### Application Example
1. 

### Socket Programming with UDP
Client와 Server 사이의 연결이 없다.
	데이터 전송 전 행동이 없다.

>전송된 데이터를 잃어 버리거나 ~한다.

### Application 관점
- UDP는 *신용하지않는* 전송을 제공한다.

### Interaction

```C
// Create socket, port = x
serverSocket = socket(AF_INET, SOCK_DGRAM)
```

``` C
// Create socket
clientSocket = socket(AF_INET, SOCK_DGRAM)
```
1. 데이터 그램 생성 with IP server, port = x -> 데이터 그램 전송
2. 데이터 그램 읽기
3. 

### Example: UDP Client
``` python
from socket import *

serverName = 'hostname'
serverPort = 12000

# Create UDP socket for server
clientSocket = socket(AF_INET, SOCK_DGRAM)

# get message
message = input('Input lowercase sentence : ')

# attach server name, port to message
# send into socket
clientSocket.sendto(message.encode(), serverName, serverPort)
# read reply characters from socket into string
modifiedMessage, serverAddress = clientSocket.recvform(2048)

# print
print(modifiedMessage.decode())

clientSocket.close()
```
<br>

### Example: UDP Server
``` python
from socket import *

serverPort = 12000

# Create UDP Socket
serverSocket = socket(AF_INET, SOCK_DGRAM)
# bind socket to local port number 12000
serverSocket.bind(('', serverPort))

print("The server is ready to receive!)

# loop
while True:
	# Read from UDP socket into message, getting client's address
	message, clientAddress = serverSocket.recvform(2048)
	modifiedMessage = message.decode().upper()
	
	# send upper case string back to this client
	serverSocket.sendto(modifiedMessage.encode(), clientAddress)
```

<br>

# Socket Programming
## with TCP
### Client는 Server와 contact
- 서버가 먼저 실행되어 있어야 한다.
- 서버는 socket을 생성, client의 접촉을 기다린다.
### Client와 Server의 접촉
- TCP 소캣 생성

- 클라이언트와 접촉할 때, 서버는 새로운 소캣을 생성, 해당 클라이언트와 소통을 위한 별도의 소켓
	- 서버는 여러 개의 클라이언트와 연결 허용
	- 포트 넘버 제공
### Applicatioin 관점
- TCP는 pipe 전송 제공

## with UDP
