# wiki-to-graph-converter-3236-3246

This workspace contains the Flutter frontend for the Wiki → Neo4j converter.

Containers:
- wiki_to_neo4j_frontend (Flutter): Single-page UI to submit a wiki URL or raw text and visualize the resulting graph from the backend.

Quick start
- Backend: see ../wiki-to-graph-converter-3236-3245/wiki_to_neo4j_backend/README.md and run `./gradlew bootRun` on port 3001.
- Frontend:
  - cd wiki_to_neo4j_frontend
  - cp .env.example .env  # optional; default base URL is http://localhost:3001
  - flutter pub get
  - flutter run            # or `flutter run -d chrome` for web

See `wiki_to_neo4j_frontend/README.md` for detailed setup, CORS, and troubleshooting. The frontend reads `API_BASE_URL` from `.env` (defaults to `http://localhost:3001`).