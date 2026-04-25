# portfolio-html-css

## Smart Bus System planning kit

This repository now includes documentation and an MVP Docker baseline for the Smart Bus System graduation-project concept.

## Added docs
- `docs/MVP.md`: MVP scope, acceptance criteria, and risks.
- `docs/architecture-v1.md`: architecture components, data flow, and non-functional targets.
- `docs/data-model-v1.md`: MongoDB collections, indexes, and validation rules.

## Docker Compose baseline
A starter `docker-compose.yml` is included to reflect the intended architecture services:
- `frontend` (Next.js)
- `backend` (Node.js API + Socket layer)
- `simulator` (GPS emitter)
- `redis`
- `mongo`

> Note: This compose file is a planning baseline and expects app code in `frontend/`, `backend/`, and `simulator/`.
