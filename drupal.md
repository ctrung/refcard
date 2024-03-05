## Notions

4 types de projets Drupal :
- core
- themes
- modules
- distributions (remplacés par les recipes après Drupal 10 ?)

**Drupal 7 à 8** : refonte massive basée sur Symphony, utilisation du gestionnaire de dépendances Composer, l'adoption du standard [PSR](https://www.php-fig.org/psr/).

## Modules

Organisation :
- [module-name].info.yml : fichier de metadata et dépendances.
- composer.json : fichier metadata de packaging + dépendances drupal.org et écosystème PHP.
- [module-name].module : fichier du code basé sur les hooks (anciennes versions Drupal).
- src/ : sources PHP.
- tests/ : tests PHPUnit (unitaires, kernel, fonctionels, browser, et fonctionnels JavaScript).
- [module-name].install : fichier de gestion des versions du schema.
- modules/ : sous modules (fonctionnalités fortement couplées, optionnelles).

## Thèmes

- Claro: admin thème par défaut.
- Olivero: thème par défaut.
- Stark: thème minimal pour les styles et les fonctionnalités utilisées en debuggage.
- Starter Kit: thème servant de point de départ pour les thèmes custom.

Claro et Olivera remplacent Bartik et Seven dans les versions précédentes.

Organisation :
- info.yml : fichier de metadata.
- libraries.yml : fichiers des librarires CSS et Javascript utilisées.

## Support

https://www.drupal.org/about/core/policies/core-release-cycles/schedule

Sortie des versions mineures : ~ 6 mois \
Sortie des version majeures : ~ 2 ou 3 ans

- Drupal 10.0 : déc. 2022
- Drupal 10.1 : juin 2023
- Drupal 10.2 : déc. 2023


EOL :
- Drupal 8 : Novembre 2021
- Drupal 9 : Novembre 2023
- Drupal 7 : (EOL étendue) Janvier 2025, https://www.drupal.org/psa-2023-06-07
