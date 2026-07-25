# Epic A — Automatisation

Référence : [architecture.md](../architecture.md) §2, [prd.md](../prd.md) §5

## Story A1 — Configurer la routine quotidienne

**Description** : mettre en place une routine cloud (skill `schedule`) qui exécute le radar de veille automatiquement chaque jour à 7h Paris, sans action manuelle de l'utilisateur.

**Critères d'acceptation** :
- La routine se déclenche chaque jour à 7h (heure de Paris).
- Le prompt exécuté par la routine applique le périmètre complet de CLAUDE.md (6 sujets, seuils, sources gratuites, règle absolue de lecture réelle).
- Le résultat est écrit dans `digests/YYYY-MM-DD.md` (date du jour d'exécution).
- Statut : **à faire** — nécessite une action de configuration réelle (création d'un job planifié) à valider avec l'utilisateur avant activation.

**Statut** : prête à être implémentée (en attente de go pour activer réellement la routine).
