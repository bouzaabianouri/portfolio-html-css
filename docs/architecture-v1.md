# Smart Bus System — Architecture v1

## 1) Vue d’ensemble
Smart Bus System est une plateforme de transit temps réel orientée:
- suivi live,
- diffusion d’événements,
- estimation ETA,
- pilotage de flotte.

Architecture logique:

```text
[Bus Simulator / GPS Device]
          ↓
      API Gateway / Ingestion API
          ↓
        Redis Pub/Sub
          ↓
  Real-time Engine (Socket.io)
          ↓
       MongoDB Atlas
          ↓
 Frontend (Next.js + Leaflet)
```

---

## 2) Composants

### 2.1 Frontend (Next.js)
Rôles:
- Carte live (bus + routes + arrêts)
- Dashboard utilisateur
- Dashboard admin (CRUD basique)
- Connexion Socket.io client

Tech:
- React + Next.js
- Tailwind CSS
- Leaflet / React-Leaflet
- Zustand ou Redux Toolkit

### 2.2 Backend API (Node.js)
Rôles:
- Endpoints REST (bus/routes/users)
- Endpoint ingestion GPS
- Validation (Zod)
- Auth JWT (roles admin/viewer)

### 2.3 Messaging (Redis)
Rôles:
- Découplage ingestion ↔ diffusion
- Pub/Sub pour événements de position

### 2.4 Real-time Engine (Socket.io)
Rôles:
- Broadcast des mises à jour aux clients
- Gestion de reconnexion/heartbeat
- Emission ciblée (par route ou global)

### 2.5 Storage (MongoDB Atlas)
Rôles:
- État courant du bus
- Historique de localisation
- Données routes/utilisateurs/logs

### 2.6 Simulator
Rôles:
- Génération de positions GPS réalistes
- Simulation multi-bus, vitesses et arrêts
- Injection de scénarios d’erreur

---

## 3) Flux de données (Data Flow)

### Flux nominal
1. Simulator envoie `{busId, lat, lng, speed, timestamp}` vers l’API ingestion.
2. Backend valide la payload (Zod).
3. Event publié sur Redis (channel: `bus.location.updated`).
4. Real-time engine consomme l’event puis:
   - diffuse via Socket.io aux clients abonnés,
   - persiste dans MongoDB (current state + history).
5. Frontend met à jour la carte et recalcule/affiche ETA.

### Flux incident (exemple)
- Bus inactif > X secondes:
  - statut passe `offline`,
  - alerte visible côté admin.

---

## 4) Non-fonctionnel (v1)
- Latence cible end-to-end: < 2 secondes.
- Disponibilité démo locale: > 95% pendant la session.
- Scalabilité initiale: 20–50 bus simulés.
- Sécurité: JWT + validation stricte + rate limiting basique.

---

## 5) Décisions d’architecture
1. Redis Pub/Sub pour découpler ingestion et temps réel.
2. MongoDB pour flexibilité du schéma et historique.
3. Socket.io pour simplifier la communication temps réel.
4. Simulation obligatoire pour valider le système sans hardware.

---

## 6) Limites v1
- ETA heuristique (pas ML).
- Pas de partitionnement avancé des données historiques.
- Observabilité basique (logs applicatifs principalement).

---

## 7) Evolution prévue (v2+)
- ETA avancé (ML/traffic-aware).
- Détection d’anomalies (bus bloqué, outlier speed).
- Analytics dashboard (efficacité des routes).
- Stratégie de scaling horizontal des services.
