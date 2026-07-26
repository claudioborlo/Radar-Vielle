# Epic E — Publication & analyse de tendances

Ajouté le 2026-07-26, à la demande de l'utilisateur : au-delà de l'alerte du jour, pouvoir analyser l'évolution dans le temps (tendances), et publier le tableau de bord automatiquement chaque jour.

Référence : [prd.md](../prd.md) §2bis (FR7, FR8, NFR5)

**Décisions actées avec l'utilisateur (2026-07-26)** :
- Portée : les **6 sujets** sont suivis dans le temps, y compris les 3 sujets qualitatifs (Tech/IA, Climat, Drone) via une fréquence de déclenchement, pas seulement les 3 sujets chiffrés (Bourse, Matières premières, Immobilier).
- Publication : **automatique chaque jour**, effectuée par la routine cloud elle-même (nécessite l'outil `Artifact` dans `allowed_tools` de la routine).

## Story E1 — Historisation structurée des métriques

**Description** : à chaque exécution quotidienne, après avoir écrit et mergé le digest, la routine enregistre un point de données structuré dans `docs/metrics-history.json` (append-only, un objet par date) :
- Sujets chiffrés (Bourse, Matières premières, Immobilier) : valeurs numériques clés extraites du digest du jour (ex. indices, or/cuivre, prix/m²).
- Sujets qualitatifs (Tech/IA, Climat, Drone) : booléen déclencheur atteint/non atteint.

**Critères d'acceptation** :
- [ ] `docs/metrics-history.json` contient une entrée par jour d'exécution, jamais réécrite rétroactivement.
- [ ] Les 6 sujets sont représentés dans chaque entrée.
- [ ] Le format reste stable (mêmes clés) pour permettre l'agrégation dans le temps.

**Statut** : ⏳ à implémenter — nécessite une mise à jour du prompt de la routine.

## Story E2 — Tableau de bord de tendances

**Description** : une page web (Artifact) dédiée à l'analyse de tendances, distincte du tableau de bord projet (statut BMAD) et du tableau de bord Bourse (alerte du jour) déjà existants. Lit `docs/metrics-history.json` et affiche :
- Courbes d'évolution pour les sujets chiffrés (une par métrique suivie).
- Fréquence de déclenchement dans le temps pour les sujets qualitatifs (ex. barres empilées ou compteur cumulé).
- Vue tableau sous chaque graphique (accessibilité, cf. skill dataviz).

**Critères d'acceptation** :
- [ ] Un graphique par métrique/sujet, utilisant la méthode du skill dataviz (formulaire couleur adapté au job : séquentiel pour magnitude, diverging si polarité, catégoriel pour comparer les sujets qualitatifs entre eux).
- [ ] Le tableau de bord reste lisible même avec un seul jour de données (état honnête "historique en construction"), et devient plus utile à mesure que les jours s'accumulent.
- [ ] Vue tableau présente pour chaque graphique.

**Statut** : ⏳ à implémenter — première version prévue avec les données disponibles au 2026-07-26 (encore peu de recul).

## Story E3 — Publication automatique quotidienne

**Description** : la routine republie elle-même le tableau de bord de tendances chaque jour, sur une **URL stable** (même lien à chaque republication, pas un nouveau lien).

**Critères d'acceptation** :
- [ ] `allowed_tools` de la routine inclut `Artifact`.
- [ ] Le prompt de la routine référence explicitement l'URL de l'artifact existant (paramètre `url` de l'outil Artifact) pour éviter de créer un nouveau lien chaque jour.
- [ ] Si la publication échoue, le digest du jour reste écrit et mergé (NFR5) — la routine signale l'échec de publication dans sa réponse finale sans le traiter comme un échec global du run.

**Statut** : ⏳ à implémenter, en même temps que E1/E2.
