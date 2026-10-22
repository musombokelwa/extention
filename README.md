# Extension — Ubuntu Agent / Ubuntu Control

## 1. Présentation du projet

**Extension** est un projet logiciel destiné à connecter des outils et des environnements de développement afin de faciliter le contrôle, l'automatisation et l'utilisation d'un ordinateur Ubuntu.

L'une des fonctionnalités principales prévues est **Ubuntu Agent / Ubuntu Control** : une solution permettant de piloter un ordinateur Ubuntu depuis une application mobile installée sur un iPhone, lorsque les deux appareils sont connectés au même réseau Wi-Fi.

L'objectif est de permettre à l'utilisateur d'interagir avec son ordinateur à distance, sans dépendre obligatoirement d'un service cloud externe.

Le projet doit être développé progressivement, avec une architecture claire, une communication sécurisée et des fonctionnalités indépendantes pouvant évoluer sans nécessiter la réécriture complète du système.

## 2. Objectifs du projet

Le projet poursuit les objectifs suivants :

1. Permettre à un iPhone de communiquer avec un ordinateur Ubuntu.
2. Détecter l'ordinateur sur le réseau local sans imposer une adresse IP fixe.
3. Sécuriser l'association entre le téléphone et l'ordinateur.
4. Permettre l'ouverture et l'utilisation d'applications sur Ubuntu.
5. Fournir une interface mobile simple pour sélectionner les outils disponibles.
6. Permettre, à terme, d'envoyer des commandes au terminal Ubuntu depuis le téléphone.
7. Maintenir une communication interactive et réactive entre les deux appareils.
8. Réduire la dépendance aux infrastructures externes et garder le contrôle des données échangées.
9. Concevoir une base technique extensible pour ajouter de nouveaux outils.
10. Faciliter la maintenance, les tests et les contributions au code.

## 3. Architecture générale

L'architecture cible repose sur deux composants principaux.

### 3.1. Application mobile

Technologie principale : **Flutter / Dart**.

L'application mobile constitue l'interface utilisateur. Elle doit permettre de :

- détecter les ordinateurs compatibles sur le réseau local ;
- sélectionner un ordinateur ;
- effectuer l'association sécurisée avec cet ordinateur ;
- saisir le code PIN ou utiliser le mécanisme d'authentification prévu ;
- afficher les outils disponibles ;
- envoyer des demandes d'action à l'agent Ubuntu ;
- recevoir les réponses et afficher les résultats ;
- gérer les erreurs de connexion et les déconnexions.

Le répertoire associé dans le projet est `flutter_client/`.

### 3.2. Agent Ubuntu

Technologies principales : **Python et FastAPI**.

L'agent fonctionne sur l'ordinateur Ubuntu. Il assure l'intermédiaire entre l'application mobile et les fonctionnalités autorisées du système.

Ses responsabilités sont notamment :

- démarrer le service local ;
- détecter et gérer les demandes de connexion ;
- authentifier les appareils associés ;
- exposer les fonctionnalités autorisées ;
- maintenir une communication interactive ;
- exécuter les actions validées ;
- transmettre les résultats à l'application mobile ;
- enregistrer les événements nécessaires au diagnostic technique.

Le répertoire associé dans le projet est `agent/`, tandis que `server/` peut contenir des composants serveur supplémentaires selon l'implémentation réelle.

**Important :** l'architecture et les responsabilités décrites ici représentent la conception cible. Elles doivent être comparées au code existant avant toute modification.

## 4. Communication entre les appareils

Le fonctionnement général prévu est le suivant :

1. L'utilisateur démarre l'agent sur Ubuntu.
2. L'agent rend le service disponible sur le réseau local.
3. L'utilisateur ouvre l'application Flutter sur son iPhone.
4. L'application recherche les ordinateurs compatibles.
5. L'utilisateur sélectionne son ordinateur.
6. Les deux appareils réalisent une association sécurisée.
7. L'application établit une connexion avec l'agent.
8. L'utilisateur choisit une fonctionnalité.
9. L'application envoie une demande autorisée.
10. L'agent vérifie la demande et exécute l'action correspondante.
11. Le résultat est transmis au téléphone.
12. L'application affiche le résultat ou une erreur explicite.

### Protocole de communication

