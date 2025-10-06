# wiki-to-graph-converter-3236-3246

This workspace contains the Flutter frontend for the Wiki → Neo4j converter.

Containers:
- wiki_to_neo4j_frontend (Flutter): Single-page UI to submit a wiki URL or raw text and visualize the resulting graph from the backend.

See `wiki_to_neo4j_frontend/README.md` for setup and run instructions. The frontend reads `API_BASE_URL` from `.env` (defaults to `http://localhost:3001`).