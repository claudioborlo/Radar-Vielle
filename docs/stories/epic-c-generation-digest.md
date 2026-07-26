# Epic C — Génération du digest

Référence : [architecture.md](../architecture.md) §3, [prd.md](../prd.md) FR2, FR4

## Story C1 — Assemblage du digest du jour

**Description** : pour chaque sujet, évaluer si son déclencheur spécifique (CLAUDE.md) est atteint ; rédiger 3-4 lignes (tendance + explication) si oui, "Rien à signaler aujourd'hui." si non ; assembler le tout au format Markdown daté.

**Statut** : ✅ implémentée — intégrée dans le prompt de la routine (Story A1), étapes 3-4 et gabarit de sortie.

**Critères d'acceptation** (à vérifier sur un digest réel produit) :
- [ ] Un bloc par sujet (6 au total), dans l'ordre de CLAUDE.md.
- [ ] Un sujet sans déclencheur atteint affiche "Rien à signaler aujourd'hui." (pas d'omission — décision actée le 2026-07-25).
- [ ] Un sujet avec déclencheur atteint fait 3-4 lignes maximum, contenant la tendance ET l'explication du déclenchement.
- [ ] Le fichier a la date du jour en titre.
- [ ] Une section distincte liste les sources inaccessibles du jour, si applicable.

**Comment tester** : inspecter `digests/YYYY-MM-DD.md` après une exécution de la routine et cocher chaque critère. À valider sur le run de test déclenché le 2026-07-25.

## ✅ Validé (2026-07-26)

Premier run quotidien complet réel : `digests/2026-07-26.md` (PR #7, mergée automatiquement). Tous les critères vérifiés : 6 blocs dans l'ordre de CLAUDE.md, "Rien à signaler aujourd'hui" utilisé correctement pour Immobilier (aucun seuil atteint), sources citées avec liens pour chaque sujet déclenché, section "Sources inaccessibles aujourd'hui" présente et correcte.
