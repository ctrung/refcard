## Glossaire 

Acteurs
- client
- authorization server (AS)
- resource owner (RO)
- protected resource (aka resources server)

Composants
- (access) token
- scopes : limiter les accès d'un client. String de valeurs séparées par le caractère espace
- authorization grants (aka flows) : procédé d'obtention de l'access token

Communications 
- front channel : communication indirecte entre deux composants via les redirections HTTP et passage d'infos via les URL params (non sécurisée car accessible par le navigateur, cad stocké dans l'historique de navigation, accessible par les plugins, urls stockées dans les logs, etc...) 
- back channel : communication point à point directe entre deux composants (sécurisé) 

Autres
- **confidential client** : client capable de stocker de manière sécurisé un secret (eg. service backend, webapp)
- **public client** : client qui ne garantit pas la sécurité d'un secret (eg. browser, native-app)
- **bearer token** : token utilisable par n'importe qui
- **opaque token** : OAuth2 n'impose pas de format pour les access token, celui ci peut être opaque pour le client et le resource server.
- **configuration time secret** : se réfère à un secret établi à l'avance (eg. client secret)
- **runtime secret** : se réfère à un secret établi au runtime (eg. access token) 


## Flows (authorization grant types)

- **Authorization code** : le client doit passer par un code d'autorisation pour obtenir l'access token (`/authorize` + `response_type=code`, puis `/token` + `grant_type=authorization_code`)
- **Implicit** : le client obtient directement le token (`/authorize` + `response_type=token` seulement). A l'origine, conçu pour les usecases SPA (client dans le browser aka in-browser applications). Non recommandé car limité (non sécurisation du client-secret, pas de refresh token, communications front channel exclusivement)
- **Client** : le resource owner et le client sont confondus. Conçu pour les échanges inter-services backend. Le client obtient directement un token via un canal back channel (`/token` + `grant_type=client_credentials`))
- **Password** (aka resource owner credentials grant type) : à utiliser en dernier ressort car il s'agit de l'anti pattern "ask for the keys". Echange de ses credentials contre un token (`/token` + `grant_type=password`)

## Pièges à éviter

`Client` :

**Attaques CSRF (Cross Site Request Forgery) sur le client** en substituant un code d'autorisation forgé par le programme malveillant. La parade consiste à introduire un paramètre `state` généré par le client que le serveur d'autorisation doit retourner lors de l'envoi du token. Le client détecte ainsi si la demande du token provient rellement de lui même.

**Flow inadapté au usecase**

**Token hijacking via des `redirect_uri` trop souples enregistrées sur le serveur d'autorisations** : la recommandation est d'utiliser un _exact matching_, éviter les wildcards autant que possible. Conséquences liées au token hijacking : vol du code d'autorisation via l'en-tête HTTP referer `Referer` sur une code authorization flow, vol du token si le client possède un point d'entrée de type Open Redirector (eg. /redirect?goto=) sur un implicit flow.

**Eviter de passer l'access token au travers du paramètre de requête `access_token` au sein d'une URI** : privilégier le header `Authorization: Bearer access_token_value`. Cela évite de retrouver l'access token dans les `access.log` du serveur, sur les forums en cas de copier-coller, d'être retransmis via l'en-tête HTTP `Referer`.

`Resources server` :

**Attaques XSS** : Les API endpoints d'une ressource doivent nettoyer les inputs pour éviter l'injection de scripts au sein des réponses retrounées aux navigateurs. Aussi, l'utilisation judicieuse des headers `content-type : application/json`, `X-Content-Type-Options : nosniff`, `X-XSS-Protection : 1, mode=block` aide à mitiger ces attaques. Surtout, il est recommandé de ne pas supporter l'envoi du token au travers du paramètre d'URI `access_token` en faveur du header `Authorization: Bearer` quand pour garantir qu'un appel issu d'une attaque XSS ne soit pas envoyée.

**Ajout des en-têtes CORS pour les appels inter-domaines client et resources server** : Le RS doit retourner le header `Access-Control-Allow-Origin` pour que le navigateur autorise les appels inter-domaines.

**Rejeu du token** : Eviter la divulgation du token en forçant l'utilisation de HTTPS. Le RS renvoit le header `Strict-Transport-Security` dans sa réponse (HTTP Strict Transport Security (HSTS), RFC6797). Lors des appels suivants, le navigateur effectue un HTTP Redirect 307 vers l'url en HTTPS.

`Authorization server` : 

**Un code d'autorisation ne peut être échangé qu'une fois** : Parce que celui ci est transmis au travers de l'URL lors du redirect HTTP et donc persisté dans l'historique du navigateur, son usage doit être unique. Aussi, il doit être lié à un unique client-id pour éviter toute utilisation détournée. 

**Validation de l'URI de redirection** : La seule alternative sure est l'`exact matching`. Eviter le `subdomain matching` et le `subdirectory matching` quand c'est possible.

**Validation de l'URI de redirection bis** : La validation précédente a lieu lors de la demande du code d'autorisation. Celle là a lieu lors de la demande du token et doit matcher la `redirect_uri` proposée par le client lors de la demande du code d'autorisation. 

