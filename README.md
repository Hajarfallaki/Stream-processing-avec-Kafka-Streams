📚 TP Kafka Streams : Nettoyage et Filtrage de Messages en Temps Réel

Ce projet implémente une application de traitement de flux (Stream Processing) utilisant Apache Kafka Streams pour lire des messages texte bruts, les nettoyer, les valider et les router vers différents topics de sortie.

✒️ Auteur

HAJAR ELFALLAKI-IDRISSI

🎯 Objectif du TP

Le but de l'application TextProcessingApp est de garantir la qualité et la conformité des données texte. Pour chaque message reçu :

Normalisation : Mettre le texte en majuscules et supprimer les espaces multiples ou inutiles.

Validation : Filtrer les messages trop longs (> 100 caractères) ou ceux contenant des mots interdits (HACK, SPAM, XXX).

Routage :

Les messages valides sont envoyés au topic text-clean.

Les messages invalides sont envoyés au topic text-dead-letter (DLT) pour analyse.

🛠️ Prérequis

Pour exécuter ce projet, vous devez avoir installé :

Java Development Kit (JDK) 17 ou supérieur.

Apache Maven (utilisé pour la compilation et l'exécution).

Docker (ou Docker Desktop) pour exécuter les conteneurs Kafka et Zookeeper.

🚀 Guide de Démarrage Rapide

Étape 1 : Démarrer l'infrastructure Kafka

Assurez-vous que votre cluster Kafka (Broker et Zookeeper) est en cours d'exécution via Docker.

docker-compose up -d



Étape 2 : Compilation du Projet

Compilez l'application pour vous assurer que toutes les dépendances sont chargées et que le code est prêt.

mvn clean package



Étape 3 : Lancement de l'Application Kafka Streams (Fenêtre 1)

Cette application doit être en cours d'exécution permanente pour traiter les messages en temps réel.

Ouvrez une PREMIÈRE fenêtre PowerShell, exécutez la commande, et laissez-la tourner :

mvn exec:java



(Attendez le message Application Kafka Streams démarrée...)

Étape 4 : Exécution des Tests et Vérification (Fenêtre 2)

Ouvrez une DEUXIÈME fenêtre PowerShell pour exécuter le script de test qui va créer les topics, envoyer les messages et consommer les résultats.

Copiez et collez l'intégralité du bloc suivant dans votre deuxième fenêtre :

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
# Doit afficher les messages originaux contenant SPAM, XXX, trop longs, ou vides (total : 4 messages)
docker exec kafka kafka-console-consumer --bootstrap-server localhost:9092 --topic text-dead-letter --from-beginning --max-messages 10 --timeout-ms 5000 | Out-Host

Write-Host "`n=== Test terminé! ===" -ForegroundColor Green



🧹 Nettoyage

Une fois les tests terminés, vous pouvez arrêter l'application dans la première fenêtre (Ctrl+C).

Pour arrêter les conteneurs Docker :

docker-compose down
