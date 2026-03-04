# PRD — ARANUI : ERP Hotel Flottant

> Client : ARANUI (croisiere cargo/passagers, Marquises)
> Interlocuteurs R1 : Rene (DSI) + DAF
> Outil actuel a remplacer : **Resco** (logiciel americain)
> Autres outils en place : ClickUp (ticketing), IDT/CGI (gestion commerciale/stock)
> Clientele : majorite europeens, ~20% americains, ~20% autres
> Approche validee : **sur-mesure**, decoupage en **lots**
> Date R1 : 03 mars 2026

---

## Vision

Remplacer Resco par un ERP sur-mesure couvrant l'ensemble de l'exploitation du navire ARANUI : experience passager, gestion equipage, maintenance, points de vente, stocks, bons de commande et comptabilite. Le bateau fonctionne comme un hotel flottant.

---

## Contraintes techniques critiques

### Offline-first (NON NEGOCIABLE)

- Le bateau est en mer = pas de connexion internet fiable
- **Serveur local a bord** = source de verite
- Synchronisation asynchrone vers le cloud quand internet est disponible
- Redondance web : acces distant identique a la base locale quand le bateau est en ligne
- L'equipe de support n'est pas toujours a bord → acces distant obligatoire

### Plateformes

- **Web app responsive** (telephone + ordinateur)
- Pas d'app native
- TV de cabine : page web statique en consultation (activites du jour, menus, defilement) — optionnel, pas de telecommande interactive

### Securite des comptes passagers

- Comptes temporaires crees a l'embarquement
- Authentification : numero de cabine + code personnel cree par le passager
- Risque identifie : fraude inter-cabines (quelqu'un qui donne un faux numero de cabine pour charger sur le compte d'un autre)
- Prevoir un mecanisme robuste d'identification

### Integrations existantes

- **IDT/CGI** (gestion commerciale, stocks) : pas d'API ouverte, export XML possible mais pas temps reel. Acces API = 1200EUR/an via tiers
- **Banque de Tahiti** : systeme HABITU pour debits automatiques en fin de sejour
- **Systeme de badges** : bips existants pour embarquement/debarquement (a interfacer)

---

## Lots / Modules

### LOT 1 — Intranet Passagers (PRIORITE)

Le client a confirme que c'est le premier module a livrer.

#### 1.1 Check-in

- Le passager fait son check-in
- Creation de son compte temporaire (cabine + code personnel)
- Acces a l'intranet du bateau

#### 1.2 Programme du sejour

- Consultation du programme complet : activites, excursions optionnelles, excursions incluses
- Distinction entre excursions incluses et optionnelles

#### 1.3 Reservation d'activites / excursions

- Inscription aux activites et excursions
- **Limite de places** par activite (ex : bateau annexe = 20 places max)
- Fermeture auto quand la limite est atteinte
- La liste des inscrits est communiquee a la reception
- La reception valide/confirme
- **Notification au passager** : inscription validee, avec date/heure/lieu
- Facturation sur le compte cabine

#### 1.4 Restauration

- Consultation des menus (comme un QR code de restaurant)
- Commande de menus en option (charges sur compte cabine)
- Pas de room service
- Possibilite de commander depuis le telephone (panier + montant)
- Confirmation de commande

#### 1.5 Points de vente (consultation)

- Catalogue de la boutique, du bar, du restaurant
- Consultation depuis la cabine (telephone)
- Produits, prix, disponibilite

#### 1.6 Internet / WiFi

- Captive portal (comme un hotel : cabine + code pour se connecter)
- Achat de carte WiFi :
  - En boutique physique (premiere fois)
  - Ou directement via l'application (recharge)
- Recharge credit internet depuis l'app (charge sur compte cabine)
- Frein identifie : si pas d'internet, pas de paiement CB en direct → charger sur cabine

#### 1.7 Chat / Messagerie

- Le passager peut envoyer un message a la reception
- Centralise a la reception
- Exemple : "c'est l'anniversaire de ma femme, est-ce que vous avez un gateau ?"
- Pas de room service, mais communication possible

