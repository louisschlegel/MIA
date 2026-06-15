# Mia — Documentation technique

> Plateforme de coaching fitness pilotée par l'IA (**LIA**).
> Ce dossier regroupe la documentation technique de référence : stack, architecture, infrastructure, sécurité, conformité et coûts.

---

## 📦 Contexte

**Mia** est une application mobile-first (iOS / Android) doublée d'une expérience web responsive. Son cœur produit est **LIA**, un assistant IA qui :

- suit chaque répétition pendant la séance (comptage + analyse de la forme) ;
- adapte automatiquement le programme selon les progrès réels ;
- répond aux questions (nutrition, technique, douleur, motivation) ;
- ajuste son **ton** sur un curseur *Bienveillant ↔ Intense*.

La donnée manipulée est en grande partie de la **donnée de santé** (poids, fréquence cardiaque, performance, sommeil). Cela conditionne fortement l'architecture, la sécurité et la conformité (voir `06` et `07`).

---

## 🗂️ Plan de la documentation

| # | Document | Contenu |
|---|----------|---------|
| 00 | [`00-index.md`](./00-index.md) | Ce document — vue d'ensemble & conventions |
| 01 | [`01-stack-technique.md`](./01-stack-technique.md) | Choix de technologies et justifications |
| 02 | [`02-architecture.md`](./02-architecture.md) | Architecture système, services, flux |
| 03 | [`03-modele-donnees.md`](./03-modele-donnees.md) | Modèle de données, schéma, stockage |
| 04 | [`04-infrastructure.md`](./04-infrastructure.md) | Cloud, déploiement, CI/CD, observabilité |
| 05 | [`05-ia-lia.md`](./05-ia-lia.md) | LIA : LLM, vision, moteur de reco, personnalité |
| 06 | [`06-securite.md`](./06-securite.md) | Authentification, chiffrement, menaces |
| 07 | [`07-conformite-gouvernance.md`](./07-conformite-gouvernance.md) | RGPD, HDS, gouvernance de la donnée |
| 08 | [`08-couts.md`](./08-couts.md) | Coûts d'infra & d'IA, unit economics |
| 09 | [`09-roadmap-technique.md`](./09-roadmap-technique.md) | Trajectoire technique par phase |

---

## 🎯 Principes directeurs

1. **Privacy by design** — la donnée de santé reste minimale, chiffrée, et le traitement sensible (vision) se fait *on-device* par défaut.
2. **Mobile-first, temps réel** — l'expérience de séance live impose une latence faible (< 200 ms perçus) et un fonctionnement dégradé hors-ligne.
3. **IA encadrée** — LIA ne donne jamais d'avis médical ; garde-fous, traçabilité et supervision humaine sur les recommandations sensibles.
4. **Coûts maîtrisés** — l'inférence IA est le principal poste variable ; on optimise par routage de modèles, cache et exécution edge.
5. **Itératif & mesurable** — chaque brique livrée derrière feature-flag, métriquée, réversible.

---

## 🧭 Conventions

- **Langue** : documentation en français, code et identifiants en anglais.
- **Statut** : `🟢 stable` · `🟡 cible` · `🔵 exploratoire`. La plupart des choix sont en statut **cible** (pré-MVP).
- **Diagrammes** : Mermaid (rendu nativement sur GitHub / GitLab).
- **Versionnage** : ces documents suivent le dépôt ; toute décision structurante fait l'objet d'un ADR (`docs/adr/NNNN-*.md`).

---

## ⚠️ Périmètre & avertissement

Cette documentation décrit l'**architecture cible** d'un produit en construction. Le prototype livré (`Mia.dc.html`) illustre l'expérience avec des **données mockées** : aucune des briques backend / IA décrites ici n'y est branchée. Les chiffres de coûts (`08`) sont des estimations de cadrage, à affiner avec la charge réelle.
