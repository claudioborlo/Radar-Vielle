# Méthode BMAD appliquée au projet "Radar de veille"

BMAD (*Breakthrough Method for Agile AI-Driven Development*) structure un développement assisté par IA en phases tenues par des personas spécialisés (Analyst, PM, Architect, Scrum Master/PO, Dev, QA), chacune produisant un document de référence avant de passer à la suivante. L'objectif : éviter la dérive de contexte et garder une discipline agile même sur un projet piloté par un seul développeur + IA.

Ce document liste les étapes et livrables nécessaires pour développer le radar de veille, dans l'ordre BMAD. Chaque étape peut être traitée comme une conversation dédiée avec Claude, en donnant le persona correspondant.

---

## Phase 1 — Analyst : Project Brief

**Rôle simulé** : Analyste métier. Clarifie le problème, les utilisateurs, les contraintes, avant toute solution technique.

**Entrée** : [CLAUDE.md](CLAUDE.md) (contient déjà l'essentiel : sujets suivis, seuils, sources, format).

**À produire** — `docs/project-brief.md` :
- Problème à résoudre : rester informé quotidiennement sur 6 sujets d'investissement sans lecture manuelle de presse.
- Utilisateur : toi seul, niveau intermédiaire, décisions d'investissement.
- Contraintes : pas de résumé sans lecture réelle de la source ; 3-4 lignes/sujet ; zones FR/Europe/Chine/USA ; langues FR/EN/IT.
- Hors périmètre : presse généraliste, buzz, politique pure.
- Critère de succès : digest quotidien fiable, actionnable, sans hallucination de sources.

> Ce document existe déjà en substance dans CLAUDE.md — cette étape consiste surtout à vérifier qu'il ne manque rien avant de passer à la suite.

---

## Phase 2 — PM : PRD (Product Requirements Document)

**Rôle simulé** : Product Manager. Traduit le brief en exigences fonctionnelles et non-fonctionnelles, découpées en epics.

**À produire** — `docs/prd.md` :
- **Exigences fonctionnelles (FR)**, ex. :
  - FR1 : le système collecte des informations pour chacun des 6 sujets chaque jour.
  - FR2 : chaque sujet n'est inclus dans le digest que si son déclencheur spécifique est atteint.
  - FR3 : chaque source citée a été effectivement récupérée et lue (pas de résumé sur titre/URL seuls).
  - FR4 : le digest respecte le format 3-4 lignes (tendance + explication du déclenchement).
- **Exigences non-fonctionnelles (NFR)**, ex. :
  - NFR1 : exécution automatique quotidienne (pas d'action manuelle requise).
  - NFR2 : traçabilité — sources inaccessibles signalées explicitement, jamais masquées.
  - NFR3 : le périmètre (sujets/seuils/sources) reste facilement modifiable (CLAUDE.md déjà conçu pour).
- **Epics** (regroupements de stories, voir Phase 4) :
  - Epic A — Automatisation de l'exécution quotidienne.
  - Epic B — Collecte et validation des sources par sujet.
  - Epic C — Génération et mise en forme du digest.
  - Epic D — Historisation / consultation des digests passés.

---

## Phase 3 — Architect : Document d'architecture

**Rôle simulé** : Architecte technique. Décide *comment* réaliser le PRD avec les outils disponibles dans cet environnement Claude Code.

**À produire** — `docs/architecture.md` :
- **Orchestration** : skill `schedule` → routine cloud quotidienne qui invoque le prompt de veille.
- **Collecte** : outils `WebSearch` / `WebFetch` pour chaque sujet, avec règle stricte de vérification de lecture effective avant résumé (contrainte FR3).
- **Filtrage** : logique de seuils par sujet définie dans CLAUDE.md, appliquée avant inclusion dans le digest.
- **Sortie / stockage** : soit fichier Markdown daté (`digests/YYYY-MM-DD.md`), soit page Artifact republiée chaque jour (si consultation web souhaitée — nécessite le skill `artifact-capabilities`).
- **Visualisation optionnelle** : skill `dataviz` si des graphiques de tendance (or, cuivre, indices) sont ajoutés plus tard.
- **Points ouverts à trancher** : format de stockage (fichier vs artifact), conservation d'un historique ou non, canal de notification (juste le fichier, ou une notif push).

---

## Phase 4 — PO/Scrum Master : Sharding en epics/stories

**Rôle simulé** : Product Owner / Scrum Master. Découpe le PRD + l'architecture en stories exécutables une par une, avec critères d'acceptation clairs.

**À produire** — `docs/stories/*.md`, une story = une unité de travail livrable, ex. :
- Story A1 : configurer la routine `schedule` quotidienne.
- Story B1 : implémenter la collecte + vérification de lecture pour le sujet "Bourse".
- Story B2 à B6 : idem pour les 5 autres sujets.
- Story C1 : template de mise en forme du digest (3-4 lignes/sujet, mention explicite des sources non lues).
- Story D1 : choix et mise en place du mode de stockage/historisation.

Chaque story doit être petite, testable indépendamment, et contenir ses critères d'acceptation (ex. "si une source renvoie une erreur, le sujet correspondant l'indique explicitement au lieu d'être omis silencieusement").

---

## Phase 5 — Dev : Implémentation story par story

**Rôle simulé** : Développeur. Implémente une story à la fois, dans l'ordre, sans anticiper les suivantes.

- Traiter les stories dans l'ordre du backlog (Phase 4).
- Chaque story se termine par une vérification par rapport à ses critères d'acceptation avant de passer à la suivante.

---

## Phase 6 — QA : Revue

**Rôle simulé** : QA. Vérifie chaque livrable par rapport au PRD et à la règle absolue de CLAUDE.md (jamais de résumé sans lecture réussie).

- Vérifier notamment que les sources inaccessibles sont bien signalées et non extrapolées (règle absolue).
- Vérifier le respect du format (longueur, contenu par sujet).
- Vérifier que les seuils déclencheurs sont correctement appliqués.

---

## Prochaine étape concrète

Commencer par la Phase 1/2 fusionnées vu la taille du projet : valider ensemble le PRD (`docs/prd.md`) — en particulier trancher les points ouverts d'architecture (stockage du digest, historisation) — avant de configurer la routine `schedule`.
