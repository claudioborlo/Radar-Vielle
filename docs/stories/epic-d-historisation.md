# Epic D — Historisation

Référence : [architecture.md](../architecture.md) §1, [prd.md](../prd.md) FR5, NFR4

## Story D1 — Écriture et versioning du digest quotidien

**Description** : écrire le digest du jour dans un nouveau fichier `digests/YYYY-MM-DD.md` sans écraser les digests précédents, puis le committer et le pousser sur `main` (repli en pull request si le push direct échoue par manque de droits).

**Statut** : ✅ implémentée — intégrée dans le prompt de la routine (Story A1), dernières instructions (écriture fichier + commit + push/PR).

**Critères d'acceptation** :
- [ ] Un nouveau fichier `digests/YYYY-MM-DD.md` apparaît dans le repo à chaque exécution, avec la date du jour d'exécution (fuseau Europe/Paris).
- [ ] Les fichiers `digests/` des jours précédents ne sont jamais modifiés ni supprimés.
- [ ] Le commit est bien poussé sur `main` — ou, en cas d'échec de push, une pull request est ouverte à la place et signalée dans la réponse de l'agent.

**Comment tester** : après une exécution, vérifier sur `github.com/claudioborlo/Radar-Vielle` (ou via `git fetch && git log origin/main`) qu'un nouveau commit contenant `digests/YYYY-MM-DD.md` est présent sur `main`, ou qu'une PR a été ouverte si le push a échoué. Vérification en cours sur le run de test déclenché le 2026-07-25 (surveillance active en arrière-plan).

## 🔴 Blocage critique confirmé (2026-07-25, test scopé sujet Drone)

Le point d'incertitude est levé, dans le mauvais sens : **l'environnement cloud n'a aucun droit d'écriture sur le repo**, ni en push direct ni en repli PR.

- `git push` direct sur `main` → refusé (403).
- Création de branche via l'API GitHub pour ouvrir une PR (repli prévu par le prompt) → également refusée (403 "Resource not accessible by integration").
- Le commit du digest de test existe bien **localement** dans la session cloud (`digests/_test-drone.md`, correctement rédigé), mais ne peut être publié nulle part, et disparaît avec la session (`persist_session: false`).
- Blocage secondaire, sans impact tant que le push échoue de toute façon : la signature de commit est aussi cassée dans cet environnement (`ssh-keygen` absent, clé de signature vide).

**Conséquence** : Epic D est fonctionnellement bloqué — même si Epic B fonctionnait, aucun digest ne pourrait jamais atteindre le dépôt.

**Action requise (hors de portée des outils disponibles ici)** : revoir les permissions de l'app/intégration GitHub connectée à claude.ai pour cet environnement — elle doit avoir un accès en **écriture** (contents: write, et idéalement pull-requests: write) sur `claudioborlo/Radar-Vielle`, pas seulement en lecture pour le clone.

### ✅ Résolu (2026-07-25)

Après correction côté utilisateur, un run a réussi à pousser une branche (`claude/eager-ride-6m84e1`) et à ouvrir la **pull request #1** sur `claudioborlo/Radar-Vielle`. Le repli en PR fonctionne donc désormais comme prévu par le prompt (le push direct sur `main` reste probablement toujours refusé par design/protection de branche, la PR est le chemin normal). Reste à mettre la PR ou à décider d'une politique de merge (auto-merge vs revue manuelle chaque jour).
