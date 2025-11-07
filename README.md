<!-- PROJECT LOGO -->
<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/0/05/Apache_kafka.svg" alt="Kafka Logo" width="160"/>
</p>

<h1 align="center">📚 TP Kafka Streams : Nettoyage et Filtrage de Messages en Temps Réel</h1>

<p align="center">
  <b>Application Kafka Streams pour le nettoyage, la validation et le routage de messages texte en temps réel</b>
  <br/>
  <sub>Réalisé par Hajar Elfallaki-Idrissi — ENSET Mohammedia (2025)</sub>
</p>

---

## ✒️ Auteur

👩‍💻 **HAJAR ELFALLAKI-IDRISSI**  
Étudiante ingénieure en **Data, Intelligence Artificielle & Cloud Computing**  
📍 *ENSET Mohammedia — 2025*

---

## 🎯 Objectif du TP

L'application **TextProcessingApp** vise à assurer la **qualité et la conformité** des messages texte transmis dans un flux Kafka.

### ⚙️ Fonctionnalités principales

1. **Normalisation**  
   - Conversion du texte en majuscules  
   - Suppression des espaces multiples ou inutiles  

2. **Validation**  
   - Rejet des messages de plus de **100 caractères**  
   - Rejet des messages contenant des mots interdits : `HACK`, `SPAM`, `XXX`

3. **Routage**
   - ✅ Messages valides → `text-clean`  
   - ❌ Messages invalides → `text-dead-letter` *(DLT)*

---

## 🛠️ Prérequis

Avant d’exécuter ce projet, installez :

| Outil | Version minimale | Description |
|--------|------------------|--------------|
| ☕ Java JDK | 17+ | Pour compiler et exécuter Kafka Streams |
| 🧩 Apache Maven | 3.8+ | Gestionnaire de build du projet |
| 🐳 Docker | Latest | Pour exécuter Kafka & Zookeeper |

---

## 🚀 Guide de Démarrage Rapide

### 🧱 Étape 1 — Lancer Kafka avec Docker

Assurez-vous que **Docker Desktop** est en marche, puis exécutez :

```bash
docker-compose up -d
```
---

### Étape 4 — Tester le Traitement (Deuxième fenêtre PowerShell)

Copiez-collez ce script PowerShell complet dans une seconde fenêtre :

```bash
Write-Host "=== Création des topics ===" -ForegroundColor Blue
docker exec kafka kafka-topics --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic text-input
docker exec kafka kafka-topics --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic text-clean
docker exec kafka kafka-topics --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic text-dead-letter
Write-Host "Topics vérifiés ou créés!" -ForegroundColor Green

Write-Host "`n=== Envoi de messages de test ===" -ForegroundColor Blue
"Bonjour   le    monde" | docker exec -i kafka kafka-console-producer --bootstrap-server localhost:9092 --topic text-input
"Message valide simple" | docker exec -i kafka kafka-console-producer --bootstrap-server localhost:9092 --topic text-input
"  texte avec espaces  " | docker exec -i kafka kafka-console-producer --bootstrap-server localhost:9092 --topic text-input
"" | docker exec -i kafka kafka-console-producer --bootstrap-server localhost:9092 --topic text-input
"Ce message contient SPAM" | docker exec -i kafka kafka-console-producer --bootstrap-server localhost:9092 --topic text-input
"XXX contenu" | docker exec -i kafka kafka-console-producer --bootstrap-server localhost:9092 --topic text-input
"Ce message est beaucoup trop long et dépasse largement la limite de cent caractères qui a été définie" | docker exec -i kafka kafka-console-producer --bootstrap-server localhost:9092 --topic text-input

Write-Host "`nMessages envoyés! Attente du traitement..." -ForegroundColor Blue
Start-Sleep -Seconds 3

Write-Host "`n=== Messages dans text-clean (valides) ===" -ForegroundColor Blue
# Doit afficher 3 messages en majuscules et nettoyés
docker exec kafka kafka-console-consumer --bootstrap-server localhost:9092 --topic text-clean --from-beginning --max-messages 10 --timeout-ms 5000 | Out-Host

Write-Host "`n=== Messages dans text-dead-letter (invalides) ===" -ForegroundColor Blue
# Doit afficher les messages originaux contenant SPAM, XXX, trop longs ou vides (total : 4 messages)
docker exec kafka kafka-console-consumer --bootstrap-server localhost:9092 --topic text-dead-letter --from-beginning --max-messages 10 --timeout-ms 5000 | Out-Host

Write-Host "`n=== Test terminé! ===" -ForegroundColor Green
```
