# Smart Bus System — MVP Definition (v1)

## 1) Objectif
Construire une plateforme de transport en temps réel qui permet:
- le suivi live des bus,
- l’affichage d’un ETA simple,
- l’administration de base de la flotte,
- la simulation GPS sans matériel réel.

---

## 2) In Scope (MVP)

### 2.1 Côté utilisateur
- Carte temps réel (Leaflet) avec positions des bus.
- Affichage des routes (polylines + arrêts).
- Fiche bus: vitesse, statut (moving/stopped), dernière mise à jour.
- ETA v1 (formule simple: distance / vitesse moyenne).

### 2.2 Côté admin
- CRUD bus (ajouter/modifier/supprimer).
- CRUD routes (ajouter/modifier/supprimer).
- Association bus → route.
- Visualisation de l’état de la flotte (online/offline basique).

### 2.3 Système
- Ingestion GPS via API.
- Diffusion live via WebSocket/Socket.io.
- Stockage MongoDB (état courant + historique).
- Simulation GPS multi-bus configurable.

---

## 3) Out of Scope (phase ultérieure)
- Prédiction ETA basée IA/ML.
- Détection d’anomalies avancée.
- Dashboard analytique complet.
- Mode offline mobile.
- Optimisation multi-région / haute dispo avancée.

---

## 4) Critères d’acceptation (DoD)
Le MVP est validé si:
1. Le simulateur envoie des positions toutes les 2–5 secondes.
2. Les positions sont visibles en temps réel sur le dashboard user.
3. Les données sont persistées dans MongoDB.
4. L’ETA v1 s’affiche pour au moins un arrêt cible.
5. L’admin peut gérer bus et routes.
6. Le système supporte au moins 20 bus simulés sans blocage majeur.

---

## 5) Risques MVP
- Jitter GPS (points instables) → besoin de smoothing.
- Déconnexions socket → stratégie de reconnexion.
- Mauvaise qualité des données simulées → scénarios de test réalistes requis.

---

## 6) Livrables MVP
- Code source (frontend + backend + simulator).
- Docker Compose local.
- Documentation d’exécution.
- Démo live (scriptée) avec scénarios normaux + incident simple.
