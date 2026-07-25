# PRD — Radar de veille

Statut : validé avec l'utilisateur le 2026-07-25.
Référence périmètre/règles : [CLAUDE.md](../CLAUDE.md)

## 1. Exigences fonctionnelles (FR)

- **FR1** — Le système collecte des informations pour chacun des 6 sujets suivis chaque jour : Bourse, Tech/IA/IT, Robot/Drone, Climat, Matières premières, Immobilier Paris.
- **FR2** — Un sujet n'apparaît dans le digest du jour que si son déclencheur spécifique (défini dans CLAUDE.md) est atteint. Pas de remplissage artificiel si rien ne déclenche.
- **FR3** — Règle absolue : aucune source n'est résumée si elle n'a pas été effectivement récupérée et lue. Une source inaccessible (paywall, erreur, contenu vide) est signalée explicitement dans le digest, jamais extrapolée depuis le titre/URL.
- **FR4** — Chaque sujet retenu respecte le format : 3-4 lignes max, contenant la tendance + l'explication du déclenchement de l'alerte.
- **FR5** — Le digest du jour est historisé dans un fichier daté `digests/YYYY-MM-DD.md`, consultable ultérieurement.
- **FR6** — Uniquement des sources en accès libre/gratuit (pas d'abonnement payant disponible côté utilisateur) : presse spécialisée gratuite, sources officielles (banques centrales, INSEE, notaires, régulateurs, GIEC/ADEME, etc.).

## 2. Exigences non-fonctionnelles (NFR)

- **NFR1** — Exécution automatique quotidienne, sans action manuelle de l'utilisateur (via routine `schedule`).
- **NFR2** — Traçabilité : chaque affirmation du digest doit pouvoir être reliée à une source réellement consultée ce jour-là.
- **NFR3** — Le périmètre (sujets, seuils, sources, langues, zones) reste piloté par CLAUDE.md et modifiable sans toucher au reste du système.
- **NFR4** — L'historique (`digests/`) ne doit pas être écrasé : chaque jour produit un fichier distinct.

## 3. Sources retenues par sujet (accès libre uniquement)

| Sujet | Sources |
|---|---|
| 1. Bourse | Indices CAC 40, Euro Stoxx 50, S&P 500, Nasdaq, SSE Composite/Hang Seng ; Zonebourse, Investing.com, communiqués officiels BCE/Fed/PBoC |
| 2. Tech/IA/IT | MIT Technology Review, Ars Technica, Numerama, blogs officiels des labs (OpenAI, Anthropic, Google DeepMind, Mistral), Stratechery |
| 3. Robot/Drone | Usine Nouvelle, La Tribune, Air&Cosmos, communiqués DGA/Ministère des Armées, BPI France, Les Echos Industrie (articles en accès libre) |
| 4. Climat | GIEC/IPCC, ADEME, Ministère de la Transition écologique, Carbone 4 |
| 5. Matières premières | LBMA (or), LME (cuivre), World Gold Council, Trading Economics, Kitco |
| 6. Immobilier Paris | Chambre des Notaires de Paris/Île-de-France, INSEE, base Immobilier.notaires.fr |

> Cette liste est indicative, pas exhaustive : à chaque exécution, le radar peut chercher d'autres sources gratuites correspondant aux critères de CLAUDE.md (spécialisé/officiel, zones et langues autorisées).

## 4. Décisions d'architecture actées

- **Stockage/historisation** : fichier Markdown daté par jour, `digests/YYYY-MM-DD.md`. Pas d'écrasement, pas de fichier unique cumulatif.
- **Sources** : accès libre uniquement, aucun abonnement payant à exploiter.

## 5. Epics

- **Epic A — Automatisation** : mise en place de la routine `schedule` quotidienne.
- **Epic B — Collecte et validation des sources** : une story par sujet, avec vérification de lecture effective (FR3) avant tout résumé.
- **Epic C — Génération du digest** : template respectant FR2 et FR4, assemblage des sujets retenus.
- **Epic D — Historisation** : écriture dans `digests/YYYY-MM-DD.md` (FR5, NFR4).

## 6. Hors périmètre (pour l'instant)

- Visualisations graphiques (dataviz) — à revisiter si besoin plus tard.
- Publication en page web (Artifact) — à revisiter si besoin plus tard.
- Notifications push.
