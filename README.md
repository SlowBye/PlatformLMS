Atelier
 Conception d'une plateforme LMS

PRÉSENTÉ PAR 
MORAIS PEREIRA David,
 ZAOUALI Olfa, 
BEN KEMIL Mezen,
 TATANG DOUANLA Jopcelin

 


CLASSE 
 Mastère 1 DEV WEB
 DATE DE REMISE  
11-02-2025



Atelier	1
1.	Une cartographie de l’architecture :	3
2.	Une justification des choix d’architecture :	4
3.	Deux exemples d’implémentation technique :	5
4.	Un README explicatif :	9
Résumé de l’Architecture :	Erreur ! Signet non défini.


























1.	Une cartographie de l’architecture :
a.	Identification des différentes briques et services
Fonctionnalité	Outils	Services
Gestion des utilisateurs	- Base de données des utilisateurs 
- Mécanisme d'authentification sécurisé (OAuth2, JWT) 
	- Service d’inscription des utilisateurs 
- Service de connexion (authentification) 
- Service de gestion des rôles et permissions 
Gestion des cours	- Base de données des cours 
- Moteur de gestion des cours (création, édition, organisation)	- Service de création et édition de cours 
- Service de gestion des modules de cours 
- Service de partage des documents et ressources pédagogiques 
Système de correction automatique	- Base de données des évaluations (résultats des examens, soumissions) 
- Moteur de correction automatique (algorithmique)	- Service de correction d’examens 
- Service de correction de code pour les soumissions de devoirs 
Moteur de recommandation	- Base de données des performances d'apprentissage des étudiants 
- Moteur d’analyse et de recommandation 	- Service d'analyse des données d'apprentissage des étudiants 
- Service de génération de recommandations de cours
Forum et messagerie	- Base de données des discussions/messages 
- Serveur de messagerie en temps réel (WebSockets)	- Service de gestion des forums 
- Service de messagerie privée 
- Service de notifications en temps réel
Gestion des certificats et badges	- Base de données des certificats et badges obtenus 
- Moteur de validation des acquis et génération de certificats

	- Service de validation des acquis 
- Service de génération de certificats et attribution de badges

b.	Distinction entre les éléments en Microkernel et Microservices
Module	Architecture	Justification
Gestion des utilisateurs	Microservices	Permet de gérer indépendamment l'authentification, les rôles et les permissions. Facilite l’intégration avec des services externes (OAuth…).
Gestion des cours	Microkernel	Noyaux du LMS et peut avoir des plugins supplémentaires
Correction automatique	Microservices	Nécessitant une grande scalabilité pour supporter un grand nombre de copie.
Moteur de recommandation	Microservices	Basé sur l'analyse des données d’apprentissage. Doit être évolutif et peut nécessiter de l’IA.
Forum et messagerie	Microservices	Indépendant du LMS, peut être remplacé par un service externe (ex : Slack).
Gestion des certificats et badges	Microkernel	Dépasse la complexité du besoin pas besoin d’en faire un microservice, peut être implémentée en tant que plugin dans le Microkernel de gestion des cours.

c.	Présentation des API et des bases de données utilisées
Fonctionnalité	API	Base de données
Gestion des utilisateurs	REST API : Pour la gestion des utilisateurs	SQL (MySQL) : Gérer des informations relationnelles sur les utilisateurs (inscription, rôles, etc.)
Gestion des cours	REST API :  Pour ajouter, supprimer, modifier des cours et obtenir des détails	SQL (MySQL) : Structuration des cours, modules et leur relation avec les utilisateurs
Système de correction	REST API : Pour corriger les examens 	SQL (MySQL) : Stocker les soumissions, évaluations, résultats de correction
Moteur de recommandation	REST API / Kafka : Recommandations basées sur les performances d'apprentissage 	NoSQL (MongoDB) : Pour analyser les données non structurées sur les performances des étudiants
Forum et messagerie	WebSockets : Communication en temps réel 	NoSQL (MongoDB) : Stocker les messages, discussions et notifications
Gestion des certificats	REST API : Créer et gérer les certificats	SQL (MySQL) : Stocker les certificats et badges des étudiants


2.	Une justification des choix d’architecture :
a.	Microkernel vs Microservices :  Choix d’Architecture pour les fonctionnalités 
•	Microkernel :
Fonctionnalités : Gestion des cours, gestion des certificats et badges.
Justification : Ces briques sont centrales au système LMS. Le modèle Microkernel permet de garder un noyau stable et flexible tout en ajoutant des plugins ou modules supplémentaires sans perturber le système. Cela garantit la stabilité, la cohérence et l’extensibilité des fonctionnalités fondamentales comme la gestion des cours et des certificats.
•	Microservices :
Fonctionnalités : Gestion des utilisateurs, correction automatique, moteur de recommandation, forum et messagerie.
Justification : Ces briques nécessitent indépendance, évolutivité et scalabilité. Microservices permettent une gestion modulaire et l'évolutivité, où chaque service peut être développé, déployé et mis à l'échelle indépendamment. Cela est essentiel pour des services comme la gestion des utilisateurs, le moteur de recommandation ou la correction automatique, qui doivent être capables de gérer un volume élevé de données et d'utilisateurs.

