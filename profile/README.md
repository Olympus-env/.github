<div align="center">

# 🏛️ Projet Olympus

**A modular ecosystem where automation, intelligence and prediction run entirely on-device.**

*Un écosystème modulaire d'outils autonomes, conçus pour fonctionner en local — sans dépendance au cloud.*

</div>

---

## Vision globale

**Olympus** est un écosystème d'outils logiciels indépendants, chacun dédié à un domaine précis (veille documentaire, finance quantitative, assistance vocale), mais partageant une même philosophie d'ingénierie.

- **Pourquoi cet écosystème ?** Réunir, sous une bannière commune, des modules qui appliquent les mêmes principes de conception : exécution **100 % locale**, intelligence artificielle embarquée et confidentialité par défaut.
- **Quel problème résout-il ?** La plupart des outils intelligents délèguent données et calcul à des services cloud (coût récurrent, latence réseau, fuite de données). Olympus explore l'inverse : faire tourner l'analyse, la prédiction et l'automatisation **sur la machine de l'utilisateur**.
- **Pourquoi plusieurs modules ?** Chaque domaine a ses contraintes propres. La séparation en dépôts autonomes garde chaque module simple, testable et déployable indépendamment, tout en réutilisant les mêmes briques (LLM local via Ollama, configuration par `.env`, Python).
- **Ambition technique :** démontrer qu'un poste de travail moderne peut héberger des agents IA utiles, fiables et privés, sans serveur distant.

Chaque module porte le nom d'une figure de la mythologie grecque, choisie selon son rôle technique.

> ℹ️ Les modules sont aujourd'hui développés **indépendamment**. Les liens d'intégration directe entre eux décrits plus bas relèvent de la **vision d'architecture** et ne sont pas tous encore implémentés dans le code.

---

## Le Panthéon — Écosystème de modules

