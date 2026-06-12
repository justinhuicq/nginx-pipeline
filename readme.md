Voici le README mis à jour :

```markdown
# Nginx CI Pipeline — Niveaux 1 & 2

## Description

Pipeline CI/CD avec GitHub Actions pour déployer un serveur nginx dans un conteneur Docker.
Le projet couvre deux niveaux de maturité : mise en place de la pipeline (Novice) puis amélioration
avec tests HTTP, endpoint de santé et bonnes pratiques de sécurité (Engineer).

---

## Structure du projet

```
.
├── .github/
│   └── workflows/
│       └── ci.yml        # Pipeline GitHub Actions
├── Dockerfile            # Recette de l'image Docker
├── nginx.conf            # Configuration du serveur nginx
├── index.html            # Page web servie
└── README.md
```

---

## Schéma du pipeline

```
Push / Pull Request
       │
       ▼
┌─────────────────────────────────────────────────────┐
│                   GitHub Actions                    │
│                                                     │
│  1. Checkout du code                                │
│  2. Vérification des fichiers (Dockerfile,          │
│     nginx.conf, index.html)                         │
│  3. Build de l'image Docker                         │
│  4. Test de la configuration nginx (nginx -t)       │
│  5. Démarrage du conteneur                          │
│  6. Test HTTP — page principale (200 OK)            │
│  7. Test endpoint /health (200 OK)                  │
│  8. Nettoyage du conteneur                          │
└─────────────────────────────────────────────────────┘
```

---

## Déclencheurs

La pipeline se déclenche automatiquement sur :
- `push` vers la branche `main`
- `pull_request` vers la branche `main`

---

## Détail des étapes

### 1. Vérification des fichiers
Contrôle que les trois fichiers essentiels sont présents à la racine du repo :
`Dockerfile`, `nginx.conf`, `index.html`.

### 2. Build Docker
Construction de l'image à partir du `Dockerfile` basé sur `nginx:alpine`.

### 3. Test de configuration nginx
Exécution de `nginx -t` à l'intérieur du conteneur pour valider la syntaxe
de `nginx.conf` avant tout démarrage.

### 4. Test HTTP — page principale
Démarrage du conteneur sur le port 8080, puis vérification via `curl` que
le service répond `HTTP 200` sur `/`.

### 5. Test endpoint /health
Vérification que l'endpoint `/health` répond `HTTP 200` avec le corps `healthy`.
Cet endpoint est utilisable par des outils de supervision externes.

### 6. Nettoyage
Arrêt et suppression du conteneur de test à la fin du job, même en cas d'échec
(directive `if: always()`).

---

## Endpoint /health

nginx expose une route de santé sur `/health` :

```
GET http://localhost/health
→ 200 OK
→ Body : healthy
```

Configuration dans `nginx.conf` :

```nginx
location /health {
    access_log off;
    return 200 "healthy\n";
    add_header Content-Type text/plain;
}
```

---

## Sécurité

- Aucun secret, mot de passe ou clé API dans le code source ou les logs.
- Les secrets éventuels sont gérés via **GitHub Secrets** 
  (Settings → Secrets and variables → Actions) et injectés comme variables
  d'environnement dans le workflow. GitHub les masque automatiquement dans les logs.

---

## Test d'échec volontaire (Niveau 1)

Pour valider que la pipeline détecte bien une erreur, un commit avec une
configuration nginx invalide (point-virgule manquant, accolade non fermée)
provoque l'échec du step **Test de la configuration Nginx**.

Restaurer la configuration correcte et recommiter repasse la pipeline au vert.

---

## Lancer le projet en local

```bash
# Build de l'image
docker build -t nginx-pipeline .

# Démarrage du conteneur
docker run -d -p 8080:80 nginx-pipeline

# Test de la page principale
curl http://localhost:8080

# Test du healthcheck
curl http://localhost:8080/health
```

---

## Prérequis

- Docker
- Un compte GitHub avec Actions activé
```
