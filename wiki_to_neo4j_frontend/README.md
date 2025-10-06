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

If not provided, the base URL defaults to `http://localhost:3001`.

Note: Do not commit secrets to `.env`. This project does not require any external service keys.

## Run the app
- Mobile (Android/iOS emulators or devices):
  - Ensure Flutter SDK is installed.
  - From the `wiki_to_neo4j_frontend` directory:
    ```
    flutter pub get
    flutter run
    ```
- Web:
  - Ensure backend CORS is configured to allow the Flutter web origin.
  - Then:
    ```
    flutter pub get
    flutter run -d chrome
    ```

## Backend expectations
- Base URL: `${API_BASE_URL}` or `http://localhost:3001` by default
- Endpoints:
  - `POST /api/convert` body: `{ "url": "https://...", }` OR `{ "content": "text..." }`
    - Response: `{ "jobId": "..." }`
  - `GET /api/graph/{jobId}` returns:
    ```
    {
      "nodes": [ { "id": "1", "label": "Node", "type": "Category" } ],
      "relationships": [ { "source": "1", "target": "2", "type": "LINKS_TO" } ]
    }
    ```
    or wrapped within `{ "status": "...", "result": { ... } }`
  - `GET /health` -> 200 OK when healthy

## CORS considerations
When running the Flutter web app, ensure the backend allows CORS for the web origin. Typical Spring Boot setup involves enabling CORS on controller paths or globally.

## Troubleshooting
- If the backend is not reachable, the health indicator will show "Unreachable", and submissions will provide a readable error message.
- If `/api/graph/{jobId}` is not ready immediately, the app retries once after a brief delay.
- JSON not as expected? The UI tries both direct and nested `result` structures.

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
