## SSL/TLS: Use native trustore

**Windows** \
Useful in corporate environments \
https://stackoverflow.com/questions/684081/importing-ssl-certificate-into-eclipse \
https://stackoverflow.com/questions/41257366/import-windows-certificates-to-java/49011158#49011158 
```
javax.net.ssl.trustStore=NUL
javax.net.ssl.trustStoreType=Windows-ROOT
```

**Linux** \
https://stackoverflow.com/questions/76131942/how-to-make-the-java-runtime-use-os-ca-certchain \
https://stackoverflow.com/questions/46845251/java-trust-unix-certificate-store
