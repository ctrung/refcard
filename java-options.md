
[Java 21 overview options](https://docs.oracle.com/en/java/javase/21/docs/specs/man/java.html#overview-of-java-options)

[chriswhocodes.com](https://chriswhocodes.com/vm-options-explorer.html) : JITWatch, VM Options Explorer, JaCoLine and other utilities.

[Foojay.io](https://foojay.io/command-line-arguments)


## Flags

### javax.net.ssl.trustStore

SSL/TLS: Use native trustore

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

## Experimental options

### –XX:+PrintFlagsFinal
To check which options are enabled by default.

### –XX:+UseCompressedOops
This option has been enabled by default since Java 6, update 23. 

