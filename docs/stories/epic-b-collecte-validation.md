# Epic B — Collecte et validation des sources

Référence : [architecture.md](../architecture.md) §2, [prd.md](../prd.md) FR1-FR3

## Story B1 — Recherche et validation des sources, par sujet

**Description** : pour chacun des 6 sujets définis dans CLAUDE.md, rechercher des sources gratuites pertinentes (WebSearch), les récupérer effectivement (WebFetch), et n'utiliser que celles réellement lues avec succès.

**Statut** : ✅ implémentée et renforcée le 2026-07-25 — intégrée dans le prompt de la routine `radar-de-veille-quotidien` (Story A1), étapes 1-2 et 4. Correction apportée : le prompt initial ne référençait pas la liste de sources curatée dans le PRD ; il pointe désormais explicitement vers `docs/prd.md §3` comme point de départ de la recherche par sujet. Pas de story séparée par sujet : la même logique s'applique aux 6, seuls les mots-clés/sources varient.

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
