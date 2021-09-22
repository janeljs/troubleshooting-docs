## 문제

도커 네트워크 구성 방식에는 3가지가 있는데,
첫 번째는 Bridge 방식, 두 번째는 Host 방식, 세 번째는 컨테이너를 외부 네트워크로부터 격리한 None 형식이다.

그 중 Host 방식으로 네트워크를 구성할 경우 컨테이너가 도커 엔진이 작동하는 host의 ip 주소를 그대로 사용하기 때문에 host의 IP 주소와 컨테이너의 ip 주소가 동일해야 한다. 

그런데 구성한 컨테이너의 ip는 `192.168.65.3`이고, wsl2의 ip는 `192.168.55.89`인 문제가 발생했다. 

#### container
![](https://images.velog.io/images/janeljs/post/5da82f17-fc11-48d2-be2b-4b99f3237d4b/image.png)
#### wsl2
![](https://images.velog.io/images/janeljs/post/26a57931-50a2-4228-abde-1f2d1cc2bc00/image.png)

## 해결

https://github.com/microsoft/WSL/issues/4150#issuecomment-504209723

위의 글을 참고해보면, Microsoft는 wsl2 베타 버전을 도입하면서 기본값으로 설정되어 있던 bridge 네트워크 어댑터를 hyper-v 가상 네트워크 어댑터로 변경했다고 한다. 이 가상 어댑터가 재부팅 할 때마다 wsl2의 IP 주소를 변경하는 것이다. 

부팅마다 wsl2의 tcp 포트를 호스트 os로 포워딩 시키는 방식으로 IP를 고정시켜주는 것과 같은 눈속임(?)을 불러일으킬 수 있다. 


```bash
$remoteport = bash.exe -c "ifconfig eth0 | grep 'inet '"
$found = $remoteport -match '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}';

if( $found ){
  $remoteport = $matches[0];
} else{
  echo "The Script Exited, the ip address of WSL 2 cannot be found";
  exit;
}

#[Ports]

#All the ports you want to forward separated by coma
$ports=@(80,443,10000,3000,5000);


#[Static ip]
#You can change the addr to your ip config to listen to a specific address
$addr='0.0.0.0';
$ports_a = $ports -join ",";


#Remove Firewall Exception Rules
iex "Remove-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' ";

#adding Exception Rules for inbound and outbound Rules
iex "New-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' -Direction Outbound -LocalPort $ports_a -Action Allow -Protocol TCP";
iex "New-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' -Direction Inbound -LocalPort $ports_a -Action Allow -Protocol TCP";

for( $i = 0; $i -lt $ports.length; $i++ ){
  $port = $ports[$i];
  iex "netsh interface portproxy delete v4tov4 listenport=$port listenaddress=$addr";
  iex "netsh interface portproxy add v4tov4 listenport=$port listenaddress=$addr connectport=$port connectaddress=$remoteport";
}
```

1. 먼저 wsl2 머신의 IP 주소를 얻어온다.
2. 이전에 등록되어 있는 포트포워딩 규칙을 제거한 뒤, 포트 포워딩 규칙을 추가한다. 
3. 이전에 추가되어 있던 방화벽 규칙을 제거하고 새로운 방화벽 규칙을 추가한다. 




