# Mia

> Plateforme de coaching fitness pilotée par l'IA (**LIA**).

**Mia** est une application mobile-first (iOS / Android) doublée d'une expérience web responsive.
Son cœur produit est **LIA**, un assistant IA qui suit chaque répétition pendant la séance,
adapte le programme selon les progrès réels, répond aux questions (nutrition, technique,
douleur, motivation) et ajuste son ton sur un curseur *Bienveillant ↔ Intense*.

Ce dépôt regroupe le **prototype d'expérience**, le **deck stratégique** et la
**documentation technique de référence**.

---

## 🗂️ Structure

| Dossier | Contenu |
|---|---|
| [`prototype/`](./prototype) | Prototype interactif de l'expérience produit (`mia.dc.html`). Données **mockées**, aucun backend branché. |
| [`deck/`](./deck) | Deck stratégique (`mia-strategie.dc.html`) — vision, positionnement, business. |
| [`docs/`](./docs) | Documentation technique : stack, architecture, modèle de données, infra, IA, sécurité, conformité, coûts, roadmap. Commencer par [`docs/00-index.md`](./docs/00-index.md). |
| [`screenshots/`](./screenshots) | Captures du prototype et de l'écran de chat LIA. |

`prototype/` et `deck/` sont **autonomes** : chacun embarque son runtime (`support.js`,
plus `deck-stage.js` pour le deck). Le runtime est un artefact généré et versionné, à ne pas
éditer à la main.

---

## ▶️ Visualiser

Les livrables sont des fichiers **`.dc.html`** (design components) qui se rendent dans un
navigateur. Ils chargent des ressources distantes (polices Google, icônes Lucide) → connexion
internet requise.

```bash
# Servir le dépôt localement puis ouvrir dans le navigateur
python3 -m http.server 8000
# → http://localhost:8000/prototype/mia.dc.html
# → http://localhost:8000/deck/mia-strategie.dc.html
```

> Ouvrir directement le fichier (`file://`) fonctionne dans la plupart des cas ; un petit
> serveur local évite les restrictions éventuelles du navigateur.

---

## 📖 Documentation

La doc technique décrit l'**architecture cible** d'un produit en construction. Le prototype
illustre l'expérience avec des **données mockées** — aucune brique backend / IA n'y est
branchée, et les estimations de coûts sont des chiffres de cadrage.

Plan : [`docs/00-index.md`](./docs/00-index.md)

| # | Document |
|---|---|
| 01 | [Stack technique](./docs/01-stack-technique.md) |
| 02 | [Architecture](./docs/02-architecture.md) |
| 03 | [Modèle de données](./docs/03-modele-donnees.md) |
| 04 | [Infrastructure](./docs/04-infrastructure.md) |
| 05 | [LIA — moteur IA](./docs/05-ia-lia.md) |
| 06 | [Sécurité](./docs/06-securite.md) |
| 07 | [Conformité & gouvernance](./docs/07-conformite-gouvernance.md) |
| 08 | [Coûts](./docs/08-couts.md) |
| 09 | [Roadmap technique](./docs/09-roadmap-technique.md) |

---

## ⚠️ Périmètre

Donnée manipulée = en grande partie **donnée de santé** (poids, fréquence cardiaque,
performance, sommeil) → forte exigence de sécurité et de conformité (RGPD / HDS).
Voir [`docs/06`](./docs/06-securite.md) et [`docs/07`](./docs/07-conformite-gouvernance.md).
