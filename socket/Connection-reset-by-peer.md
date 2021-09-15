## 문제
```
client_loop: send disconnect: Connection reset by peer
```
클라이언트가 서버로부터 데이터를 받는 도중에 네트워크 에러가 발생했다는 뜻이다. 
서버는 커넥션을 받아들였고, 요청을 처리하고 클라이언트에게 응답을 보냈지만 서버가 소켓을 닫을 때 클라이언트는 커넥션이 비정상적으로 종료되었다고 받아들이는 상태이다. 
`connection reset by peer`는 지금 접속해있는 사이트가 커넥션을 리셋했다는 뜻이다. 보통 서버 에러가 발생하거나 트래픽이 몰릴 때 발생한다. 

## 해결
- 재시작

--- 


- https://www.ibm.com/support/pages/connection-reset-peer-socket-write-error-error-message#:~:text=A%20connection%20reset%20by%20peer,a%20server%20error%20as%20well.
