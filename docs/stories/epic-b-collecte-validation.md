# Epic B — Collecte et validation des sources

Référence : [architecture.md](../architecture.md) §2, [prd.md](../prd.md) FR1-FR3

## Story B1 — Recherche et validation des sources, par sujet

**Description** : pour chacun des 7 sujets définis dans CLAUDE.md, rechercher des sources gratuites pertinentes (WebSearch), les récupérer effectivement (WebFetch), et n'utiliser que celles réellement lues avec succès.

**Statut** : ✅ implémentée et renforcée le 2026-07-25 — intégrée dans le prompt de la routine `radar-de-veille-quotidien` (Story A1), étapes 1-2 et 4. Correction apportée : le prompt initial ne référençait pas la liste de sources curatée dans le PRD ; il pointe désormais explicitement vers `docs/prd.md §3` comme point de départ de la recherche par sujet. Pas de story séparée par sujet : la même logique s'applique aux 7, seuls les mots-clés/sources varient.

## Story B2 — Ajout du 7ᵉ sujet : Cryptomonnaies (2026-07-26)

**Déclencheur** : demande de l'utilisateur (2026-07-26) — ajouter une surveillance sur les cryptomonnaies au radar, en plus des 6 sujets existants.

**Périmètre ajouté** : Bitcoin et Ethereum, angle investissement + actualité réglementaire (UE/MiCA, USA/SEC). Déclencheur spécifique (au lieu du générique ≥2%, jugé trop sensible vu la volatilité structurelle du marché crypto) : variation ≥5% en 24h sur BTC ou ETH, ou décision réglementaire majeure. Voir [CLAUDE.md](../../CLAUDE.md) §Sujets suivis point 7 et [prd.md](../prd.md) §3 (sources : CoinGecko, CoinDesk, Zonebourse, ESMA/AMF, SEC).

**Propagation** : même logique que Story B1 (pas de story séparée par sujet) — le prompt de la routine (Story A1) et le tableau de bord de tendances (Epic E) ont été mis à jour en conséquence le même jour pour intégrer ce 7ᵉ sujet.

