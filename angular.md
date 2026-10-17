# Commands learned in Pro Angular 16 (2024)

```sh
npm install --global @angular/cli@16.0.0    # ng is the angular development tools

ng new todo --routing false --style css --skip-git --skip-tests  # create a project
ng serve                                                         # run the project

ng add @angular/material@16.0.0 --defaults  # angular-material is a styling module
                                            # https://fonts.google.com/icons

npm install bootstrap@5.2.3

ng config projects.Primer.architect.build.options.styles '["src/styles.css", "node_modules/bootstrap/dist/css/bootstrap.min.css"]'      # linux shell command
ng config projects.Primer.architect.build.options.styles '[""src/styles.css"", ""node_modules/bootstrap/dist/css/bootstrap.min.css""]'  # windows powershell command

ng serve --open  # also open the browser
```