b.	Bénéfices en Maintenance et Évolutivité des Architectures Microkernel et Microservices 
L'architecture Microservices permet d’assurer une grande évolutivité et une maintenance simplifiée pour des fonctionnalités comme la gestion des utilisateurs, la correction automatique, ou le moteur de recommandation, qui doivent être indépendantes et scalable en fonction du volume d’utilisateurs.
Le Microkernel, quant à lui, permet de garantir une stabilité et une extensibilité pour des fonctionnalités de base comme la gestion des cours et la gestion des certificats, tout en offrant la possibilité d'ajouter des plugins sans perturber le noyau du système.
En résumé, l’utilisation d’une architecture hybride (Microkernel + Microservices) optimise à la fois la maintenabilité et l’évolutivité, permettant de gérer des systèmes complexes avec des exigences diverses tout en garantissant la stabilité du système global.
3.	Structure du projet
a.	Organisation des dossiers et composants
Le projet est structuré en suivant une approche modulaire, combinant Microkernel pour le noyau principal et ses plugins, ainsi que Microservices pour des fonctionnalités indépendantes et évolutives.
 
b.	Explication des interactions entre les différents modules
L’architecture repose sur la séparation des responsabilités entre le Microkernel (le noyau principal), ses plugins, et les microservices.
•	Le noyau (Core Server) : Il gère la gestion des cours, qui est la base du système. Il expose une API REST permettant d’interagir avec les cours.
•	Les plugins (ex. gestion des certificats, gamification, etc.) : Ils s’appuient sur l’API REST du noyau pour récupérer des informations et ajouter des fonctionnalités supplémentaires sans modifier le noyau lui-même.
•	Les microservices : Fonctionnalités indépendantes (authentification, correction automatique, moteur de recommandation, etc.…) qui interagissent avec le noyau et les plugins via des APIs REST ou messagerie asynchrone (Kafka).
4.	Deux exemples d’implémentation technique :
a.	Exposition d’un service sous forme de Microservice (déclaration d’API REST)

 

b.	Mécanisme d’extension sous forme de Microkernel (exemple de relation entre le noyau et ses plugins)
Architecture :
Le Core contient les fonctionnalités principales, comme la gestion des cours (ajout, mise à jour des cours, etc.…).
Le Plugin (gestion des certificats) va interagir avec le Core pour récupérer les informations sur les cours ou les performances des étudiants, puis générer un certificat.
Core - Gestion des Cours (Microkernel)
Le Core fournit une API REST pour récupérer les informations des cours et des performances des étudiants. Ce service est indépendant du plugin.
  
Plugin - Gestion des Certificats
Le Plugin va se connecter au Core via l'API REST pour récupérer les informations de cours d'un étudiant et générer un certificat.
  
=> Lorsqu'un étudiant termine un cours, le plugin de gestion des certificats est déclenché pour générer un certificat correspondant. Pour cela, il effectue une requête HTTP GET via RestTemplate (Spring) à l'API REST du Core afin de récupérer les informations du cours associé. Les données obtenues sont ensuite analysées et utilisées pour générer le certificat, qui est finalement retourné à l'utilisateur via l'API du plugin. Ce processus s'applique de manière similaire à d'autres plugins, tels que l'analyse des performances ou la gamification, qui interagissent avec le Core selon le même principe. Vous trouverez ci-dessous une présentation graphique illustrant la manière dont ces plugins communiquent avec le Core.
 


5.	Un README explicatif :
a.	Les technologies choisies 
•	Frameworks : Spring Boot → l’un des meilleurs Frameworks pour combiner une architecture Microkernel et Microservices, offrant un noyau extensible tout en permettant le développement de services indépendants et scalables.
•	Langages : Java → choix naturel pour Spring Boot, performant et robuste.
•	Bases de données :
♦	MySQL → pour stocker des données relationnelles structurées (utilisateurs, cours, certificats).
♦	MongoDB → pour des données semi-structurées et volumineuses (messagerie).
•	Middleware :
♦	Kafka → système de messagerie distribué utilisé pour l’échange de données entre services en temps réel.
•	Orchestration :
♦	Docker → facilite le déploiement des services sous forme de conteneurs isolés.
b.	Évolution du système
•	Intégration d'IA pour les recommandations et la correction automatique :
♦	L’analyse des performances des étudiants pourrait être optimisée par du Machine Learning pour offrir des recommandations personnalisées de cours.
♦	Une IA pourrait aussi automatiser la correction des examens et fournir des feedbacks détaillés.
•	Amélioration de l’engagement des utilisateurs avec des assistants IA :
♦	Intégration d’un chatbot IA basé sur GPT pour répondre aux questions des étudiants en temps réel.
♦	Développement d’un tuteur virtuel intelligent capable d’adapter les exercices en fonction du niveau et du rythme d’apprentissage de chaque utilisateur.
•	      Expansion du LMS vers des formats d’apprentissage immersifs :
♦	Ajout d’un module de réalité virtuelle (VR) permettant des formations interactives dans un environnement immersif.
♦	Intégration de la réalité augmentée (AR) pour proposer des expériences d’apprentissage plus engageantes (exemple : dissections virtuelles pour les cours de biologie).



