📖 README – ReelSieve
🎯 Objectif

ReelSieve est un outil open source qui scanne ta bibliothèque de films et séries, utilise un LLM (ex. Mistral via Ollama) pour déduire les métadonnées correctes à partir des noms de fichiers et infos techniques, puis détecte et déplace automatiquement les doublons.

💡 Idéal si :

    Tu as des fichiers nommés de manière incohérente

    Les outils comme Radarr/Sonarr ont du mal à reconnaître certains médias

    Tu veux un tri intelligent avant de confier ta collection à un serveur comme Jellyfin

🛠 Fonctionnalités

    Scan récursif des répertoires

    Extraction technique via ffprobe (résolution, codec, audio, sous-titres…)

    Identification des médias par LLM (titre, année, saison, épisode…)

    Détection de doublons intelligents (même film/série mais noms différents)

    Scoring qualité (résolution, codec, audio, préférences linguistiques…)

    Déplacement des doublons vers un dossier de quarantaine

    Mode --dry-run pour simulation sans rien déplacer

    Docker Compose prêt à l’emploi :

        Ollama dans le conteneur

        ou Ollama déjà sur l’hôte

📦 Installation
1) Récupérer le projet

git clone https://github.com/noxmoze/ReelSieve.git
cd reelsieve


## 🛠 Installation (sans Docker)
```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

cp config.example.yaml config.yaml
# Modifier config.yaml selon vos chemins

# Scan des fichiers
python -m app.cli scan

# Analyse LLM
python -m app.cli guess

# Détection des doublons (dry-run)
python -m app.cli dedupe --dry-run

# Appliquer la déduplication
python -m app.cli dedupe --apply

🐳 Installation avec Docker Compose
1️⃣ Configuration de base

cp config.example.yaml config.yaml
# Modifier config.yaml pour votre bibliothèque

2️⃣ Ollama en local

docker compose up -d ollama
docker exec -it $(docker ps -qf name=ollama) ollama pull mistral

Modifier config.yaml :

base_url: http://host.docker.internal:11434

Copier le fichier :

cp docker-compose.ollama-local.yaml docker-compose.yaml
docker compose build

3️⃣ Ollama sur un autre serveur

Modifier config.yaml :

base_url: http://adresse_du_serveur:11434

Copier le fichier :

cp docker-compose.ollama-server-externe.yaml docker-compose.yaml
docker compose build

📦 Commandes Docker

# 1) Scan des fichiers
docker compose run --rm media-dedupe python -m app.cli scan

# 2) Analyse LLM
docker compose run --rm media-dedupe python -m app.cli guess

# 3) Rapport des doublons
docker compose run --rm media-dedupe python -m app.cli report

# 4) Déduplication en dry-run
docker compose run --rm media-dedupe python -m app.cli dedupe --dry-run

# 5) Appliquer la déduplication
docker compose run --rm media-dedupe python -m app.cli dedupe --apply

📂 Base de données

    Fichier : data/media.db

    Tables :

        files

        decisions

🔒 Sécurité

    Par défaut, dry-run → aucune suppression ou déplacement

    Avec --apply : déplace uniquement vers quarantine_dir (pas de suppression définitive)

📅 Roadmap — Phase 2

    Intégration TMDb (API + cache local)

    RAG pour fiabiliser les identifications


