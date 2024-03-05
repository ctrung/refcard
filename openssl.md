## Extension

https://crypto.stackexchange.com/questions/43697/what-are-the-differences-between-pem-csr-key-crt-and-other-such-file-exte

* .crl : liste de révocation de certificats
* .crt or .cer : certificat (ou bundle de CA de confiance sous Linux)
* .csr (ou .req ou .p10) : certificate signing request
* .jks : java keystore
* .key : clé privée
* .p7 or .p7m : message
* .p7b or .p7c : bundle de certificats
* .p7s : signature détachée
* .p8, .pkcs8 : clé privée
* .p12 or .pfx : pkcs12 key store
* .pem : Privacy Enhanced Mail; encoding base64 pouvant contenir tout objet. Historiquement utilisé pour les transferts en mode texte.


## Convertir un certificat

```sh
# DER en PEM
openssl x509 -inform der -in telekom.crt -out telekom.pem
```

## Créer un keystore pkcs12

```sh
openssl pkcs12 -export -in cert.pem -inkey key.pem -out certificate.p12 -name "certificate"
openssl pkcs12 -export -chain -CAfile <cafile>.pem -in <cert>.pem -inkey priv.keystore -out <certificate>.keystore -name ssl -passout pass:<password>
```

## Lister les CAs du système

```sh
# redhat
awk -v cmd='openssl x509 -noout -subject' '/BEGIN/{close(cmd)};{print | cmd}' < /etc/ssl/certs/ca-bundle.crt

# debian 
awk -v cmd='openssl x509 -noout -subject' '/BEGIN/{close(cmd)};{print | cmd}' < /etc/ssl/certs/ca-certificates.crt
```

## Inspecter un certificat d'un nom de domaine

```sh
openssl s_client -showcerts -connect repo.itextsupport.com:443

# avec l'option -proxy <host:port> (v >= 1.1.0)
openssl s_client -showcerts -proxy 192.168.103.115:3128 -connect repo.itextsupport.com:443

```

## Inspecter un certificat fichier

```sh
openssl verify cert.pem

# self signed
openssl verify -CAfile cert.pem cert.pem

# chaine de certificats
cat int1.crt in2.crt > int1int2.crt
openssl verify -CAfile int1int2.crt cert.pem
```

## Installer un CA sur le système 

https://www.hs-schmalkalden.de/en/university/faculties/faculty-of-electrical-engineering/studium/use-of-it/install-ca-certificates-on-linux-systems.html

**NB** : seul le format PEM est reconnu. Si format DER, convertir auparavant.

```sh
# debian
<Copy PEM files to /usr/local/share/ca-certificates>
<Change the file name suffix to “*.crt”.>
update-ca-certificates # update-ca-certificates 

# redhat
<Copy PEM files to /etc/pki/ca-trust/source/anchors>
<Change the file name suffix to “*.pem”.>
update-ca-trust

# manuellement (eg. anciennes versions de centos)
cat foo.crt >>/etc/pki/tls/certs/ca-bundle.crt
```
