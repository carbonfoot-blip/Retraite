# SPEC-001 : Correction de la perte de focus lors de l'édition des champs (Données & Hypothèses)

## 📌 Métadonnées
- **ID de la Spécification :** SPEC-001
- **Statut :** `En cours`
- **Auteur :** Félix-Antoine Legault / Antigravity
- **Date de création :** 2026-11-08
- **Branche Git cible :** `fix/input-focus-loss`

---

## 1. 🎯 Contexte & Objectif
- **Pourquoi cette évolution ?** Dans plusieurs onglets de la section « Données » (`Profil`, `Épargne`, `Pension A/B`, `RRQ`, `PSV`, `Hypothèses / Héritages`), la saisie d'un caractère (`oninput`) déclenche un re-rendu complet du conteneur DOM (`renderDonneesTab(...)` qui écrase `c.innerHTML`), détruisant l'élément `<input>` actif et faisant perdre le focus à chaque frappe (ou après un délai de 600–1000 ms). De plus, la liste des héritages utilisait `row.outerHTML` après avoir attaché des écouteurs `addEventListener`, ce qui supprimait les écouteurs d'événements.
- **Utilisateur cible :** Utilisateur planifiant sa retraite (individuel ou couple).
- **Bénéfice attendu :** Édition fluide, continue et sans perte de focus sur tous les champs numériques et textes de l'application, avec mise à jour instantanée des indicateurs calculés et sauvegarde automatique.

---

## 2. 🔍 Périmètre (Scope)

### ✅ Ce qui est inclus (In-Scope)
- **Onglet `Profil`** : Suppression de `renderDonneesTab('profil')` sur `oninput` des champs `anneeRetraiteA` et `anneeRetraiteB`. Mise à jour ciblée en place (`updateProfilComputed()`) des âges actuels, textes d'aide (« Dans X an(s) — à Y ans »), titres de scénario et états actifs des boutons `+1/+3/+5 ans`.
- **Onglet `Épargne`** : Mise à jour ciblée en place des indices de répartition REER/CELI (`#hint-pct-reer-a`, `#hint-pct-reer-b`) et du résiduel REEE (`#reee-residuel`) lors de la frappe.
- **Onglets `Pension A` & `Pension B`** : Suppression du re-rendu complet différé (`setTimeout(renderDonneesTab, 1000)`) lors de la frappe dans les champs numériques (`setPension`) qui faisait perdre le focus et reformatait le champ en cours d'édition. Mise à jour en place (`updatePensionComputed(key)`) des 4 cartes de résultats calculés et du statut du montant manuel.
- **Onglet `RRQ`** : Suppression de `renderDonneesTab('rrq')` sur `input` des champs `revenusCotisesMoyens` et des champs de montants manuels (`overrides`). Mise à jour ciblée en place des montants affichés dans les cartes et le résumé officiel sans détruire les `<input>` actifs.
- **Onglet `PSV`** : Suppression de `renderDonneesTab('psv')` sur `input` des champs de montants manuels (`override65`, `override70`). Mise à jour ciblée en place des cartes PSV et du résumé sans détruire l'`<input>` actif.
- **Onglet `Hypothèses (Héritages)`** : Remplacement de `row.outerHTML` par des gestionnaires `oninput`/`onclick` valides et mise à jour ciblée de l'indice d'âge sans re-rendre tout l'onglet pendant la frappe.
- **Recalcul global automatique** : Planification différée (`scheduleRecalcul()`) de `recalculerTout()` après la saisie sans toucher au DOM de l'onglet `Données` actif.

### ❌ Ce qui est exclu (Out-of-Scope)
- Modification des formules financières et fiscales.

---

## 3. ✅ Critères d'Acceptation
- [x] Aucun champ `<input>` de l'application ne perd le focus lorsqu'on tape ou modifie plusieurs caractères d'affilée.
- [x] Les valeurs dérivées / calculées (âge, années avant retraite, répartition REER/CELI, rente PD calculée, rentes RRQ/PSV, âges aux héritages) se mettent à jour en temps réel pendant la saisie.
- [x] Le script de validation `validate.ps1` passe avec 0 erreur.
