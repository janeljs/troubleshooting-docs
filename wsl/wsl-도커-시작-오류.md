## 문제
`sudo systemctl start docker` 명령어가 작동하지 않았는데, wsl2는 해당 명령어를 지원하지 않는다고 한다.
> Instead of using sudo systemctl start docker use: sudo /etc/init.d/docker start , as of right now we do not have systemd in WSL 2.

## 해결
wsl을 관리자 권한으로 실행한다음 `sudo service docker start`를 실행하니 해결되었다.    
참고: [System has not been booted with systemd as init system](https://github.com/MicrosoftDocs/WSL/issues/457)
