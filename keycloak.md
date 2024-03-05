Keycloak is an open source identity and access management (IAM) software since 2014.

- fully customizable pages
- passwords management
- SSO
- OAuth 2.0, OpenID Connect, SAML 2.0
- identity brokering capabilities : AD, LDAP
- scalable, redundancy

**Book** : Keycloak-Identity-and-Access-Management-for-Modern-Applications (2021)


https://oauth.net/
https://openid.net/

## Running on Docker

```
# old
docker run -e KEYCLOAK_USER=admin -e KEYCLOAK_PASSWORD=admin -p 8080:8080 jboss/keycloak

# new (keycloak docs, 06/13/2022)
docker run -p 8080:8080 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin quay.io/keycloak/keycloak:18.0.0 start-dev
```

## URLs

Admin console : 	http://localhost:8080/admin/master/console \
Account console : 	http://localhost:8080/realms/[myrealm]/account/

## Glossary

- client scope : defines a set of claims
- protocol mapper : useful to define a custom claim
- upn : user principal name

## Oauth2, OpenID Connect : must know primer

Tokens : ID token, access token, refresh token

Specifications : 
- Proof Key for Code Exchange (PKCE, RFC 7636) : OAuth 2.0 extension to prevent authorization code interception from exchanging it for an access token.
- Bearer Tokens (RFC 6750)
- Token Introspection (RFC 7662)
- Token Revocation (RFC 7009)
- JSON Web Token (JWT, RFC 7519)
- JSON Web Signature (JWS, RFC 7515)
- JSON Web Encryption (JWE, RFC 7516)
- JSON Web Algorithms (JWA, RFC 7518)
- JSON Web Key (JWK, RFC 7517)

OAuth 2.0 roles : Authorization server, client, resource owner, resource server

OAuth 2.0 Flows :
- Client credential flow : application accessing a resource on its behalf (ie. application is the resource owner)
- Device flow : application is running on a device without a browser or is input constrained (eg. smart TV)
- Authorization code flow : applicable for all other usecases, requests an authorization code to be exchanged with an access and refresh token
- (legacy, not recommanded) Implicit flow : simplified flow for native application. Now considered insecure, not to be used.
- (legacy, not recommanded) Resource Owner Password Credentials flow : user's credentials are collected by the application and passed to the authorization server.  

OpenID roles : end user, relying party (RP) : ie. the application that wants to authenticate the end user, OpenID provider (OP)

OpenID Flows : Authorization code flow : similar to OAuth 2.0 flow to get an ID token, hybrid flow : ID token is returned from the initial request, (legacy, not recommanded) Implicit flow
