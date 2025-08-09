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

Installation (sans Docker)

python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp config.example.yaml config.yaml
# Modifier config.yaml en fonction de vos chemins
python -m app.cli scan
python -m app.cli guess
python -m app.cli dedupe --dry-run
# Pour appliquer les déplacements :
python -m app.cli dedupe --apply

Installation avec Docker Compose
Configuration de base

    Copier config.example.yaml en config.yaml.

    Modifier config.yaml pour indiquer l’emplacement de votre bibliothèque média.

Avec Ollama en local

    Lancer le conteneur Ollama et télécharger un modèle :

docker compose up -d ollama
docker exec -it $(docker ps -qf name=ollama) ollama pull mistral

    Dans config.yaml, modifier l’URL Ollama :

base_url: http://host.docker.internal:11434

    Copier docker-compose.ollama-local.yaml en docker-compose.yaml.

    Construire l’image :

docker compose build

Avec Ollama sur un autre serveur

    Dans config.yaml, indiquer l’URL du serveur Ollama :

base_url: http://adresse_du_serveur:11434

    Copier docker-compose.ollama-server-externe.yaml en docker-compose.yaml.

    Construire l’image :

docker compose build

Commandes Docker

    Scan des fichiers (ffprobe + hash + DB)

docker compose run --rm media-dedupe python -m app.cli scan

    Analyse LLM (titre/année/type/épisode)

docker compose run --rm media-dedupe python -m app.cli guess

    Rapport des doublons détectés

docker compose run --rm media-dedupe python -m app.cli report

    Déduplication en DRY-RUN (aucun déplacement)

docker compose run --rm media-dedupe python -m app.cli dedupe --dry-run

    Appliquer la déduplication (déplacement vers le dossier de quarantaine)

docker compose run --rm media-dedupe python -m app.cli dedupe --apply

Informations Docker

    Le fichier docker-compose.yml installe ffmpeg et lance l’application avec config.yaml.

    Pensez à monter vos dossiers médias dans le conteneur.

Commandes disponibles

    scan : parcourt les dossiers, extrait les métadonnées et remplit la DB.

    guess : utilise le LLM pour déduire titre, année, type, SxxExx.

    dedupe : génère un rapport ; avec --apply, déplace les doublons vers quarantine_dir.

Base de données

    Fichier SQLite : data/media.db

    Tables :

        files

        decisions

Sécurité

    Par défaut, mode dry-run (aucune modification).

    L’option --apply déplace uniquement les fichiers vers quarantine_dir (aucune suppression).

Prochaines étapes (Phase 2)

    Intégration TMDb (API + cache local) et RAG pour fiabiliser les identifications.
