# Wiki → Neo4j Frontend (Flutter)

Modern single-page Flutter UI for submitting a wiki URL or raw text to the backend converter and visualizing the resulting graph (nodes and relationships). Designed with the "Ocean Professional" theme.

## Features
- Toggle between URL and raw text input
- Submits to backend: POST /api/convert with {url} or {content}
- Fetch result: GET /api/graph/{jobId} (with a short retry)
- Health check: GET /health
- Results displayed as a structured list with basic color-coding and a prettified JSON view
- Copy-to-clipboard for JSON
- Resilient error handling (404/500/backend not available)

## Configure environment
The app uses `flutter_dotenv` to read `.env` from the project root. If `.env` is missing, defaults are used.

1) Copy `.env.example` to `.env` and edit:
```
API_BASE_URL=http://localhost:3001
```
2) Save `.env` in the `wiki_to_neo4j_frontend` directory. Do not commit secrets to `.env`. This project does not require any external service keys.

If not provided, the base URL defaults to `http://localhost:3001`.

## Run the app
- Backend (required):
  - From backend folder `wiki_to_neo4j_backend`:
    - Start: `./gradlew bootRun`
    - Verify: open http://localhost:3001/health (should return OK)
- Mobile (Android/iOS emulators or devices):
  - Ensure Flutter SDK is installed.
  - From the `wiki_to_neo4j_frontend` directory:
    ```
    flutter pub get
    flutter run
    ```
- Web:
  - Ensure backend CORS is configured to allow the Flutter web origin (see below).
  - Then:
    ```
    flutter pub get
    flutter run -d chrome
    ```

## Backend expectations
- Base URL: `${API_BASE_URL}` or `http://localhost:3001` by default
- Endpoints:
  - `POST /api/convert` body: `{ "url": "https://..." }` OR `{ "content": "text..." }`
    - Response: `{ "jobId": "..." }`
  - `GET /api/graph/{jobId}` returns:
    ```
    {
      "nodes": [ { "id": "1", "label": "Node" } ],
      "relationships": [ { "source": "1", "target": "2", "type": "MENTIONS" } ]
    }
    ```
    or wrapped within `{ "status": "...", "result": { ... } }`
  - `GET /health` -> 200 OK when healthy

## CORS considerations (Web)
- The backend currently allows:
  - http://localhost:3000, http://127.0.0.1:3000
  - http://localhost:3001, http://127.0.0.1:3001
- If you serve Flutter web from a different port (e.g., 5173, 8080), add that origin to `CorsConfig.java` and restart the backend.

## Troubleshooting
- Backend unreachable or CORS error in browser devtools:
  - Confirm backend is running and `API_BASE_URL` points to it.
  - For web, ensure your origin is allowed in backend CORS.
- Graph not ready:
  - The app retries once after a short delay. If still unavailable, try again later using the same jobId.
- JSON shape differences:
  - The UI supports both direct GraphResult and nested `{ result: ... }` responses.

## Development notes
- Theme colors:
  - primary: #2563EB
  - secondary/success: #F59E0B
  - error: #EF4444
  - background: #f9fafb
  - surface: #ffffff
  - text: #111827
- State management: `ChangeNotifier` + `Provider`
- Env: `flutter_dotenv` (app compiles and runs even without `.env`)
