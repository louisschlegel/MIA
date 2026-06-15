# 01 — Stack technique

> Statut global : 🟡 cible (pré-MVP) · Dernière revue : 2026-06

Ce document liste les technologies retenues et **pourquoi**. Le fil conducteur : un produit mobile-first, temps réel, qui manipule de la donnée de santé et embarque de l'IA (LLM + vision).

---

## 1. Vue synthétique

| Couche | Technologie | Statut | Pourquoi (résumé) |
|--------|-------------|--------|-------------------|
| Mobile | **React Native (Expo)** + TypeScript | 🟡 | Une base de code iOS/Android, accès natif caméra/capteurs, OTA updates |
| Web | **Next.js 15** (App Router) + TypeScript | 🟡 | Site responsive + marketing + back-office, SSR/SEO, partage du design system |
| Design system | **Tokens partagés** + Tamagui / RN-Web | 🟡 | Composants unifiés mobile + web |
| API Gateway | **NestJS** (Node 22, TypeScript) | 🟡 | API REST/GraphQL typée, modulaire, écosystème mûr |
| Temps réel | **WebSocket** (NestJS Gateway) + Redis Pub/Sub | 🟡 | Coaching live pendant la séance |
| Services IA | **Python 3.12 + FastAPI** | 🟡 | Écosystème ML, orchestration LLM, inférence |
| Vision (reps) | **MediaPipe / TF Lite** on-device | 🟡 | Pose estimation locale : latence + vie privée |
| LLM | **API Claude** (Anthropic) + fallback | 🟡 | Qualité conversationnelle, sûreté, tool-use |
| Recherche/RAG | **pgvector** (+ embeddings) | 🟡 | RAG sur base de connaissance + contexte utilisateur |
| Base relationnelle | **PostgreSQL 16** | 🟢 | Robustesse, JSON, extensions (pgvector, PostGIS) |
| Séries temporelles | **TimescaleDB** (extension PG) | 🟡 | Métriques d'entraînement, capteurs |
| Cache / file | **Redis 7** | 🟢 | Cache, sessions live, rate-limit, files légères |
| Jobs / events | **BullMQ** (Redis) puis **Kafka** à l'échelle | 🟡 | Tâches asynchrones, event-driven |
| Stockage objets | **S3** (chiffré KMS) | 🟢 | Médias, exports, modèles |
| Auth | **OAuth2/OIDC** (Auth0 ou Keycloak) | 🟡 | Standard, social login, MFA |
| Paiement | **Stripe** + RevenueCat | 🟢 | Abonnements web + in-app (App/Play Store) |
| Infra | **Cloud souverain UE / HDS** + Kubernetes | 🟡 | Donnée de santé hébergée en UE (voir `07`) |
| IaC | **Terraform** + Helm | 🟡 | Infra reproductible, revue en PR |
| CI/CD | **GitHub Actions** + EAS (mobile) | 🟡 | Pipelines, builds mobiles managés |
| Observabilité | **OpenTelemetry** + Grafana/Loki/Tempo | 🟡 | Traces, logs, métriques unifiés |

---

## 2. Frontend

### 2.1 Mobile — React Native (Expo)

**Pourquoi React Native + Expo :**

- **Un seul code** iOS + Android : l'équipe est petite, le time-to-market prime.
- **Accès natif** indispensable : caméra (comptage de reps), capteurs de mouvement, HealthKit / Health Connect (fréquence cardiaque, sommeil, pas), notifications.
- **OTA updates** (EAS Update) : corriger l'UI sans repasser par la review des stores — précieux en phase d'itération.
- **Performance** suffisante pour notre UI ; les traitements lourds (vision) tournent dans des modules natifs / GPU.

**Alternatives écartées :**
- *Flutter* : excellent, mais l'écosystème JS/TS partagé avec le web et le vivier de devs penchent pour RN.
- *Natif pur (Swift/Kotlin)* : qualité maximale mais double coût ; réservé aux modules critiques (vision) via bridges natifs.

### 2.2 Web — Next.js

- **Responsive** : reprend le parcours client (l'utilisateur veut « les deux »).
- **Marketing/SEO** en SSR/SSG, **back-office** (support, contenu, supervision IA) en zone protégée.
- **Design system partagé** mobile↔web via tokens + React Native Web / Tamagui.

---

## 3. Backend

### 3.1 Polyglotte assumé : Node (NestJS) + Python (FastAPI)

| Domaine | Runtime | Raison |
|---------|---------|--------|
| API métier, auth, temps réel, paiements | **NestJS / Node** | Typage, productivité, WebSocket natif, un seul langage avec le front |
| IA : orchestration LLM, embeddings, reco, vision serveur | **FastAPI / Python** | Écosystème ML (PyTorch, transformers, MediaPipe), libs d'inférence |

Les deux communiquent via **gRPC / REST interne** + un **bus d'événements**. Découplage = on peut scaler l'IA indépendamment du métier (le poste de coût et de charge le plus variable).

### 3.2 Temps réel

- **WebSocket** pour la séance live (compteur, conseils, repos synchronisés).
- **Redis Pub/Sub** pour diffuser les événements entre instances.
- Repli **offline-first** : la séance fonctionne sans réseau, synchronisation différée (file locale → backend).

---

## 4. Données

- **PostgreSQL** comme socle transactionnel (utilisateurs, programmes, abonnements).
- **TimescaleDB** (hypertables) pour les **séries temporelles** d'entraînement (volume, charges, FC) — requêtes d'agrégation efficaces pour les courbes de progrès.
- **pgvector** pour les **embeddings** (RAG, similarité d'exercices, mémoire de LIA).
- **Redis** pour le chaud (sessions live, cache de réponses LIA, rate-limit).
- **S3** chiffré pour les objets (rares médias, exports RGPD, artefacts de modèles).

> Choix structurant : **rester sur l'écosystème PostgreSQL** le plus longtemps possible (Timescale + pgvector sont des extensions) pour limiter le nombre de moteurs à opérer.

---

## 5. IA — voir `05-ia-lia.md`

Résumé des briques : **LLM** (chat/coaching/personnalité) via API Claude + garde-fous ; **RAG** sur base de connaissance d'entraînement + contexte utilisateur ; **moteur de recommandation** (adaptation du programme) ; **vision on-device** pour le comptage de répétitions et l'analyse de forme.

---

## 6. Outillage transverse

- **TypeScript partout** côté JS ; **Pydantic** côté Python pour les contrats.
- **Monorepo** (Turborepo / Nx) : packages partagés (types, design system, client API généré).
- **OpenAPI + codegen** : le client mobile/web est généré depuis le contrat backend.
- **Tests** : Vitest/Jest (unit), Playwright (web e2e), Detox/Maestro (mobile e2e), pytest (IA), tests de charge k6.
- **Qualité** : ESLint/Prettier, Ruff/Black, lint des migrations, revue obligatoire en PR.

---

## 7. Décisions à trancher (ADR ouverts)

| ADR | Sujet | Options |
|-----|-------|---------|
| 0001 | Hébergeur HDS UE | OVHcloud (HDS) · Scaleway · AWS Europe (clause HDS) |
| 0002 | Auth managé vs auto-hébergé | Auth0 (rapide) vs Keycloak (souverain, moins cher à l'échelle) |
| 0003 | LLM principal | Claude API vs modèle open-weight auto-hébergé (coût/souveraineté) |
| 0004 | GraphQL vs REST pour le client | REST+OpenAPI (simple) vs GraphQL (flexibilité front) |
| 0005 | Vision 100 % on-device vs hybride | On-device (vie privée) vs assistance serveur (précision) |
