# ARANUI — Vision, Recap & Timeline

> Projet : ERP Hotel Flottant sur-mesure
> Client : ARANUI (croisiere cargo/passagers, Marquises)
> Equipe client : Rene (DSI), DAF
> Date R1 : 03 mars 2026

---

## 1. Vision

Construire un ERP sur-mesure **offline-first** qui remplace Resco et couvre l'ensemble de l'exploitation du navire ARANUI : experience passager, gestion equipage, maintenance, points de vente, stocks, bons de commande et comptabilite.

**Le bateau est un hotel flottant** — l'outil doit fonctionner sans internet, avec un serveur local a bord comme source de verite et une synchronisation cloud asynchrone.

### Principes directeurs

| Principe          | Implication                                                           |
| ----------------- | --------------------------------------------------------------------- |
| **Offline-first** | Serveur local a bord, sync cloud asynchrone, zero dependance internet |
| **Sur-mesure**    | Solution taillee pour les besoins ARANUI, pas un ERP generique        |
| **Modulaire**     | 6 lots independants, livrables et facturables separement              |
| **Progressif**    | Le client peut arreter a tout moment, chaque lot a de la valeur seul  |
| **Web-only**      | Web app responsive (PWA), pas d'app native                            |

---

## 2. Recap des 6 Lots

### LOT 1 — Intranet Passagers `P0`

Le coeur du MVP. Tout ce que le passager voit et utilise pendant sa croisiere.

| Module          | Description                                                                     |
| --------------- | ------------------------------------------------------------------------------- |
| Check-in        | Creation compte temporaire (cabine + code personnel)                            |
| Programme       | Consultation activites, excursions incluses/optionnelles                        |
| Reservations    | Inscription activites avec limite de places, validation reception, notification |
| Restauration    | Consultation menus, commande options (charge cabine)                            |
| Points de vente | Catalogue boutique/bar/restaurant, prix, dispo                                  |
| WiFi            | Captive portal, achat/recharge carte internet                                   |
| Chat            | Messagerie passager → reception                                                 |
| Suivi depenses  | Historique conso, montant total temps reel                                      |
| TV cabine       | Page statique (menu/activites du jour) — optionnel                              |

**Paiement** : debit automatique fin de sejour (Banque de Tahiti / HABITU) ou CB manuelle la veille.

### LOT 2 — Gestion Equipage `P1`

| Module                    | Description                                           |
| ------------------------- | ----------------------------------------------------- |
| Embarquement/Debarquement | Check-in/out a chaque ile, integration badges/bips    |
| Comptes equipage          | Cabines, cartes conso, roles                          |
| Securite maritime         | Muster stations, protocoles urgence, manuel numerique |

### LOT 3 — Maintenance / Ticketing `P1`

Remplace le carnet papier ET ClickUp.

| Module           | Description                                 |
| ---------------- | ------------------------------------------- |
| Creation tickets | Photo, description, localisation, categorie |
| Pipeline         | Nouveau → en cours → traite / a acheter     |
| Lien BDC         | Generation bon de commande depuis un ticket |

### LOT 4 — Bons de Commande `P2`

Workflow inspire du projet Hilton (ref interne DevLab).

| Etape             | Acteur             |
| ----------------- | ------------------ |
| Creation          | Manager du service |
| Validation 1      | DAF                |
| Validation 2      | DG                 |
| Envoi fournisseur | Manager            |
| Matching facture  | Comptabilite       |

Budget previsionnel par service + alertes depassement.

### LOT 5 — Points de Vente `P2`

| Module             | Description                                                 |
| ------------------ | ----------------------------------------------------------- |
| Ventes a bord      | Boutique, bar, restaurant — charge cabine ou badge equipage |
| Stock / Provisions | Suivi temps reel, integration IDT/CGI (export XML)          |

### LOT 6 — Comptabilite / Back-office `P3`

| Module             | Description                           |
| ------------------ | ------------------------------------- |
| Controle comptable | Ecritures de vente, encaissements     |
| Lien IDT/CGI       | Transfert bidirectionnel (export XML) |
| Encaissements      | Interface Banque de Tahiti / HABITU   |

---

## 3. Dependances entre lots

```
LOT 1 (Intranet Passagers)
  ↓
LOT 2 (Equipage) ←── integration badges
  ↓
LOT 3 (Maintenance)
  ↓
LOT 4 (Bons de Commande) ←── tickets → BDC
  ↓
LOT 5 (Points de Vente) ←── integration IDT/CGI
  ↓
LOT 6 (Comptabilite) ←── lots 4 + 5
```

---

## 4. Timeline estimee

