### 문제
```
nginx: [emerg] could not build server_names_hash, you should increase server_names_hash_bucket_size: 32
```

### 해결
```
http {
    server_names_hash_bucket_size  64;
    ...
```


---
- http://nginx.org/en/docs/http/server_names.html#optimization