#### 1.8 Suivi des depenses

- Le passager voit tout ce qu'il a consomme/achete pendant le sejour
- Suivi en temps reel du montant total
- Paiement :
  - Automatique en fin de sejour (debit via Banque de Tahiti / HABITU)
  - Le client peut payer la veille manuellement avec sa CB
  - Sinon debit automatique

#### 1.9 Affichage TV cabine (optionnel)

- Page web statique affichee sur les TV de cabine
- Activites du jour, menu du jour
- Defilement automatique, consultation uniquement
- Pas d'interaction (pas de telecommande)

---

### LOT 2 — Gestion Equipage

#### 2.1 Embarquement / Debarquement

- Check-in / check-out equipage a chaque ile
- Badges/bips existants (a integrer)
- Suivi temps reel : qui est a bord, qui est descendu
- Date, heure, nom, prenom a chaque mouvement
- Le capitaine doit avoir la liste a jour en permanence
- Meme logique pour les passagers : a chaque ile, savoir qui est descendu et qui n'est pas remonte

#### 2.2 Comptes equipage

- Cabines equipage
- Cartes pour consommer aux points de vente (au lieu de taper un nom)
- Fonctions/roles associes

#### 2.3 Securite maritime

- Muster stations (points de rassemblement)
- Map des zones de rassemblement dans l'outil
- Protocoles de securite : feu, homme a la mer, etc.
- Manuel de securite numerique (PDF integre)

---

### LOT 3 — Maintenance / Ticketing

Remplace le carnet papier ET ClickUp.

#### 3.1 Creation de tickets

- Photo du probleme
- Description, localisation, date/heure automatiques
- Categorie : equipements hoteliers, exterieur, machines
- Un responsable maintenance gere le pipeline

#### 3.2 Pipeline de traitement

- Statuts : nouveau → en cours → traite / a acheter
- Pendant le voyage : traitement des tickets faisables
- Au retour : liste des tickets non traites (piece manquante, achat necessaire)

#### 3.3 Declenchement bon de commande

- Depuis un ticket, generer directement un bon de commande
- Le BDC contient : photo, description du probleme, date/heure
- Lien ticket → bon de commande (tracabilite)

#### 3.4 Perimetre exclus

- Mecanique navire = reglementee, deja geree par un programme dedie (ne pas toucher)

---

### LOT 4 — Bons de Commande

Reference : outil developpe pour le Hilton (Sebastien, DSI).

#### 4.1 Workflow de validation

1. Manager du service cree le bon de commande
2. DAF recoit → valide ou refuse
3. DG recoit → valide ou refuse
4. Retour au manager → envoi au fournisseur
5. Reception facture → matching BDC / facture

#### 4.2 Roles et services

- Chaque manager gere son service (ex : massage = huiles, serviettes)
- Budget previsionnel par service (ex : massage = 2M XPF/mois)
- Alerte si budget depasse

#### 4.3 Audit et tracabilite

- Historique complet : qui a cree, valide, refuse, quand
- Commentaires a chaque etape
- Vision des BDC en attente de facture / a decaisser

---

### LOT 5 — Points de Vente

#### 5.1 Ventes a bord

- Boutique, bar, restaurant
- Facturation sur compte cabine (passagers)
- Facturation via carte/badge (equipage)

#### 5.2 Stock / Provisions

