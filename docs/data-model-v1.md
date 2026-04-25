# Smart Bus System — Data Model v1 (MongoDB)

## 1) Principes
- Modèle orienté lecture rapide pour état courant des bus.
- Historisation append-only pour analyses/replay.
- Indexation sur identifiants métier et timestamp.

---

## 2) Collections

### 2.1 `buses`
Document exemple:

```json
{
  "busId": "bus-01",
  "routeId": "route-10",
  "currentLocation": { "lat": 36.8, "lng": 10.18 },
  "speed": 45,
  "status": "moving",
  "lastSeenAt": "2026-04-25T10:12:00.000Z",
  "updatedAt": "2026-04-25T10:12:00.000Z"
}
```

Champs:
- `busId` (string, unique, requis)
- `routeId` (string, requis)
- `currentLocation.lat` (number, requis)
- `currentLocation.lng` (number, requis)
- `speed` (number, >= 0)
- `status` (enum: `moving|stopped|offline`)
- `lastSeenAt` (Date)
- `updatedAt` (Date)

Indexes:
- `{ busId: 1 }` unique
- `{ routeId: 1 }`
- `{ status: 1 }`
- `{ lastSeenAt: -1 }`

---

### 2.2 `routes`
Document exemple:

```json
{
  "routeId": "route-10",
  "name": "Tunis Center Line",
  "stops": [
    { "name": "Stop 1", "lat": 36.80, "lng": 10.18, "order": 1 },
    { "name": "Stop 2", "lat": 36.81, "lng": 10.20, "order": 2 }
  ],
  "isActive": true,
  "createdAt": "2026-04-25T10:00:00.000Z",
  "updatedAt": "2026-04-25T10:10:00.000Z"
}
```

Champs:
- `routeId` (string, unique, requis)
- `name` (string, requis)
- `stops[]` (array, min 2)
  - `name`, `lat`, `lng`, `order`
- `isActive` (boolean)

Indexes:
- `{ routeId: 1 }` unique
- `{ isActive: 1 }`

---

### 2.3 `location_history`
Document exemple:

```json
{
  "busId": "bus-01",
  "routeId": "route-10",
  "timestamp": "2026-04-25T10:12:00.000Z",
  "lat": 36.80,
  "lng": 10.18,
  "speed": 44
}
```

Champs:
- `busId` (string, requis)
- `routeId` (string, optionnel recommandé)
- `timestamp` (Date, requis)
- `lat` (number, requis)
- `lng` (number, requis)
- `speed` (number, >= 0)

Indexes:
- `{ busId: 1, timestamp: -1 }`
- `{ timestamp: -1 }`

Option (plus tard):
- TTL index pour auto-expiration des données anciennes.

---

### 2.4 `users`
Document exemple:

```json
{
  "email": "admin@smartbus.local",
  "passwordHash": "<hashed>",
  "role": "admin",
  "isActive": true,
  "createdAt": "2026-04-25T10:00:00.000Z"
}
```

Champs:
- `email` (string, unique, requis)
- `passwordHash` (string, requis)
- `role` (enum: `admin|viewer`)
- `isActive` (boolean)
- `createdAt` (Date)

Indexes:
- `{ email: 1 }` unique
- `{ role: 1 }`

---

### 2.5 `logs` (optionnel v1, utile)
Document exemple:

```json
{
  "level": "info",
  "service": "ingestion-service",
  "message": "GPS event processed",
  "meta": { "busId": "bus-01" },
  "timestamp": "2026-04-25T10:12:00.000Z"
}
```

Indexes:
- `{ timestamp: -1 }`
- `{ service: 1, timestamp: -1 }`

---

## 3) Relations logiques
- `buses.routeId` → `routes.routeId`
- `location_history.busId` → `buses.busId`
- `location_history.routeId` → `routes.routeId` (si stocké)

---

## 4) Règles de validation (résumé)
- `lat` ∈ [-90, 90], `lng` ∈ [-180, 180]
- `speed` >= 0
- `stops` ordonnés, sans doublons d’ordre
- `busId`/`routeId` format cohérent (`bus-xx`, `route-xx`)

---

## 5) Notes performance
- Lire l’état courant depuis `buses` (rapide).
- Utiliser `location_history` pour timeline/replay seulement.
- Éviter d’écrire tout dans un seul document (anti-pattern).
