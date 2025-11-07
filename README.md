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
