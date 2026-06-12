# Dockeriser Java, Angular et React: guide DevSecOps

Ce depot fournit des templates Docker prets a adapter pour trois stacks courantes:

- Java Spring Boot
- Angular
- React

L'objectif est de construire des images legeres, reproductibles et securisees, en appliquant les bonnes pratiques DevSecOps: multi-stage build, runtime minimal, utilisateur non-root, surface d'attaque reduite, scan de vulnerabilites et gestion saine des secrets.

## Structure du depot

```text
.
|-- java-spring-boot/
|   |-- Dockerfile
|   `-- .dockerignore
|-- angular/
|   |-- Dockerfile
|   |-- nginx.conf
|   `-- .dockerignore
|-- react/
|   |-- Dockerfile
|   |-- nginx.conf
|   `-- .dockerignore
`-- .github/workflows/security-scan.yml
```

## Points cles appliques

### 1. Multi-stage build

Chaque Dockerfile separe l'etape de build de l'etape runtime.

- Java: Maven compile l'application, puis seul le JAR est copie dans une image JRE.
- Angular et React: Node.js compile les assets statiques, puis Nginx les sert en production.

Resultat: moins de dependances dans l'image finale, images plus petites, surface d'attaque reduite.

### 2. Images de base legeres

Les templates utilisent des images Alpine quand cela reste raisonnable:

- `maven:3.9-eclipse-temurin-17-alpine`
- `eclipse-temurin:17-jre-alpine`
- `node:20-alpine`
- `nginx:1.27-alpine`

En production stricte, vous pouvez aller plus loin avec des images distroless pour Java.

### 3. Execution non-root

Les conteneurs ne tournent pas en root:

- Java cree un utilisateur applicatif dedie.
- Nginx ecoute sur le port `8080`, compatible avec un processus non-root.

Cette pratique limite l'impact d'une compromission applicative.

### 4. Gestion des secrets

Ne stockez jamais de mots de passe, tokens, cles API ou certificats prives dans:

- un Dockerfile
- une image Docker
- un fichier `.env` versionne
- une variable `ARG` de build

Injectez les secrets au runtime avec Kubernetes Secrets, Docker Secrets, Vault, SOPS ou le gestionnaire de secrets de votre cloud.

### 5. Exposition minimale

Les Dockerfiles exposent uniquement le port necessaire:

- Java Spring Boot: `8080`
- Angular / React via Nginx: `8080`

`EXPOSE` documente l'intention, mais ne publie pas le port tout seul. La publication reste controlee au runtime.

### 6. Configuration Nginx durcie

Les templates frontend ajoutent:

- `try_files` pour les routes SPA
- endpoint `/healthz`
- headers de securite HTTP
- cache long pour assets statiques hashes
- desactivation de `server_tokens`
- port non privilegie `8080`

### 7. Scans DevSecOps dans la CI

La workflow GitHub Actions inclut:

- Hadolint pour analyser les Dockerfiles
- Trivy pour scanner les vulnerabilites et mauvaises configurations

Exemple local:

```bash
trivy fs .
trivy image mon-application:1.0.0
```

## Utilisation

### Java Spring Boot

Depuis la racine d'un projet Spring Boot Maven:

```bash
docker build -f java-spring-boot/Dockerfile -t spring-app:local .
docker run --rm -p 8080:8080 spring-app:local
```

Le Dockerfile attend:

- `pom.xml`
- `src/`
- un artefact produit dans `target/*.jar`

### Angular

Depuis la racine d'un projet Angular:

```bash
docker build -f angular/Dockerfile -t angular-app:local .
docker run --rm -p 8080:8080 angular-app:local
```

Si votre dossier de sortie Angular differe, adaptez l'argument:

```bash
docker build -f angular/Dockerfile --build-arg BUILD_OUTPUT=dist/votre-app/browser -t angular-app:local .
```

### React

Depuis la racine d'un projet React:

```bash
docker build -f react/Dockerfile -t react-app:local .
docker run --rm -p 8080:8080 react-app:local
```

Pour Create React App, utilisez souvent:

```bash
docker build -f react/Dockerfile --build-arg BUILD_OUTPUT=build -t react-app:local .
```

Pour Vite, la valeur par defaut `dist` convient generalement.

## Optimisation des images

Bonnes pratiques integrees:

- copier les manifests de dependances avant le code source pour optimiser le cache Docker
- utiliser `npm ci` plutot que `npm install`
- eviter les outils de build dans l'image finale
- exclure `node_modules`, `target`, logs, caches et fichiers locaux via `.dockerignore`
- utiliser `COPY --chown` quand des fichiers runtime doivent appartenir a l'utilisateur applicatif
- garder les tags d'images explicites et mettre a jour regulierement les bases

## Supply chain et registre

Pour une chaine de livraison plus robuste:

- publiez les images dans un registre prive ou controle: Harbor, Nexus, JFrog Artifactory, GHCR, ECR, ACR ou GCR
- activez les scans de registre
- signez les images avec Cosign
- genereez une SBOM avec Syft ou Trivy
- epinglez les versions critiques et surveillez les CVE
- mettez en cache les dependances Maven/NPM dans votre CI ou votre artifact repository

Exemple de signature:

```bash
cosign sign ghcr.io/organisation/application:1.0.0
cosign verify ghcr.io/organisation/application:1.0.0
```

## Checklist production

- [ ] L'image finale ne contient pas le compilateur ni les dependances de build.
- [ ] Le conteneur tourne avec un utilisateur non-root.
- [ ] Aucun secret n'est present dans l'image.
- [ ] Les ports exposes sont limites.
- [ ] Les images sont scannees avec Trivy ou un outil equivalent.
- [ ] Les images sont signees avant de partir en production.
- [ ] Les dependances applicatives sont surveillees.
- [ ] Les tags d'image sont immuables dans les environnements critiques.

## Licence

MIT. Vous pouvez reutiliser ces templates dans vos projets internes ou publics.
