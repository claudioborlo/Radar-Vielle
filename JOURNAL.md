# Journal du projet — Radar de veille

Ce fichier garde la trace des échanges et décisions autour du projet de radar de veille (voir [CLAUDE.md](CLAUDE.md) pour le périmètre officiel). Mis à jour progressivement au fil des discussions.

## 2026-07-25

- Périmètre du radar défini dans CLAUDE.md : 6 sujets suivis (Bourse, Tech/IA, Robot/Drone, Climat, Matières premières, Immobilier Paris), format digest quotidien 3-4 lignes/sujet, règle absolue de ne jamais résumer une source non lue.
- Question : quels skills Claude Code utiliser pour mieux générer ce programme de veille ?
- Recommandation donnée :
  - **schedule** — pour automatiser l'exécution quotidienne du digest (routine cloud sur cron).
  - **dataviz** — optionnel, pour visualiser les seuils de variation (≥2%) sous forme de graphiques plutôt que texte brut.
  - **artifact-design** / **artifact-capabilities** — optionnel, pour publier le digest comme page web consultable, éventuellement auto-actualisée.
  - La collecte des sources elle-même passe par les outils WebSearch/WebFetch directement (pas un skill dédié).
- Prochaine étape envisagée : mettre en place la routine `schedule` pour automatiser l'exécution quotidienne — en attente de confirmation utilisateur.
- Demande : simuler la méthode BMAD (Analyst → PM → Architect → PO/SM → Dev → QA) et documenter les étapes nécessaires au développement du projet.
- Créé [BMAD-METHODE.md](BMAD-METHODE.md) : découpage du projet en 6 phases BMAD avec livrables associés (project-brief, PRD, architecture, stories, implémentation, QA). Points ouverts identifiés côté architecture : format de stockage du digest (fichier daté vs Artifact republié) et historisation. Prochaine étape suggérée : valider le PRD et trancher ces points avant de configurer `schedule`.
- Décisions actées : historique conservé (un fichier daté par jour, `digests/YYYY-MM-DD.md`), sources en accès libre uniquement (pas d'abonnement payant côté utilisateur).
- Créé [docs/prd.md](docs/prd.md) : PRD validé avec FR1-FR6, NFR1-NFR4, liste de sources gratuites par sujet, décisions d'architecture actées (stockage/historisation), epics A-D, hors périmètre (dataviz, publication web, notifs — reportés).
- Prochaine étape : phase Architect (docs/architecture.md) puis sharding en stories (Epic A en premier : routine `schedule`).
- Créé [docs/architecture.md](docs/architecture.md) : flux complet (schedule → WebSearch/WebFetch par sujet → évaluation déclencheur → digest → écriture digests/YYYY-MM-DD.md), composants mappés aux epics, format du fichier de digest.
- Décisions actées : sujets sans déclencheur → mention "rien à signaler" (pas d'omission) ; exécution quotidienne à 7h Paris ; dossier `digests/` à la racine du projet.
- Créé [docs/stories/epic-a-automatisation.md](docs/stories/epic-a-automatisation.md) : Story A1 (configurer la routine schedule quotidienne à 7h) — prête, en attente du go utilisateur pour activation réelle.
- Prochaine étape : soit activer réellement la routine (Story A1) via le skill `schedule`, soit continuer le sharding des Epics B/C/D avant toute implémentation.
