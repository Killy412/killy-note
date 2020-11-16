## JDK密钥生成

- 生成私钥和公钥

```
生成私钥：ssh-keygen -t rsa -b 4096 -f ${private}.key -C ""
生成公钥：openssl rsa -in ${private}.key -pubout -outform PEM -out ${public}.key.pub
转换格式：openssl pkcs8 -topk8 -inform PEM -in jwtRS256.key -outform pem -nocrypt -out pkcs8.pem
```