**Statut** : ✅ ajoutée le 2026-07-26 — validée par un test scopé réel (PR #8, voir note ci-dessous) : sources CoinGecko (BTC/ETH) effectivement récupérées, seuil de 5%/24h correctement évalué comme non atteint (BTC +0,6%, ETH +1,3%), aucune décision réglementaire majeure inventée. Prompt de la routine restauré à sa version complète (7 sujets) juste après le test.

**Test technique isolé (2026-07-26)** : prompt de la routine temporairement remplacé par une version scopée au seul sujet Cryptomonnaies, `RemoteTrigger run` déclenché manuellement. Résultat écrit dans `digests/_test-crypto.md`, commité et mergé automatiquement via **PR #8**. Confirme que le mécanisme (recherche → lecture réelle → évaluation du déclencheur → écriture → merge auto) fonctionne aussi pour ce 7ᵉ sujet, sur le même modèle que les tests individuels des 6 sujets précédents (PR #1-#6).

## Story B3 — Tableau de bord dédié Drone + correction d'un bug d'échelle (2026-07-26)

**Déclencheur** : remarque de l'utilisateur — le graphique « Variation par valeur / indice » (sujet Bourse) affichait des valeurs incohérentes avec son échelle, et il manquait un tableau de bord dédié au sujet Drone (sur le modèle du tableau de bord Bourse existant).

**Bug corrigé** : le graphique en barres divergentes (tableau de bord projet + tableau de bord Bourse dédié) utilise une échelle symétrique ±8% (`var scale = 8; // symmetric half-scale in %`), mais l'étiquette d'axe affichait à tort « +2 % » côté positif au lieu de « +8 % » — incohérence entre l'échelle réellement utilisée pour dessiner les barres et le repère affiché à l'utilisateur. Corrigé sur les deux pages concernées.

**Tableau de bord Drone dédié créé** : `dashboards/alertes-drone.html`, publié à https://claude.ai/code/artifact/8ec3c247-559a-411f-a60f-3142afa5128d — reprend les alertes du sujet Drone (AndroMach, Valeo x Harmattan AI) et ajoute un **radar chart** (petits multiples, un par valeur) comparant les 6 sociétés de la simulation Drones (Story F9) sur 4 axes normalisés 0-100 : Performance (depuis l'entrée), Stabilité (inverse du risque affiché), Liquidité (capitalisation, échelle log) et Conviction (poids dans l'allocation). Méthodologie de calcul documentée explicitement sur la page (transformations de données déjà sourcées, pas des notes inventées) — vue tableau incluse pour l'accessibilité.

**Persistance** : les deux dashboards « historiques » qui n'avaient jamais eu de fichier source dans le repo (tableau de bord projet, tableau de bord Bourse dédié) sont désormais versionnés — `dashboards/project-dashboard.html` et `dashboards/alertes-bourse.html` — pour permettre leur maintenance future sans dépendre uniquement de l'état publié sur claude.ai.

**Statut** : ✅ appliquée le 2026-07-26 — 3 dashboards republiés (tableau de bord projet, Alertes Bourse corrigé, Alertes & Radar Drone nouveau), liens croisés ajoutés entre eux.

### Correction complémentaire (2026-07-26) — repère d'axe désaligné de la barre

**Déclencheur** : remarque de l'utilisateur — malgré la correction précédente (« +2 % » → « +8 % »), des barres semblaient toujours dépasser visuellement le repère d'axe.

**Diagnostic réel** : chaque ligne du graphique utilise une grille à deux colonnes (`8.5rem` pour le libellé + `1fr` pour la piste de la barre), mais le repère d'axe sous le graphique (`-8 % / 0 / +8 %`) était un simple `flex` sur toute la largeur de la carte, sans tenir compte de la colonne de libellé. Le tick « 0 » du repère ne tombait donc pas à l'aplomb de la ligne de base réelle des barres (décalée à droite par la colonne de libellé), donnant l'impression que les barres dépassaient l'axe alors que c'est le repère qui était mal aligné.

**Corrections appliquées** (sur le tableau de bord projet et Alertes Bourse) :
- Le repère d'axe utilise désormais la même grille à deux colonnes que les lignes du graphique, pour que « 0 » tombe exactement sur la ligne de base et que les bords gauche/droit correspondent aux extrémités réelles des pistes.
- Échelle relevée de ±8% à ±10% pour laisser une marge visuelle au-dessus de la plus forte variation observée (Intel, -7,88%), au lieu de l'amener presque au bord de la piste.

**Statut** : ✅ corrigée le 2026-07-26 — 2 dashboards republiés (tableau de bord projet, Alertes Bourse).

**Critères d'acceptation** (à vérifier sur un digest réel produit) :
- [ ] Chaque sujet mentionné dans le digest cite au moins une source, et cette source a été effectivement récupérée (pas de résumé basé sur un titre/snippet seul).
- [ ] Une source inaccessible (paywall/erreur/vide) est signalée explicitement, jamais résumée (FR3 — règle absolue).
- [ ] Les sources respectent les zones (France, Europe, Chine, USA) et langues (FR, EN, IT) de CLAUDE.md.
- [ ] Aucune source payante n'est utilisée (FR6).

**Comment tester** : inspecter `digests/YYYY-MM-DD.md` produit par une exécution de la routine (Story A1) et cocher chaque critère ci-dessus. Un run de test a été déclenché manuellement le 2026-07-25 — validation à faire sur son résultat.

## 🔴 Blocage critique découvert (2026-07-25, test scopé sujet Drone)

Le test technique réduit au seul sujet Drone a révélé que **WebSearch fonctionne** (candidats pertinents trouvés : levée de fonds Harmattan AI menée par Dassault Aviation, partenariat Harmattan AI/Valeo, commande DGA de 5000 drones DELCO, levée Skydrone Robotics) mais **WebFetch échoue systématiquement (9/9 sources testées, erreur 403)**, y compris sur des domaines neutres (Wikipedia, google.com). Un test `curl` direct dans l'environnement confirme que la politique réseau (egress) de l'environnement cloud bloque quasiment tout le web externe, avec une liste blanche limitée à pypi/npm.

**Conséquence** : la règle absolue (FR3) est respectée — aucune source n'a été résumée sans lecture réussie, le digest de test documente honnêtement l'échec — mais dans ces conditions, **aucun sujet ne peut jamais produire de contenu réel**, seulement des "source inaccessible". Epic B est donc fonctionnellement bloqué tant que la politique réseau de l'environnement cloud (`env_01K217Rx2uxPfYrSqstrfbCu`) n'autorise pas l'accès aux domaines de presse/sources visés.

**Action requise (hors de portée des outils disponibles ici)** : vérifier/modifier la politique d'accès réseau de l'environnement sur claude.ai (paramètres de l'environnement cloud utilisé par la routine) pour autoriser l'accès aux domaines nécessaires, ou changer d'environnement si une option avec accès réseau plus large existe.

### ✅ Résolu (2026-07-25, run suivant sur PR #1)

Un nouveau run scopé Drone a réussi à lire une source réelle : [opex360.com, 23/07/2026](https://www.opex360.com/2026/07/23/avec-lappui-du-cnes-et-de-la-dga-andromach-est-en-bonne-voie-de-faire-voler-un-drone-suborbital-hypersonique/) (levée de fonds AndroMach, drone suborbital hypersonique, soutien DGA/CNES). Une autre source (Usine Nouvelle) est restée en 403 et a été correctement écartée sans être résumée — la règle absolue continue d'être respectée même en cas d'échec partiel. Le blocage réseau semble donc levé, au moins partiellement ; à confirmer sur un run complet 6 sujets.
