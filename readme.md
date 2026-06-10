# Nginx CI Pipeline — Niveau 1 Novice

## Description
Pipeline CI/CD avec GitHub Actions pour déployer un serveur nginx dans un conteneur Docker.

## Schéma du pipeline
Push → Checkout → Vérif. fichiers → Build Docker → Test nginx -t → Test HTTP

## Structure du projet
.
├── .github/
│   └── workflows/
│       └── ci.yml
├── Dockerfile
├── nginx.conf
└── index.html

## Étapes de la pipeline
1. **Vérification des fichiers** : contrôle que nginx.conf, index.html et Dockerfile sont présents
2. **Build Docker** : construction de l'image
3. **Test nginx** : `nginx -t` valide la configuration
4. **Test HTTP** : `curl` vérifie que le serveur répond

## Test d'échec
Un commit avec une configuration nginx invalide provoque l'échec du step "Test de la configuration Nginx".