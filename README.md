# AI-Driven Email Assistant

Un système d'automatisation des e-mails basé sur l'IA qui récupère, filtre, résume et génère des réponses aux e-mails en utilisant des modèles de langage avancés. Il s'intègre aux serveurs IMAP et SMTP et utilise un workflow de graphe d'état pour gérer le traitement des e-mails.

Vous pouvez lire mon blog pour mieux comprendre le projet - [ici](https://medium.com/@parthshr370/building-your-first-agent-with-deepseek-ai-email-agent-e6f17d3c290e)

## Table des Matières

- [Aperçu](#aperçu)
- [Fonctionnalités](#fonctionnalités)
- [Installation](#installation)
- [Configuration](#configuration)
- [Utilisation](#utilisation)
- [Structure du Répertoire](#structure-du-répertoire)
- [Tests](#tests)
- [Contributions](#contributions)
- [Licence](#licence)
- [Remerciements](#remerciements)

## Aperçu

Ce dépôt implémente un pipeline de traitement des e-mails qui exploite des modèles de langage de pointe (via l'API Deepseek) et un workflow de graphe d'état (en utilisant LangGraph) pour :

- **Ingérer les E-mails :** Récupérer les e-mails d'un serveur IMAP ou charger des e-mails simulés à partir d'un fichier JSON.
- **Filtrer les E-mails :** Classifier les e-mails entrants comme _spam_, _urgent_, _informationnel_ ou _nécessite une révision_.
- **Résumer les E-mails :** Générer des résumés concis du contenu des e-mails.
- **Générer des Réponses :** Rédiger automatiquement des réponses aux e-mails tout en permettant une révision humaine.
- **Envoyer des E-mails :** Envoyer les réponses via SMTP ou envoyer les brouillons à un compte Gmail.

## Fonctionnalités

- **Ingestion d'E-mails :** Prend en charge à la fois la récupération d'e-mails en direct (via IMAP) et la simulation (via un fichier JSON local).
- **Agent de Filtrage :** Utilise un modèle de langage pour classer les e-mails dans des catégories.
- **Agent de Résumé :** Génère des résumés de 2 à 3 phrases des corps d'e-mails.
- **Agent de Réponse :** Rédige des réponses polies et professionnelles basées sur le contenu et le résumé des e-mails.
- **Révision Humaine :** Offre une option de révision manuelle et d'édition des réponses générées automatiquement.
- **Workflow de Graphe d'État :** Orchestre les étapes de traitement des e-mails (filtrage, résumé et génération de réponse) avec des transitions conditionnelles.
- **Journalisation :** Journalisation détaillée pour le débogage et la surveillance du comportement de l'application.

## Installation

### Prérequis

- Python 3.8 ou supérieur
- [pip](https://pip.pypa.io/)
- (Optionnel) [virtualenv](https://virtualenv.pypa.io/)

### Configuration

1. **Cloner le dépôt :**

   ```bash
   git clone https://github.com/votre_nom_utilisateur/nom-de-votre-depot.git
   cd nom-de-votre-depot
   ```

2. **Créer et activer un environnement virtuel (recommandé) :**

   ```bash
   python -m venv venv
   source venv/bin/activate  # Sur Windows : venv\Scripts\activate
   ```

3. **Installer les dépendances :**

   ```bash
   pip install -r requirements.txt
   ```

## Configuration

L'application nécessite plusieurs paramètres de configuration (tels que les clés API et les identifiants du serveur de messagerie). Créez un fichier `.env` à la racine du projet avec les variables suivantes :

```dotenv
# API Deepseek
DEEPSEEK_API_KEY=votre_cle_api_deepseek

# Paramètres SMTP
EMAIL_SERVER=smtp.votreserveur.com
EMAIL_USERNAME=votre_email@exemple.com
EMAIL_PASSWORD=votre_mot_de_passe_email
EMAIL_PORT=587  # Ou votre port SMTP

# Paramètres IMAP (par défaut, les paramètres Gmail si non fournis)
IMAP_USERNAME=votre_nom_utilisateur_imap
IMAP_PASSWORD=votre_mot_de_passe_imap
IMAP_SERVER=imap.gmail.com
IMAP_PORT=993
```

Ajustez les valeurs selon votre environnement et votre fournisseur de messagerie.

## Utilisation

Pour exécuter l'application principale de traitement des e-mails, exécutez simplement :

```bash
python main.py
```

### À quoi s'attendre

1. **Récupération des E-mails :**  
   L'application récupérera les e-mails de votre serveur IMAP (ou simulera en utilisant `sample_emails.json` si configurée pour la simulation).

2. **Traitement des E-mails :**  
   Chaque e-mail est traité via un workflow de graphe d'état :
   - **Filtrage :** Classifie l'e-mail (par exemple, spam, urgent, informationnel, nécessite une révision).
   - **Résumé :** Génère un court résumé du contenu de l'e-mail.
   - **Génération de Réponse :** Rédige une réponse. Si la réponse est incertaine ou marquée pour révision, elle demande une intervention humaine.

3. **Envoi/Brouillon :**  
   Vous serez invité à envoyer l'e-mail ou à l'enregistrer comme brouillon (qui sera envoyé via SMTP à l'adresse Gmail que vous avez spécifiée).

## Structure du Répertoire

```plaintext
.
├── agents
│   ├── filtering_agent.py           # Classification des e-mails en utilisant les LLM
│   ├── human_review_agent.py        # Permet la révision manuelle des réponses générées
│   ├── response_agent.py            # Génère les réponses aux e-mails
│   ├── summarization_agent.py       # Résume le contenu des e-mails
│   └── __init__.py
├── config.py                        # Charge la configuration et les variables d'environnement
├── core
│   ├── email_imap.py                # Intégration IMAP pour la récupération d'e-mails en direct
│   ├── email_ingestion.py           # Ingestion d'e-mails simulée (fichier JSON)
│   ├── email_sender.py              # Intégration SMTP pour l'envoi d'e-mails
│   ├── state.py                     # Définition de la classe de données EmailState
│   ├── supervisor.py                # Coordonne le workflow de graphe d'état
│   └── __init__.py
├── drafts
│   └── Schedule.txt                 # Exemple de fichier de brouillon d'e-mail
├── email.sh                       # Script shell pour les tâches d'e-mail (si nécessaire)
├── hello.md                       # Notes supplémentaires du projet ou infos de démo
├── hhhhhh.jpg                     # Image du projet (ex : logo)
├── main.py                        # Point d'entrée principal pour l'application
├── Python Script COmbined for ipynb.py  # Script combiné à partir d'un Jupyter Notebook
├── README.md                      # Ce fichier de documentation
├── requirements.txt               # Dépendances Python
├── sample_emails.json             # Données d'e-mails simulées pour les tests/démo
├── sample.ipynb                   # Jupyter Notebook avec des exemples de code
├── test_email.py                  # Tests unitaires pour les fonctionnalités de traitement des e-mails
└── utils
    ├── formatter.py               # Fonctions utilitaires pour le formatage des e-mails
    ├── logger.py                  # Configuration et configuration du journal
    └── __init__.py
```

## Tests

Pour exécuter les tests (si fournis), vous pouvez utiliser un framework de test comme `pytest` :

```bash
pytest
```

Ou exécutez :

```bash
python -m unittest discover
```

Assurez-vous que les dépendances de test sont installées et que vous avez configuré vos variables d'environnement pour les tests si nécessaire.

## Contributions

Les contributions sont les bienvenues ! Pour contribuer :

1. Forkez le dépôt.
2. Créez une nouvelle branche (`git checkout -b feature/ma-fonctionnalite`).
3. Commitez vos modifications et poussez-les vers votre branche.
4. Ouvrez une Pull Request avec une description claire de vos modifications.

Veuillez suivre nos conventions de codage et inclure des tests pertinents lorsque c'est applicable.

## Licence

Ce projet est sous licence MIT. Voir le fichier [LICENCE](https://github.com/parthshr370/Email-AI-Agent/blob/main/LICENSE.txt) pour plus de détails.

## Remerciements

- **Deepseek :** Pour l'API et le backend du modèle de langage.
- **LangChain & LangGraph :** Pour les frameworks qui aident à construire des workflows pilotés par des modèles de langage.
- **Contributeurs Open Source :** Merci à tous ceux qui ont contribué aux bibliothèques et outils utilisés dans ce projet.

---

Cette documentation devrait servir de guide complet pour l'installation, l'utilisation et la contribution à l'Assistant E-mail Alimenté par l'IA. N'hésitez pas à modifier ou à étendre les sections à mesure que votre projet évolue.
