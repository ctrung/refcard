
https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html


# Import a certificate as a trusted certificate

https://docs.oracle.com/javase/tutorial/security/toolsign/rstep2.html

```shell
keytool -import -alias susan -file Example.cer -keystore exampleraystore
```

# List certificates in JRE/JDK cacerts

```shell
# with password provided on cli (defaut : changeit)
keytool -list -v -cacerts -storepass changeit
keytool -list -v -storepass changeit -keystore <$JAVA_HOME/lib/security/cacerts>

# without password provided on cli (type enter at password prompt or provide one, default : changeit)
keytool -list -v -cacerts
keytool -list -v -keystore <$JAVA_HOME/lib/security/cacerts>

``` 

# Print a certificate informations

https://docs.oracle.com/javase/tutorial/security/toolsign/rstep2.html

```shell
# from a file
keytool -printcert -file repo.itextsupport.com.crt # autres format supportés : cer

# from a keystore entry
keytool -list -v -keystore <file, eg. cacerts> -alias repo.itextsupport.com
```