| Module | Dépôt | Rôle | Logique du nom | Statut |
|--------|-------|------|----------------|--------|
| **Hermès** | [Olympus-env/Hermes](https://github.com/Olympus-env/Hermes) | Application desktop locale de **veille et de réponse automatisée aux appels d'offre** (collecte, analyse, rédaction assistée, validation humaine). | Hermès, **messager des dieux** : il relie l'utilisateur aux portails externes, transmet et met en forme l'information. | `Active development` |
| **Cassandre** | [Olympus-env/Cassandre](https://github.com/Olympus-env/Cassandre) | **Moteur de trading algorithmique quantitatif** local : analyse technique, détection de régime de marché, backtests et paper trading. | Cassandre, dotée du **don de prophétie** : le module est tourné vers l'analyse prédictive des marchés. | `Active development` — Research / Paper Trading (*live trading désactivé*) |
| **Thalos** | [Olympus-env/thalos](https://github.com/Olympus-env/thalos) | **Assistant vocal 100 % local et hors-ligne** (type JARVIS) : reconnaissance vocale, raisonnement par LLM, synthèse vocale et contrôle de l'OS. | Thalos, **automate de bronze gardien**, évoque l'automatisation et le rôle d'interface de contrôle de la machine. | `Prototype` — MVP en cours |

---

## Architecture et synergie globale

Les trois modules sont des applications autonomes reposant sur un **socle commun** : Python, un **LLM exécuté localement via [Ollama](https://ollama.com/)**, et une configuration par fichiers `.env`. Aucune donnée ne quitte la machine.

```text
                         ┌───────────────────────────┐
                         │      Utilisateur (local)   │
                         └─────────────┬─────────────┘
                                       │ voix / texte
                                ┌──────▼──────┐
                                │   THALOS    │  Assistant vocal — interface de contrôle
                                │ (voix, LLM) │
                                └──────┬──────┘
                  (vision d'intégration ─ non encore implémentée)
                ┌──────────────────────┼──────────────────────┐
        ┌───────▼────────┐                          ┌──────────▼─────────┐
        │     HERMÈS     │                          │     CASSANDRE      │
        │ Veille & AO    │                          │ Trading quantitatif│
        │ FastAPI+Tauri  │                          │ Analyse / Paper    │
        └───────┬────────┘                          └──────────┬─────────┘
                │                                              │
          Communication                                   Prédiction
        & rédaction assistée                          & aide à la décision
                └──────────────┬───────────────────────────────┘
                       ┌───────▼────────┐
                       │  Socle commun  │
                       │ Python · Ollama│
                       │ Local-first    │
                       └────────────────┘
```

**Rôles dans l'écosystème :**
- **Hermès** — module de *communication* : il dialogue avec des sources externes (portails d'appels d'offre), structure l'information et produit des livrables.
- **Cassandre** — module d'*analyse et de prédiction* : il transforme des données de marché en signaux et décisions étudiées.
- **Thalos** — module d'*interface et d'automatisation* : pensé comme un point d'entrée vocal capable, à terme, de piloter les autres services locaux.

**Échanges envisagés :** chaque module exposant déjà (Hermès) ou pouvant exposer une API locale et lisant sa configuration via `.env`, l'intégration future passerait naturellement par des **appels HTTP locaux** (`127.0.0.1`) et des **modèles Ollama partagés**, sans aucun service distant.

---

## Stack technologique globale

| Domaine | Technologies |
|---------|--------------|
| **Langage principal** | Python (3.10 → 3.12) |
| **IA / LLM locaux** | Ollama (Mistral 7B, Gemma 2), `nomic-embed-text` |
| **Backend & API** | FastAPI, Uvicorn, SQLModel / SQLAlchemy, SQLite |
| **Desktop & UI** | Tauri 2, React 18, TypeScript, Tailwind CSS, Electron, Gradio |
| **Données & finance** | pandas, numpy, pandas-ta, ccxt |
| **Documents & scraping** | Playwright, APScheduler, pdfplumber, PyMuPDF, BeautifulSoup |
| **Voix** | OpenAI Whisper, pyttsx3, sounddevice |
| **Sécurité & config** | `cryptography`, `python-dotenv`, fichiers `.env` |
| **Qualité** | pytest, pytest-asyncio |
| **Principe transverse** | 100 % local / offline, zéro cloud |

> Les technologies listées sont celles **réellement présentes** dans les dépôts ; elles varient d'un module à l'autre (voir le README de chaque projet pour le détail).

---

## Structure recommandée des dépôts

Modèle de référence pour les nouveaux modules de l'organisation, aligné sur l'existant :

```text
module-name/
├── src/                  # Code source du module
├── tests/                # Tests automatisés (pytest)
├── docs/ ou diagnostics/ # Documentation, rapports, cahier des charges
├── scripts/              # Scripts d'exécution / outillage
├── requirements.txt      # Dépendances Python
├── .env.example          # Variables d'environnement documentées (sans secret)
├── .gitignore
└── README.md             # Présentation et instructions du module
```

Les modules applicatifs plus complexes (ex. Hermès) peuvent séparer `backend/` et `frontend/`.

---

## Guidelines de contribution

- **Branches** : noms clairs et préfixés (`feature/…`, `fix/…`, `docs/…`).
- **Commits** : messages explicites décrivant l'intention du changement.
- **Documentation** : documenter chaque nouvelle fonctionnalité dans le README du module concerné.
- **Tests** : ajouter ou mettre à jour les tests `pytest` lorsque c'est pertinent.
- **Style** : respecter les conventions du langage et l'organisation existante du dépôt.
- **Configuration** : documenter toute nouvelle variable dans `.env.example`.
- **Secrets** : ne **jamais** committer de clés API, tokens, mots de passe ni fichiers de données sensibles.

---

## Sécurité et environnements

- **Variables d'environnement** : toute la configuration sensible passe par un fichier `.env` (jamais versionné), documenté via `.env.example`.
- **`.gitignore`** : présent dans chaque dépôt pour exclure secrets, données locales, artefacts de build et environnements virtuels.
- **Secrets jamais versionnés** : clés d'API (ex. exchanges, portails) et clés de chiffrement restent strictement locales.
- **Séparation des environnements** : distinguer local / test / production lorsque c'est pertinent. Exemple concret — Cassandre démarre **toujours en mode `paper`** par défaut, le trading réel devant être activé explicitement.
- **Confidentialité par conception** : exécution locale, services exposés uniquement sur `127.0.0.1`, aucune télémétrie cloud.
- **Dépendances** : maintenir des versions épinglées et surveiller les bibliothèques sensibles.

---

## Roadmap globale

- [ ] **Stabiliser** les modules existants (Hermès, Cassandre, Thalos).
- [ ] **Standardiser** les README individuels et la structure des dépôts.
- [ ] **Définir une convention d'intégration** inter-modules (API locales sur `127.0.0.1`, modèles Ollama partagés).
- [ ] **Renforcer la couverture de tests** sur l'ensemble des modules.
- [ ] **Documenter l'architecture** globale (schémas, flux de données).
- [ ] **Mettre en place des workflows CI/CD** (lint + tests) via GitHub Actions.
- [ ] **Industrialiser la distribution** (packaging / installeurs, à l'image de l'installeur Windows d'Hermès).

---

## Conclusion

Olympus n'est pas un produit unique mais un **panthéon d'outils** partageant une même conviction : l'intelligence logicielle peut être **puissante, utile et entièrement privée**, sans jamais quitter la machine de l'utilisateur.

<div align="center">
  <sub>🏛️ Projet Olympus — modular, local-first, intelligent.</sub>
</div>
