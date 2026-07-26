# Epic F — Simulation d'investissement PEA

Ajouté le 2026-07-26, à la demande de l'utilisateur : simuler une allocation de 10 000 € dans un PEA (Plan d'Épargne en Actions), en s'appuyant sur les tendances technologiques et la situation géopolitique déjà détectées par le radar, pour identifier des actions françaises à surveiller.

**Avertissement (à rappeler à chaque itération de cet epic)** : il s'agit d'une analyse informative construite à partir de sources publiques et des signaux du radar — **pas un conseil en investissement personnalisé**. Aucune recommandation d'achat ferme n'est faite ; l'utilisateur reste seul décisionnaire et est invité à consulter un conseiller financier agréé avant toute décision réelle, en particulier compte tenu du montant (10 000 €) et du cadre fiscal du PEA.

**Contrainte structurelle du PEA** : seules les actions de sociétés dont le siège est dans l'UE/EEE sont éligibles (avec quelques exceptions type ETF PEA synthétiques) — le périmètre est donc naturellement limité aux valeurs françaises/européennes, ce qui correspond à la demande de l'utilisateur.

## Story F1 — Croisement des signaux radar avec des valeurs françaises éligibles PEA

**Description** : à partir des thèmes déjà suivis par le radar (Bourse, Tech/IA, Robot/Drone, Matières premières, Climat), identifier des sociétés françaises cotées et éligibles PEA dont l'activité est directement exposée à ces tendances (ex. défense/drones face aux tensions géopolitiques, semi-conducteurs/logiciels face à la dynamique IA).

**Statut** : ✅ réalisée le 2026-07-26 — voir échange en conversation pour la liste et le raisonnement détaillé.

## Story F2 — Vérification des données via sources réelles

**Description** : ne jamais avancer un cours de bourse ou une donnée financière sans l'avoir récupérée d'une source réelle (même règle absolue que le reste du radar) — sinon, le signaler explicitement comme non vérifié.

**Statut** : en cours — dépend de la disponibilité d'un outil de recherche/lecture web dans la conversation au moment de l'analyse (contrairement à la routine cloud, cette conversation ne dispose pas nécessairement de WebSearch).

## Story F3 — Simulation d'allocation (à titre illustratif uniquement)

**Description** : proposer une répartition illustrative des 10 000 € entre les valeurs identifiées, pour donner un ordre de grandeur — pas une recommandation d'exécution.

**Statut** : ✅ réalisée le 2026-07-26 — 7 lignes (Thales, Dassault Aviation, Valeo, Safran, Capgemini, STMicroelectronics, réserve de liquidités), voir détail dans la conversation et sur le tableau de bord (Story F4).

## Story F4 — Tableau de bord de la simulation

**Description** : publier un tableau de bord dédié à la simulation PEA — répartition des 10 000 € (graphique part-du-tout, méthode dataviz), fiche par valeur (cours, variation, secteur, lien avec les signaux du radar, niveau de risque), et rappel visible que ce n'est pas un conseil en investissement personnalisé.

**Critères d'acceptation** :
- [ ] Graphique de répartition (barre empilée horizontale, palette catégorielle à ordre fixe, étiquettes directes).
- [ ] Une carte par valeur avec cours/variation réels sourcés (pas de chiffre inventé).
- [ ] Bandeau d'avertissement non-conseil visible en haut de page.
- [ ] Lien vers le tableau de bord projet.

**Statut** : ✅ publiée le 2026-07-26.

## Story F5 — Suivi quotidien automatique du portefeuille simulé

**Description** : la routine quotidienne vérifie chaque jour le cours réel des 6 valeurs de la simulation (Thales, Dassault Aviation, Valeo, Safran, Capgemini, STMicroelectronics) et met à jour `dashboards/simulation-pea.html` en conséquence, republié à la même URL — pour transformer la simulation ponctuelle en suivi de performance dans le temps.

**Critères d'acceptation** :
- [x] Chaque jour, la routine tente de récupérer le cours réel de chacune des 6 valeurs (WebSearch/WebFetch, sources gratuites).
- [x] Aucun cours n'est jamais inventé : une valeur non vérifiée aujourd'hui garde son dernier cours connu, avec une note explicite "(non mis à jour aujourd'hui)".
- [x] Le dashboard est republié à l'URL fixe existante (pas de nouveau lien).
- [ ] Comme pour l'Epic E, un échec de cette étape (NFR5) n'empêche jamais l'écriture/le merge du digest quotidien principal. *(non testé : aucun échec rencontré jusqu'ici pour observer le comportement de repli)*

