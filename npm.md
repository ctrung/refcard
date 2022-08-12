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

npm list 				# list projetc packages

npm outdated 				# check updatable packages

# set npm init defaults
npm set init.author.email "email"
npm set init.author.name "name"
npm set init.license "license"

npm uninstall --save <package_name> # write to package.json

npm update	# usecase 1 : checkout existing project
		# usecase 2 : update packages

npm view 	# package info

npx cowsay Hi	# execute a package without install
```

