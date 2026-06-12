# Docker DevSecOps Templates Design

## Objectif

Creer un depot GitHub de reference contenant des Dockerfiles optimises et securises pour Java Spring Boot, Angular et React, avec une documentation claire en francais.

## Architecture

Le depot est organise par technologie. Chaque dossier contient un Dockerfile multi-stage, un `.dockerignore` adapte et, pour les frontends, une configuration Nginx durcie pour servir des fichiers statiques.

Une workflow GitHub Actions centralisee lance des controles DevSecOps simples: lint Dockerfile avec Hadolint et scan de vulnerabilites avec Trivy.

## Decisions de securite

- Utiliser des builds multi-stage pour separer compilation et runtime.
- Executer les conteneurs avec un utilisateur non-root.
- Eviter les secrets dans les images et documenter leur injection au runtime.
- Limiter les ports exposes au strict necessaire.
- Activer des headers HTTP de securite pour Nginx.
- Scanner images, dependances et IaC dans la CI.
- Preparer le depot pour la signature et la publication dans un registre prive.

## Livrables

- `java-spring-boot/Dockerfile`
- `java-spring-boot/.dockerignore`
- `angular/Dockerfile`
- `angular/.dockerignore`
- `angular/nginx.conf`
- `react/Dockerfile`
- `react/.dockerignore`
- `react/nginx.conf`
- `.github/workflows/security-scan.yml`
- `README.md`
- `SECURITY.md`
- `LICENSE`

## Verification

Le depot doit pouvoir etre verifie par lecture statique, par `git status`, et par inspection des Dockerfiles. La creation du depot distant GitHub depend de la disponibilite de `gh` et de l'authentification locale.
