# Security Policy

## Portee

Ce depot contient des templates Docker et CI. Les vulnerabilites pertinentes concernent notamment:

- mauvaises pratiques dans les Dockerfiles
- configuration Nginx faible
- exposition inutile de secrets ou ports
- recommandations DevSecOps incorrectes
- workflow CI qui ne detecte pas des problemes critiques attendus

## Signaler une vulnerabilite

Ouvrez une issue privee si votre plateforme GitHub le permet, ou contactez le mainteneur du depot avec:

- une description claire du risque
- le fichier concerne
- les conditions de reproduction
- une proposition de correction si disponible

Evitez de publier des preuves d'exploitation detaillees avant correction.

## Bonnes pratiques utilisateur

Ces templates sont des bases. Avant production:

- adaptez les versions d'images a votre politique interne
- scannez vos images finales
- stockez les secrets hors image
- signez les images publiees
- appliquez vos politiques Kubernetes ou runtime: read-only filesystem, dropped capabilities, seccomp, AppArmor et resource limits
