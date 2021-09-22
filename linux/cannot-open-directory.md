## 문제
```
ls: cannot open directory '.': Permission denied
```
사용자가 디렉토리를 읽을 권한이 없어 발생

## 해결
```
chmod 775 application-name
sudo chmod 775 /var/lib/docker/volumes
```

> 775: The chmod 775 is an essential command that assigns read, write, and execute permission to a specific user, group, or others.


read/write/execute 권한을 추가해주니 해결되었다. 

출처: https://linuxhint.com/what-is-the-meaning-of-chmod-755-and-how-to-execute-and-verify-it/#:~:text=The%20chmod%20775%20is%20an,user%2C%20group%2C%20or%20others.
