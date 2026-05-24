# Automatisation SecOps : Analyseur de Logs et Système d'Alerte

## 📝 Description
Ce projet consiste en un script développé en Python et Bash conçu pour surveiller en temps réel ou analyser de manière asynchrone les fichiers de logs d'un serveur Linux (ex: `/var/log/auth.log` ou les logs d'un serveur Web Nginx/Apache). L'objectif est d'identifier les attaques par force brute ou les scans de vulnérabilités et de notifier immédiatement l'équipe de sécurité.

## 🛠️ Technologies & Compétences Validées
* **Langages :** Python (Modules : `re`, `sys`, `requests`, `os`), Bash Scripting.
* **Concepts Cyber :** Analyse de logs (Parsing), Expressions Régulières (Regex), Détection des signaux d'attaque (Fuzzing/Brute force), Réponses aux incidents.
* **Système :** Tâches planifiées (Cronjobs sous Debian/Linux).

## 📊 Architecture du Script
1. **Extraction (Bash) :** Un script Bash extrait périodiquement les dernières lignes du fichier de log ciblé pour éviter de surcharger la mémoire.
2. **Analyse (Python) :** Le script Python applique des filtres Regex pour détecter :
   * Les codes d'erreur HTTP répétés (ex: 404 en masse indiquant un scan de répertoires).
   * Les échecs de connexion répétés (ex: `Failed password for invalid user`).
3. **Alerte :** Si une adresse IP dépasse un seuil critique (ex: 20 tentatives échouées en 1 minute), le script génère un payload JSON et l'envoie à un Webhook (Discord/Telegram).

## 🚀 Exemple de Code (Extrait du Parseur)
```python
import re

# Regex pour détecter les échecs de connexion SSH
ssh_fail_pattern = r"Failed password for .* from (\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3})"

def analyze_logs(log_file):
    with open(log_file, "r") as file:
        for line in file:
            match = re.search(ssh_fail_pattern, line)
            if match:
                ip_address = match.group(1)
                # Logique de comptage et d'alerte ici...