- **HTTP/HTTPS** : pour les opérations de contrôle et les API classiques, selon les besoins.
- **WebSocket** : pour les échanges interactifs nécessitant une communication persistante, notamment le terminal.
- **Réseau local** : pour la communication directe entre les appareils lorsque la configuration réseau le permet.

La découverte automatique doit être conçue pour fonctionner sans adresse IP codée en dur. Elle devra tenir compte des règles du réseau local, des pare-feu et des autorisations réseau d'iOS.

## 5. Authentification et sécurité

La sécurité doit être prise en compte dès la première version.

Les mécanismes à prévoir sont :

- association explicite entre le téléphone et l'ordinateur ;
- authentification par PIN ou jeton sécurisé, selon le protocole retenu ;
- génération de jetons suffisamment aléatoires ;
- stockage sécurisé des informations d'authentification ;
- expiration et révocation des autorisations ;
- limitation des tentatives de connexion ;
- validation des demandes reçues ;
- protection contre les commandes non autorisées ;
- gestion des connexions et déconnexions ;
- journalisation des événements importants sans exposer les secrets.

Les mots de passe, PIN sensibles et jetons ne doivent pas être enregistrés en clair dans les journaux ou dans le dépôt Git.

L'exécution de commandes arbitraires doit être encadrée par des contrôles d'accès. L'agent ne doit pas exécuter automatiquement une commande reçue simplement parce qu'elle provient d'un appareil connecté.

La communication doit être protégée contre l'interception et les attaques sur le réseau local. Une connexion sur le même Wi-Fi ne constitue pas, à elle seule, une garantie de sécurité.

## 6. Fonctionnalités prévues pour la première version

### 6.1. Terminal Ubuntu

Le terminal constitue l'une des fonctionnalités principales.

Objectifs :

- ouvrir une session de terminal sur Ubuntu ;
- transmettre les commandes autorisées depuis l'iPhone ;
- afficher la sortie des commandes ;
- gérer les erreurs ;
- maintenir le contexte de session lorsque nécessaire ;
- gérer la fermeture et la reconnexion ;
- permettre à l'utilisateur de contrôler explicitement les actions sensibles.

L'objectif final est de pouvoir interagir avec le terminal Ubuntu depuis le téléphone.

### 6.2. Calculatrice

La calculatrice doit permettre d'accéder rapidement à un outil de calcul depuis l'interface du projet.

Son intégration peut prendre la forme d'un outil mobile autonome ou d'une fonctionnalité associée à l'environnement Ubuntu, selon l'architecture retenue.

### 6.3. Antigravity

Le projet prévoit un accès à l'environnement Antigravity installé sur Ubuntu.

Les actions précises — ouverture, détection de l'installation et éventuelle interaction — doivent être définies et validées en fonction des capacités réellement disponibles sur le système.

### 6.4. Jupyter Notebook

Le projet prévoit également un accès à Jupyter Notebook.

L'intégration devra préciser si l'application permet uniquement de démarrer ou d'ouvrir Jupyter, ou si elle prévoit des interactions plus avancées avec les notebooks.

Chaque fonctionnalité doit être isolée dans un module afin de faciliter les tests et les évolutions.

## 7. Organisation du dépôt

La structure connue du dépôt contient notamment les éléments suivants :

```text
extension/
├── app/                 # Application Next.js
├── public/              # Ressources publiques
├── server/              # Composants serveur
├── agent/               # Agent Ubuntu à examiner
├── data/                # Données du projet
├── flutter_client/      # Application mobile Flutter
├── prisma/              # Schéma et ressources Prisma
├── prisma.config.ts     # Configuration Prisma
├── package.json         # Dépendances et scripts Node.js
├── package-lock.json    # Verrouillage des dépendances npm
├── next.config.ts       # Configuration Next.js
├── tsconfig.json        # Configuration TypeScript
└── README.md            # Documentation du projet
```

Cette liste décrit les principaux éléments précédemment repérés. Elle n'est pas nécessairement exhaustive et ne confirme pas que chaque composant est terminé ou utilisé dans le parcours principal.

### Rôle des principaux répertoires

- `app/` : contient l'application web Next.js.
- `public/` : contient les ressources publiques de l'application web.
- `server/` : regroupe les composants serveur, selon leur organisation actuelle.
- `agent/` : est destiné à l'agent Ubuntu.
- `data/` : regroupe les données nécessaires au projet.
- `flutter_client/` : contient le client mobile Flutter.
- `prisma/` : contient les ressources liées au modèle de données Prisma.
- `package.json` : définit les dépendances et les commandes du projet.

