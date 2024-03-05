## Links

docs \
https://nodejs.dev/learn \
https://docs.npmjs.com/

npm semver \
https://nodejs.dev/learn/semantic-versioning-using-npm \
https://semver.npmjs.com/ (calculator)

npm \
https://www.lucidchart.com/techblog/2017/03/15/package-management-stop-using-version-ranges/


## npm

package == module (installed in *node_modules* folder)

Semver operators
- `~` : patch releases accepted, eg 1.0.x 
- `^` : releases that don't change the leftmost non-zero number, eg. 0.1.x or 1.x.y

package.json \
https://docs.npmjs.com/cli/v8/configuring-npm/package-json \
https://nodejs.dev/learn/the-package-json-guide

package-lock.json \
https://docs.npmjs.com/cli/v8/configuring-npm/package-lock-json \
https://nodejs.dev/learn/the-package-lock-json-file


Background of package-lock.json

- before : npm-shrinkwrap
- npm@5.0.0 : introducing *package-lock.json*
  https://blog.npmjs.org/post/161081169345/v500.html
- npm@5.1.0 : *package-lock.json* is modified when *package.json* changes
- npm@5.4.2 : *package-lock.json* intended behaviour 
  https://github.com/npm/npm/issues/17979#issuecomment-332701215
- npm@5.6.0 : fix a cross platform bug in npm@5.4.2 
  https://github.com/npm/npm/issues/18712  
- npm@5.7.0 : introducing *npm ci* for faster, more reliable builds
  https://docs.npmjs.com/cli/v8/commands/npm-ci
  https://blog.npmjs.org/post/171556855892/introducing-npm-ci-for-faster-more-reliable

```
npm -v
npm help
```


```
npm config set loglevel ... :		# write to ~/.npmrc

npm doctor 				# check npm env

npm init

npm install <package_name> 		# project 
npm install -g <package_name> 		# globally 

npm install <package_name>@latest
npm install <package_name>@<version>

npm install <package_name> --save	# write to package.json
npm install <package_name> --save-dev	# write to package.json


npm install --legacy-peer-deps          # since NPM 7+, keep NPM 3~6 old behaviour, ie. does not download peer dependencies (developer is responsible to download it manually then) 
                                        # https://weekendprojects.dev/posts/how-to-use-npm-legacy-peer-deps-command/#introduction

npm list 				# list project packages
npm list -g <package>			# list installed package info

npm outdated 				# check updatable packages

# set npm init defaults
npm set init.author.email "email"
npm set init.author.name "name"
npm set init.license "license"

npm uninstall --save <package_name> # write to package.json

npm update		# usecase 1 : checkout existing project
			# usecase 2 : update packages

npm view <package>	# package info

npx cowsay Hi		# execute a package without install
```

## Peer dependencies

Comprendre les peer dependencies et l'histoire des problématiques engendrées :
- https://weekendprojects.dev/posts/how-to-use-npm-legacy-peer-deps-command/#what-is-a-peer-dependency-anyway
- https://angularindepth.com/posts/1187/npm-peer-dependencies
- https://github.blog/2021-02-02-npm-7-is-now-generally-available/#peer-dependencies

npm 4~6 : `npm i` affichait un avertissement sur les éventuels conflits de peer dependencies. Elles n'étaient pas automatiquement installées et c'était à l'utilisateur de le faire manuellement. 

npm 7+  : `npm i` installe désormais les peer dependencies et par défaut bloque si un conflit est détecté. Les options `--force` et `--legacy-peer-deps` peuvent être utilisées comme contournement pour respectivement ignorer (une version est favorisée) ou ne rien faire (comme avant npm 7, c'est alors à l'utilisateur de les installer manuellement). 

## Proxy

Fichier .npmrc
```
proxy=
https-proxy=
```
