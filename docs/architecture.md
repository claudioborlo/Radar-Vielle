# Architecture — Radar de veille

Statut : à valider avec l'utilisateur.
Référence : [CLAUDE.md](../CLAUDE.md), [prd.md](prd.md)

## 1. Vue d'ensemble du flux

```
schedule (routine quotidienne)
        │
        ▼
Pour chaque sujet (1..6 définis dans CLAUDE.md) :
        │
        ├─ Recherche de sources gratuites pertinentes (WebSearch)
        ├─ Récupération effective du contenu (WebFetch)
        │     └─ Échec/paywall/vide → sujet marqué "source inaccessible", jamais résumé (FR3)
        ├─ Évaluation du déclencheur spécifique au sujet (CLAUDE.md)
        │     └─ Non atteint → sujet exclu du digest du jour (FR2)
        └─ Rédaction 3-4 lignes (tendance + explication du déclenchement) (FR4)
        │
        ▼
Assemblage du digest du jour
        │
        ▼
Écriture dans digests/YYYY-MM-DD.md (FR5, NFR4)
```

## 2. Composants techniques

| Composant | Réalisation | Lié à |
|---|---|---|
| Orchestration/planification | Skill `schedule` → routine cloud exécutée 1x/jour | Epic A, NFR1 |
| Recherche de sources | Outil `WebSearch`, requêtes ciblées par sujet (mots-clés + sources listées dans prd.md §3) | Epic B |
| Récupération de contenu | Outil `WebFetch` sur chaque URL candidate ; un échec = source non lue, jamais de résumé basé sur le titre/snippet seul | Epic B, FR3 |
| Évaluation des déclencheurs | Logique de seuils définie sujet par sujet dans CLAUDE.md (ex. variation ≥2%, rupture technologique, décision de politique publique) | Epic B, FR2 |
| Génération du digest | Template texte : 1 bloc par sujet retenu, 3-4 lignes, mention explicite si une source du sujet était inaccessible | Epic C, FR4 |
| Historisation | Écriture d'un nouveau fichier `digests/YYYY-MM-DD.md` à chaque exécution, aucun écrasement | Epic D, FR5, NFR4 |

## 3. Format du fichier de digest

```markdown
# Radar de veille — YYYY-MM-DD

## Bourse
[tendance] — [explication du déclenchement]. Sources : [liste].

## Tech / IA / IT
...

## Robot / Drone
Rien à signaler aujourd'hui.

## Sources inaccessibles aujourd'hui
- [Sujet] : [source] — [raison : paywall / erreur / contenu vide]
```

## 4. Pourquoi ce choix plutôt que les alternatives écartées

- **Fichier daté vs fichier unique cumulatif** : un fichier par jour évite qu'une erreur d'écriture corrompe tout l'historique, et rend chaque digest indépendant et archivable (NFR4).
- **Pas d'Artifact republié pour l'instant** : simplicité — un simple fichier Markdown suffit au besoin actuel (consultation par toi seul). Peut être ajouté plus tard sans changer la logique de collecte (composant indépendant).
- **Pas de dataviz pour l'instant** : le format texte court (3-4 lignes) ne justifie pas encore de graphique ; à revisiter si le suivi de matières premières/immobilier s'enrichit de séries longues.

## 5. Décisions actées (2026-07-25)

1. **Sujets sans déclencheur atteint** : mentionnés brièvement avec "rien à signaler" plutôt qu'omis — confirme que le sujet a bien été vérifié ce jour-là.
2. **Heure d'exécution** : 7h Paris, avant l'ouverture des marchés européens.
3. **Dossier `digests/`** : créé à la racine du projet, à côté de CLAUDE.md.
