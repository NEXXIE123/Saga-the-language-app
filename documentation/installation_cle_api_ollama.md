# Installation clé API Ollama


## Étape 1 — Installer Ollama

curl -L https://github.com/ollama/ollama/releases/latest/download/ollama-linux-amd64 -o ollama
chmod +x ollama
sudo mv ollama /usr/local/bin/


## Étape 2 — Télécharger le modèle

ollama pull llama3.2


## Étape 3 — Vérifier que ça marche

ollama run llama3.2