### Phase 0 — Cadrage & Architecture `Mars 2026`

- [x] R1 decouverte (03/03/2026)
- [ ] R2 : validation PRD, budget, questions ouvertes
- [ ] Choix stack technique definitif
- [ ] Maquettes / wireframes Lot 1
- [ ] Specification protocoles : badges, HABITU, captive portal, IDT/CGI XML
- [ ] Setup infra : serveur local bateau + cloud + pipeline sync

### Phase 1 — LOT 1 : Intranet Passagers `Avril — Juillet 2026`

| Mois    | Objectif                                       | Jalon                           |
| ------- | ---------------------------------------------- | ------------------------------- |
| Avril   | Check-in, comptes passagers, programme sejour  | **Facturation 40%**             |
| Mai     | Reservations, restauration, catalogue PdV      | Demo interne                    |
| Juin    | WiFi/captive portal, chat, suivi depenses      | **Facturation 30%**             |
| Juillet | Tests terrain (a bord), TV cabine, corrections | **Livraison — Facturation 30%** |

### Phase 2 — LOT 2 + LOT 3 `Aout — Novembre 2026`

| Mois | Objectif                                                 | Jalon                     |
| ---- | -------------------------------------------------------- | ------------------------- |
| Aout | Equipage : embarquement/debarquement, integration badges | **Facturation 40% Lot 2** |
| Sept | Equipage : comptes, securite maritime                    | **Facturation 30% Lot 2** |
| Oct  | Maintenance : tickets, pipeline                          | **Facturation 40% Lot 3** |
| Nov  | Maintenance : lien BDC, tests terrain                    | **Livraison Lots 2+3**    |

### Phase 3 — LOT 4 + LOT 5 `Decembre 2026 — Mars 2027`

| Mois | Objectif                                | Jalon                     |
| ---- | --------------------------------------- | ------------------------- |
| Dec  | BDC : workflow validation DAF/DG        | **Facturation 40% Lot 4** |
| Jan  | BDC : budgets, audit, matching factures | **Livraison Lot 4**       |
| Fev  | PdV : ventes, facturation cabine/badge  | **Facturation 40% Lot 5** |
| Mars | PdV : stock, integration IDT/CGI        | **Livraison Lot 5**       |

### Phase 4 — LOT 6 `Avril — Juin 2027`

| Mois  | Objectif                                       | Jalon                     |
| ----- | ---------------------------------------------- | ------------------------- |
| Avril | Module comptable, ecritures                    | **Facturation 40% Lot 6** |
| Mai   | Lien IDT/CGI, encaissements HABITU             | **Facturation 30% Lot 6** |
| Juin  | Tests finaux, formation, mise en prod complete | **Livraison finale**      |

---

## 5. Jalons cles

```
Mars 2026          R2 cadrage + validation budget
Juillet 2026       LOT 1 en production (MVP passagers)
Novembre 2026      LOTs 2+3 en production (equipage + maintenance)
Mars 2027          LOTs 4+5 en production (BDC + PdV)
Juin 2027          LOT 6 en production (compta) — ERP complet
```

---

## 6. Risques & points d'attention

| Risque                                    | Impact                 | Mitigation                       |
| ----------------------------------------- | ---------------------- | -------------------------------- |
| Protocole badges inconnu                  | Bloque Lot 2           | Identifier le systeme au R2      |
| API IDT/CGI limitee (XML, pas temps reel) | Sync degradee Lots 5+6 | Prevoir buffer + reconciliation  |
| HABITU non documente                      | Bloque paiements       | Contacter Banque de Tahiti tot   |
| Captive portal WiFi bateau                | Bloque WiFi Lot 1      | Identifier routeur au R2         |
| Fraude inter-cabines                      | Securite comptes       | Mecanisme identification robuste |
| Budget non evoque                         | Risque commercial      | Aborder au R2                    |
| Complexite offline/sync                   | Architecture critique  | POC sync des Phase 0             |

---

## 7. Modele de facturation

Par lot, en 3 paliers :

```
 40%  ──→  Lancement du lot
 30%  ──→  Mi-developpement
 30%  ──→  Livraison validee
```

Chaque lot est independant — le client peut arreter a tout moment sans dette technique.

---

## 8. Prochaines etapes (R2)

1. Valider cette timeline et le budget avec le client
2. Repondre aux 12 questions ouvertes du PRD
3. Contacter Dominique (expert comptable) pour workflow compta
4. Obtenir specs techniques : badges, HABITU, WiFi bateau, IDT/CGI XML
5. Produire les maquettes du Lot 1
6. Lancer le POC architecture offline-first + sync