- Chargement des provisions avant le depart
- Suivi des stocks en temps reel par point de vente
- Actuellement : stock gere par IDT/CGI (gestion commerciale)
- Les points de vente ne voient pas le stock aujourd'hui (probleme a resoudre)
- Integration bidirectionnelle avec IDT/CGI (via export XML, pas d'API directe)

---

### LOT 6 — Comptabilite / Back-office

#### 6.1 Module comptable

- Controle comptable (existe deja dans Resco, a reproduire)
- Generation des ecritures de vente
- Gestion des encaissements

#### 6.2 Lien gestion commerciale

- Transfert de donnees bidirectionnel avec IDT/CGI
- Export XML (pas de temps reel possible)
- A terme : envisager de rapatrier la gestion commerciale dans le nouvel outil

#### 6.3 Encaissements

- Lien avec Banque de Tahiti (HABITU)
- Debit automatique en fin de sejour
- Suivi des paiements manuels (CB la veille)

---

## Ce qui est hors scope

- Mecanique navire (deja reglemente et gere)
- App native (iOS/Android) — on reste en web app
- App TV interactive (juste page statique de consultation)
- Migration complete de IDT/CGI (on s'interface en export XML)

---

## Architecture cible (proposition)

```
[TV Cabine]     [Telephone Passager]     [PC Reception]     [Tablette Maintenance]
     |                   |                      |                      |
     +-------------------+----------------------+----------------------+
                                    |
                          [Frontend Web App]
                          (responsive, PWA)
                                    |
                              [API Backend]
                                    |
                    +---------------+---------------+
                    |                               |
          [BDD Locale - Bateau]            [BDD Cloud - Redondance]
            (source de verite)              (sync asynchrone)
                    |
           [Export XML ←→ IDT/CGI]
```

### Points d'attention architecture

- **Offline-first** : toute operation doit fonctionner sans internet
- **Sync bidirectionnelle** : resolution de conflits quand le bateau se reconnecte
- **Captive portal WiFi** : integration reseau du bateau pour l'authentification passagers
- **Badges** : interface avec le systeme de bips existant (protocole a identifier)
- **Paiement** : interface avec Banque de Tahiti / HABITU (protocole a identifier)

---

## Priorite de livraison

| Lot | Module                  | Priorite               | Dependances                         |
| --- | ----------------------- | ---------------------- | ----------------------------------- |
| 1   | Intranet Passagers      | P0 — Premier prototype | Infra serveur local, captive portal |
| 2   | Gestion Equipage        | P1                     | Integration badges                  |
| 3   | Maintenance / Ticketing | P1                     | -                                   |
| 4   | Bons de Commande        | P2                     | Lot 3 (tickets → BDC)               |
| 5   | Points de Vente         | P2                     | Integration IDT/CGI                 |
| 6   | Comptabilite            | P3                     | Lots 4 et 5                         |

---

## Questions ouvertes (a valider au R2)

1. **Budget** : Pas encore evoque par le client. Thomas a dit "top 10% des projets en complexite"
2. **Protocole badges** : Quel systeme de bips utilisent-ils ? Quel protocole ? API disponible ?
3. **Banque de Tahiti / HABITU** : Documentation technique de l'interface de paiement ?
4. **IDT/CGI** : Format exact de l'export XML ? Frequence possible de sync ?
5. **Captive portal** : Quel routeur/systeme WiFi sur le bateau ? Peut-on integrer un portail captif custom ?
6. **Resco** : Possibilite d'export de la data existante pour migration ?
7. **TV cabines** : Modele exact des TV ? Capacite a afficher une page web ?
8. **Nombre de cabines / passagers max ?**
9. **Nombre de points de vente a bord ?**
10. **Frequence des voyages ?** (pour planifier les releases)
11. **Expert comptable** : Dominique, independant — a contacter pour comprendre le workflow compta
12. **Rene DSI** : c'est le champion interne, bien le garder informe

---

## Modele de facturation (propose par Thomas au R1)

- Decoupage en lots
- Par lot : 40% au lancement, 30% a mi-dev, 30% a la livraison
- Chaque lot est independant → le client peut arreter a tout moment

---

## Ref interne DevLab

- Outil similaire : projet Hilton (bons de commande, workflow DAF/DG) — 1 mois de dev
- Stack : front decouple du back, API ouvertes, modules communicants
- Expert Odoo (Quentin, Bresil) si besoin de modules ERP standards
- Demo Kura (extraction IA) montree au R1 pour illustrer les capacites
