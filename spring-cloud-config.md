# Config

NB : From the book Definitive Guide to Spring Batch 2nd ed. chapter 12 (might be inaccurate, since book was written in 2019)

Client side

```xml
<dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
```

Config server side

```yml

spring:
  profiles:
    active: git
  cloud:
    config:
      server:
        git:
          uri: file:///path/to/.spring-cloud/config/

```

Encryption with Spring CLI

```sh
-- example with a string key (RSA public key also supported)
spring encrypt <mysecret> --key <key>
spring decrypt --key <key> <encrypted string>
```

How to use it inside application file ? \
-> preprend with `{cipher}`

```yml
spring:
  datasource:
    ...
    password: '{cipher}abcdefa44d2db148cd788507068e770fa7b64c4d1980ef6ab86cdefabc118def'
```

Run the config server locally : `spring cloud configserver`