La présence de plusieurs technologies dans le même dépôt impose de documenter clairement les responsabilités de chaque composant et leurs relations.

## 8. Environnement technique

| Composant | Technologie prévue ou repérée | Responsabilité |
|---|---|---|
| Application mobile | Flutter / Dart | Interface iPhone |
| Agent local | Python / FastAPI | Contrôle des fonctionnalités Ubuntu |
| Communication interactive | WebSocket | Échanges en temps réel |
| API classique | HTTP/HTTPS | Opérations de contrôle |
| Application web | Next.js / TypeScript | Interface web du dépôt |
| Couche de données | Prisma | Modélisation et accès aux données, selon la configuration |
| Système cible | Ubuntu Linux | Exécution de l'agent et des outils |

Les versions exactes, les dépendances réellement utilisées et les éventuels autres services doivent être vérifiés dans les fichiers de configuration du projet.

## 9. Installation et démarrage

Les commandes suivantes sont des exemples à adapter aux fichiers réellement présents.

### 9.1. Récupérer le projet

```bash
git clone <URL_DU_DEPOT>
cd extension
```

### 9.2. Préparer l'environnement Python

Si l'agent possède un fichier `requirements.txt` :

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r agent/requirements.txt
```

Le chemin du fichier de dépendances doit être vérifié avant l'installation.

### 9.3. Préparer l'application web

Si le projet Next.js utilise npm :

```bash
npm install
```

Les scripts réellement disponibles peuvent être consultés avec :

```bash
npm run
```

Le démarrage dépendra du script défini dans `package.json`, par exemple :

```bash
npm run dev
```

### 9.4. Préparer l'application Flutter

Depuis le répertoire mobile :

```bash
cd flutter_client
flutter pub get
flutter doctor
```

Il faudra ensuite configurer l'environnement de compilation iOS et les autorisations réseau nécessaires. La compilation et la signature d'une application iOS nécessitent généralement un environnement macOS avec les outils Apple appropriés.

### 9.5. Démarrer l'agent Ubuntu

La commande exacte dépend du point d'entrée Python existant. Elle devra être documentée après vérification des fichiers de lancement, de la configuration FastAPI et des paramètres réseau.

Ne pas inventer de commande de démarrage définitive avant cette vérification.

## 10. Configuration

La configuration devra permettre de définir les paramètres nécessaires au fonctionnement du système, notamment :

- adresse d'écoute de l'agent ;
- port de communication ;
- environnement d'exécution ;
- paramètres d'authentification ;
- configuration des outils disponibles ;
- options de journalisation ;
- paramètres propres à l'application web ;
- variables de connexion à une éventuelle base de données.

Les secrets doivent être placés dans des variables d'environnement ou un mécanisme de stockage sécurisé adapté.

Les fichiers `.env` contenant des secrets ne doivent pas être publiés dans le dépôt. Un fichier `.env.example` peut présenter les noms des variables attendues sans contenir de valeurs confidentielles.

## 11. Gestion des erreurs et de la connexion

Le système doit traiter explicitement les situations suivantes :

- ordinateur éteint ou agent arrêté ;
- téléphone et ordinateur sur des réseaux différents ;
- appareil introuvable ;
- authentification refusée ;
- jeton expiré ou révoqué ;
- port bloqué par le pare-feu ;
- connexion WebSocket interrompue ;
- outil demandé indisponible ;
- commande invalide ou non autorisée ;
- erreur d'exécution sur Ubuntu.

L'application mobile doit afficher des messages compréhensibles, sans exposer les détails techniques sensibles.

La reconnexion doit être contrôlée afin d'éviter les boucles de connexion et les répétitions involontaires d'actions.

## 12. Tests et validation

Le développement doit être accompagné de tests progressifs.

### Tests unitaires

Vérifier séparément :

- l'authentification ;
- la validation des demandes ;
- les fonctions de découverte ;
- les modules d'outils ;
- la gestion des erreurs.

### Tests d'intégration

Vérifier :

- la communication entre Flutter et l'agent ;
- l'établissement et la fermeture des WebSockets ;
- la transmission des résultats ;
- la gestion d'une perte de connexion ;
- le comportement avec un outil indisponible.

### Tests de sécurité

Vérifier :

- le rejet des appareils non associés ;
- la limitation des tentatives d'authentification ;
- l'expiration des jetons ;
- le rejet des commandes non autorisées ;
- la protection des données sensibles ;
- l'absence d'exécution involontaire de commandes.

### Tests fonctionnels

Le premier parcours de validation doit rester simple :

1. Démarrer l'agent Ubuntu.
2. Détecter l'ordinateur depuis le téléphone.
3. Associer le téléphone.
4. Établir la connexion.
5. Ouvrir le premier outil, le terminal.
6. Effectuer une action de test autorisée.
7. Afficher le résultat.
8. Fermer la session proprement.

## 13. Confidentialité et contrôle des données

Le fonctionnement local vise à limiter la dépendance à des infrastructures externes.

Les données et commandes doivent être transmises uniquement aux composants nécessaires au fonctionnement du système.

Le projet doit documenter :

- les données collectées ;
- les données conservées ;
- la durée de conservation ;
- les journaux produits ;
- les permissions accordées ;
- les communications éventuellement réalisées avec des services externes.

Le fonctionnement sans cloud doit être vérifié pour chaque fonctionnalité. Certaines intégrations peuvent avoir leurs propres dépendances réseau.

## 14. État du développement

Les éléments suivants constituent la feuille de route fonctionnelle. Leur état réel doit être confirmé par l'inspection du code et les tests.

- [ ] Audit de la structure du dépôt.
- [ ] Identification du point d'entrée de l'agent.
- [ ] Vérification de la configuration FastAPI.
- [ ] Démarrage fiable de l'agent Ubuntu.
- [ ] Découverte automatique de l'ordinateur.
- [ ] Association sécurisée entre les appareils.
- [ ] Authentification et gestion des jetons.
- [ ] Connexion interactive par WebSocket.
- [ ] Premier parcours fonctionnel du terminal.
- [ ] Intégration de la calculatrice.
- [ ] Intégration d'Antigravity.
- [ ] Intégration de Jupyter Notebook.
- [ ] Gestion des erreurs et des reconnexions.
- [ ] Tests de sécurité.
- [ ] Documentation des installations et des commandes.
- [ ] Validation du fonctionnement sans dépendance cloud obligatoire.

Une case ne doit être cochée que lorsque la fonctionnalité a été vérifiée.

## 15. Principes de développement

Pour conserver un projet maintenable :

1. Analyser le code existant avant de modifier son fonctionnement.
2. Éviter de réécrire les modules qui fonctionnent déjà sans raison technique.
3. Séparer les responsabilités entre application mobile, agent et serveur.
4. Centraliser les paramètres de configuration.
5. Ne pas intégrer de secrets dans le code source.
6. Écrire des tests pour les fonctionnalités critiques.
7. Documenter les changements importants.
8. Vérifier les dépendances avant de mettre à jour les versions.
9. Conserver un historique Git clair.
10. Tester chaque étape avant d'ajouter de nouvelles fonctionnalités.

## 16. Contributions et maintenance

Avant de contribuer :

- examiner l'architecture existante ;
- identifier le composant concerné ;
- comprendre les dépendances ;
- reproduire le problème ou définir le besoin ;
- effectuer une modification ciblée ;
- lancer les tests disponibles ;
- documenter le changement ;
- vérifier qu'aucun secret ni fichier généré inutile n'est ajouté au dépôt.

Les conventions de branches, de commits et de revue de code pourront être définies au fur et à mesure de l'organisation du projet.

## 17. Licence

La licence du projet reste à définir ou à confirmer.

Avant une publication publique, vérifier les droits de redistribution du code, des dépendances et des composants intégrés. Ne pas déclarer une licence sans décision explicite du propriétaire du projet.

## 18. Conclusion

Extension vise à fournir une architecture claire pour relier des interfaces utilisateur à des outils et services logiciels, avec un intérêt particulier pour le contrôle local d'un ordinateur Ubuntu depuis un iPhone.

La priorité du développement est de valider un premier parcours complet et sécurisé : découverte de l'ordinateur, association du téléphone, connexion, utilisation du terminal et réception du résultat.

Une fois ce parcours validé, les autres outils pourront être ajoutés progressivement sans compromettre la sécurité ni la stabilité de l'ensemble.

**Principe directeur : construire, tester et documenter chaque fonctionnalité avant de la considérer comme terminée.**
