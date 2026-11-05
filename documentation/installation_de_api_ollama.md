# Installation de l'API Ollama

> **Pourquoi Ollama et pas l'API Claude ?**
> L'API Claude (Anthropic) **n'a pas été installée** : elle est payante à l'usage
> (facturation par token) et le coût était trop important pour le projet.
> Ollama est gratuit et fait tourner les modèles **en local** sur la machine,
> sans clé API ni abonnement.

Date de l'installation : 5 novembre 2026
Machine : Linux x86_64, 15 Go de RAM, pas de GPU NVIDIA (le modèle tourne sur le CPU)

---

## Étape 0 — Constat de départ

Ollama avait déjà été installé avant (service `ollama` actif, version 0.34.4),
mais la commande `ollama` ne fonctionnait plus :

```bash
$ ollama --version
/usr/local/bin/ollama: ligne 1: Not : commande introuvable
```

**Cause :** l'ancienne commande d'installation (celle de
`installation_cle_api_ollama.md`) télécharge
`.../releases/latest/download/ollama-linux-amd64`. **Ce fichier n'existe plus**
sur GitHub : Ollama est maintenant distribué sous forme d'archive
`ollama-linux-amd64.tar.zst`. GitHub a donc renvoyé une page « Not Found »
(9 octets) qui a écrasé le vrai programme dans `/usr/local/bin/ollama`.

⚠️ N'utilisez plus l'ancienne commande `curl ... ollama-linux-amd64 -o ollama`.

---

## Étape 1 — Vérifier l'existant

```bash
ls -la /usr/local/bin/ollama          # taille du binaire (9 octets = cassé)
systemctl status ollama                # le service tourne-t-il ?
curl -s localhost:11434/api/version    # version du serveur -> {"version":"0.34.4"}
ls /usr/local/lib/ollama               # bibliothèques déjà installées
```

---

## Étape 2 — Télécharger la bonne archive

On télécharge la **même version** que les bibliothèques déjà présentes (0.34.4),
pour que le binaire et les bibliothèques restent compatibles :

```bash
mkdir -p ~/ollama-dl && cd ~/ollama-dl
curl -fL -o ollama.tar.zst \
  https://github.com/ollama/ollama/releases/download/v0.34.4/ollama-linux-amd64.tar.zst
```

(L'archive fait environ 1,4 Go. L'option `-f` fait échouer `curl` si le fichier
n'existe pas, au lieu d'enregistrer une page d'erreur.)

---

## Étape 3 — Extraire et remplacer le binaire

```bash
tar --zstd -xf ollama.tar.zst bin/ollama   # extrait seulement le programme
./bin/ollama --version                      # test -> ollama version is 0.34.4
cp bin/ollama /usr/local/bin/ollama
chmod 755 /usr/local/bin/ollama
cd ~ && rm -rf ~/ollama-dl                  # nettoyage
```

Vérification :

```bash
ollama --version   # ollama version is 0.34.4
ollama list        # les modèles déjà téléchargés sont toujours là
```

---

## Étape 4 — Télécharger le modèle

```bash
ollama pull llama3.2
```

Le modèle `llama3.2` (3B paramètres) pèse environ 2 Go.

---

## Étape 5 — Vérifier que ça marche

En ligne de commande :

```bash
ollama run llama3.2
```

Par l'API HTTP (c'est elle que l'application utilisera) :

```bash
curl -s http://localhost:11434/api/generate \
  -d '{"model":"llama3.2","prompt":"Dis bonjour en une phrase.","stream":false}'
```

Réponse obtenue : `Bonjour ! Comment puis-je vous aider aujourd'hui ?`

L'API écoute sur **`http://localhost:11434`**, sans clé API.

---

## Installation complète sur une nouvelle machine

Sur un poste où Ollama n'est pas installé, la méthode officielle (qui installe
le binaire, les bibliothèques et le service systemd) est :

```bash
curl -fsSL https://ollama.com/install.sh | sh
ollama pull llama3.2
ollama run llama3.2
```

Cette méthode demande le mot de passe `sudo`. Elle sert aussi à **mettre à jour**
Ollama vers la dernière version (0.40.14 au 5 novembre 2026).