**Statut** : ✅ validée le 2026-07-26 sur un run réel — commit `e9ab920` : cours réels des 6 valeurs récupérés via Boursorama. Marchés fermés le jour même (dimanche 26/07) : la routine a correctement daté les cours de la dernière séance (24/07) et signalé explicitement "(24/07, dernière séance — marché fermé le 26/07)" au lieu de présenter une fausse variation du jour — comportement exactement conforme à la règle absolue et au 2ᵉ critère ci-dessus.

## Story F6 — Suivi de performance à 2 semaines et recommandations de révision

**Description** : demande de l'utilisateur (2026-07-26) — vérifier, sur une fenêtre de 2 semaines, si les 6 valeurs choisies (la « recommandation » de la simulation) se comportent bien réellement, avec un suivi quotidien de la performance et, à l'issue des 2 semaines, une évaluation explicite avec recommandation de révision par valeur.

**Mécanique** :
- Date de départ (J0) : 2026-07-26, prix d'entrée = les cours réels capturés lors de la première mise à jour F5 (`entryPrice` figé dans chaque position de `dashboards/simulation-pea.html`, ne jamais le modifier).
- Chaque jour, après la mise à jour des cours (Story F5), calculer la performance de chaque valeur depuis l'entrée (`(prix_du_jour / entryPrice - 1) × 100`) et la performance globale du portefeuille pondérée par montant investi (réserve de liquidités comptée à 0 % au dénominateur des 10 000 €). Ajouter une entrée `{date, perf_pct, prices}` à `docs/portfolio-history.json` (append-only, jamais de remplacement).
- Le dashboard affiche : la performance globale du jour, une sparkline de la performance cumulée (J1→J14), et la performance individuelle depuis l'entrée sur chaque fiche valeur.
- **Au J+14 (2026-08-09)** : la routine reformule la thèse initiale de chaque valeur (voir Story F1/F3) et la revérifie via une recherche fraîche (WebSearch/WebFetch — pas de simple relecture de l'ancienne source), puis rend un verdict global et, pour chacune des 6 valeurs, une recommandation explicite parmi : **conserver**, **renforcer**, **alleger**, **remplacer** (mots-clés sans accent, utilisés tels quels comme classe CSS sur le dashboard), avec une justification courte sourcée. Ce bilan est écrit dans le bloc `review` de `dashboards/simulation-pea.html` (voir marqueurs `__PORTFOLIO_REVIEW_DATA_START__`/`END__`) sous la forme `{date, verdict, recommendations:[{name, action, reason}]}`.

**Critères d'acceptation** :
- [ ] Chaque jour (J1 à J14), une entrée est ajoutée à `docs/portfolio-history.json` — jamais de recalcul rétroactif des entrées passées.
- [ ] Le dashboard reflète la performance à jour (figure globale, sparkline, performance par valeur) sans que `entryPrice`/`entryDate` ne soient jamais modifiés.
- [ ] Au J+14, un bilan complet (verdict + recommandation par valeur, sourcée) est publié sur le dashboard — jamais de recommandation sans revérification réelle de la thèse.
- [ ] Avertissement « non-conseil personnalisé » toujours présent, y compris dans le bilan à 2 semaines.
- [ ] NFR5 : comme les autres étapes post-digest, un échec de cette étape n'empêche jamais l'écriture/le merge du digest quotidien principal.

**Statut** : ⏳ ajoutée le 2026-07-26 (dashboard, historique et instructions de routine mis à jour) — dashboard republié avec la structure de suivi en place (J1 seulement pour l'instant) ; premier vrai test du suivi quotidien attendu à partir du prochain run (J2), bilan complet attendu le 2026-08-09.

### Révision interim STMicroelectronics (2026-07-26, avant le bilan formel du 09/08)

**Déclencheur** : à la demande de l'utilisateur, calcul d'un scénario contrefactuel (« et si la simulation avait démarré début juillet ? ») avec les cours réels du 01/07/2026 (sourcés via l'historique Boursorama) comparés aux cours du 24/07/2026. Résultat : le portefeuille hypothétique afficherait -1,13 % sur la période, entièrement tiré par STMicroelectronics (-25,01 % depuis le 01/07), les 5 autres valeurs étant stables ou positives (Valeo +8,62 %, Thales +3,05 %, Dassault Aviation +2,33 %, Capgemini -0,93 %, Safran -5,75 %).

**Analyse fraîche (revérification de la thèse, sourcée)** : le repli de STMicroelectronics du 23/07/2026 vient d'un T2 2026 qui a pourtant battu le consensus (BPA ajusté 0,31$ vs 0,25$ attendu, retour au bénéfice net 222 M$, objectifs datacenter IA relevés à >1 Md$ en 2026 / >2 Md$ en 2027) — la chute (-17,7% à Paris) est due à la guidance T3 et à la pression sur les marges, pas à une dégradation de la demande. Sentiment analystes majoritairement « Hold », avis partagés entre haussiers (comparaisons faciles à venir, normalisation des stocks) et baissiers (pression sur les marges jusqu'en 2026). **Conclusion : la thèse initiale n'est pas invalidée — elle se réalise comme anticipé (position déjà marquée « risque élevé » dès la création) — mais l'ampleur du drawdown justifie de réduire l'exposition plutôt que d'attendre passivement le bilan du 09/08.**

**Décision (validée par l'utilisateur)** : allégement de STMicroelectronics de 1000 € à 500 €. Capital libéré (500 €) réparti au prorata sur les 5 autres valeurs : Thales 2000→2118 €, Dassault Aviation 2000→2118 €, Valeo 1500→1588 €, Safran 1500→1588 €, Capgemini 1500→1588 €. Réserve de liquidités inchangée (500 €). Le rééquilibrage a lieu le jour même de l'entrée (`entryDate` = 2026-07-26 pour toutes les positions), donc aucun impact sur le calcul de performance depuis l'entrée ni sur l'historique déjà écrit dans `docs/portfolio-history.json` (toujours à J0, perf 0%).

**Statut** : ✅ appliquée le 2026-07-26 — dashboard mis à jour et republié ; ne remplace pas le bilan formel du 09/08 (Story F6 ci-dessus), qui reste dû avec revérification complète des 6 thèses.

### Story F7 — Ajout d'une exposition pure-play au thème Drone

**Déclencheur** : remarque de l'utilisateur (2026-07-26) — aucune des 6 valeurs de la simulation n'est un pur acteur drone (exposition seulement indirecte via Thales/Valeo/Dassault Aviation). Recherche de véritables sociétés françaises cotées spécialisées :
- **Exail Technologies (EXA, ex-Groupe Gorgé)** — le candidat le plus sérieux (drones marins DriX-H9, sous-marins K-STER, entrée au SBF 120 sept. 2025) mais **écarté** : Thales a annoncé son rachat le 06/07/2026 (3,9 Md€, 134 €/action, +44% de prime), titre à 124,20 € le 25/07 → situation d'arbitrage de fusion à horizon 2028, pas une thèse de croissance, et exposition déjà redondante avec Thales détenu par ailleurs.
- **Tonner Drones (ALTD)** — écarté : nano-cap (~19 M€), holding de participations (Diodon, Elistair, Donecle) plutôt que fabricant opérant, cours ~0,03 €, trop spéculatif/illiquide.
- **Drone Volt (ALDRV, Euronext Growth)** — retenu : seul vrai pur-play drone opérant identifié. Fabricant français de drones civils/sécurité/défense, présent à Eurosatory 2026, marge brute passée de 13% à 37% (2024→2025) après recentrage, mais perte nette -13,8 M€ en 2025 et dilution en cours (77,5M→~85,9M actions en base diluée). Cours 0,3648 € (24/07/2026, dernière séance).

**Décision (validée par l'utilisateur)** : ajout de Drone Volt pour 300 €, financé par une réduction de la réserve de liquidités (500 € → 200 €), avec le badge de risque le plus élevé du portefeuille (« très élevé », distinct visuellement du badge « élevé » de STMicroelectronics). Allocation finale : 8 lignes, toujours 10 000 € au total.

**Critères d'acceptation** :
- [x] Position ajoutée avec `entryPrice`/`entryDate` réels et sourcés (pas de cours inventé).
- [x] Risque affiché distinctement des autres lignes (badge « très élevé »).
- [x] Alternatives sérieuses (Exail, Tonner Drones) documentées avec la raison de leur rejet, pas juste ignorées.
- [ ] Suivi quotidien (Story F5/F6) étendu à Drone Volt — routine mise à jour en conséquence, à valider sur le prochain run réel.

**Statut** : ✅ ajoutée le 2026-07-26 — dashboard republié, routine mise à jour (7 valeurs suivies désormais), `docs/portfolio-history.json` synchronisé.
