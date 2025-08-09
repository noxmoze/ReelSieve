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

git clone https://github.com/<TON_COMPTE>/reelsieve.git
cd reelsieve

2) Créer et éditer la config

cp config.example.yaml config.yaml

Exemple :

media_dirs:
  - /media/Films
  - /media/Series

quarantine_dir: /media/_doublons
database_path: data/media.db

llm:
  base_url: http://ollama:11434   # ou http://host.docker.internal:11434 si Ollama sur l’hôte
  model: mistral
  options: { temperature: 0.1, num_predict: 200 }

scoring:
  prefer_resolution: [2160, 1080, 720, 480]
  prefer_video_codec: [hevc, h265, av1, h264]
  bonus_audio_langs: ["fre","fra","fr"]
  quarantine_days: 14

🚀 Utilisation
Option A – Ollama intégré dans Docker

# Lancer Ollama et tirer le modèle
docker compose -f docker-compose.ollama.yml up -d ollama
docker compose -f docker-compose.ollama.yml exec ollama ollama pull mistral

# Build
docker compose -f docker-compose.ollama.yml build

# Scan
docker compose -f docker-compose.ollama.yml run --rm app python -m app.cli scan

# Identification LLM
docker compose -f docker-compose.ollama.yml run --rm app python -m app.cli guess

# Rapport
docker compose -f docker-compose.ollama.yml run --rm app python -m app.cli report

# Simulation de déduplication
docker compose -f docker-compose.ollama.yml run --rm app python -m app.cli dedupe --dry-run

# Déduplication réelle
docker compose -f docker-compose.ollama.yml run --rm app python -m app.cli dedupe --apply

Option B – Ollama déjà sur l’hôte

    Utiliser docker-compose.localollama.yml

    Dans config.yaml, mettre base_url: http://host.docker.internal:11434 (Windows/Mac) ou ajouter extra_hosts sur Linux

📊 Exemple de sortie

[GUESS] ./Films/22 Jump Street (2014)/... -> {'type': 'movie', 'title': '22 Jump Street (2014)', 'year': 2014}
[GUESS] ./Films/22 Jump Street (2014) MULTi/... -> {'type': 'movie', 'title': '22 Jump Street (2014)', 'year': 2014}

[REPORT]
Titre                | Année | Chemin
-------------------- | ----- | ---------------------------------
22 Jump Street       | 2014  | ./Films/22 Jump Street (2014)/...
                     |       | ./Films/22 Jump Street (2014) MULTi/...

[DEDUP DRY-RUN]
Garde: ./Films/22 Jump Street (2014)/...
Déplace: ./Films/22 Jump Street (2014) MULTi/... -> /media/_doublons

⚙️ Prérequis

    Docker et Docker Compose

    ffmpeg/ffprobe (installé dans le conteneur)

    Accès Internet pour télécharger le modèle LLM (Mistral par défaut)

🔒 Sécurité

    Aucun fichier supprimé automatiquement : les doublons vont dans quarantine_dir

    Tu peux tester en --dry-run avant d’appliquer

🗺 Roadmap (Phase 2)

    Intégration API TMDb pour fiabiliser les métadonnées

    Interface web légère

    Support direct Radarr/Sonarr

    Matching multi-critères (hash vidéo + nom + durée)
