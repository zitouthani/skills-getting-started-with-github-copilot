# Copilot instructions for this repository

## Project snapshot
This repository is a minimal FastAPI app for Mergington High School's extracurricular activities board. The server is defined in `src/app.py`, and the browser UI is served from `src/static/` with the root route redirecting to `/static/index.html`.

The app does not use a database or persistent storage. Activity state is kept in memory in the `activities` dictionary, so a server restart resets the data.

## Build, run, and test commands
- Install dependencies:
  - `pip install -r requirements.txt`
- Start the app:
  - `uvicorn src.app:app --reload`
  - The app is available at `http://localhost:8000/` and API docs at `http://localhost:8000/docs`
- Run the full test suite:
  - `pytest`
- Run a single test file:
  - `pytest tests/test_app.py -q`
- Run a single test by name or keyword:
  - `pytest tests/test_app.py -k signup -q`

There is no repo-defined lint configuration or formatter script in this project. Validation is primarily via `pytest` and straightforward FastAPI behavior checks.

## Architecture and data flow
- `src/app.py` is the single source of truth for the API. It exposes routes for:
  - `GET /activities`
  - `POST /activities/{activity_name}/signup?email=...`
  - `DELETE /activities/{activity_name}/signup?email=...`
- `src/static/app.js` is the UI client. It fetches `/activities` and calls the signup/unregister endpoints directly, then updates the DOM based on the JSON response.
- Activity names are the canonical identifiers used in route paths; keep the names consistent between backend and frontend to avoid broken links or mismatched data.
- `src/static/index.html` provides the page structure the JavaScript populates, so UI changes often require updating the client-side rendering logic as well.

## Key conventions in this codebase
- Keep the API behavior consistent with the existing FastAPI patterns in `src/app.py`: validate the requested activity, return `HTTPException` with appropriate status codes, and keep response payloads simple and predictable.
- Treat `activities` as the application state for the current process. When you add features, prefer editing the in-memory model and related routes over introducing a new storage layer.
- Preserve the frontend contract: the browser expects the `/activities` response to be a dictionary keyed by activity name, with each value containing `description`, `schedule`, `max_participants`, and `participants`.
- When adding or changing an API route, check the corresponding client code in `src/static/app.js` to keep request URLs, query parameters, and response handling aligned.
- For tests, add them under `tests/` and assert route behavior for success, duplicate signup, missing activity, and unregister flows.

## Files worth checking first
- `src/app.py` for API behavior and in-memory data model
- `src/static/app.js` for client-side request/response expectations
- `src/static/index.html` for the page shell and elements the JS populates
- `requirements.txt` for the project dependencies

## Notes for Copilot sessions
- Keep changes minimal and aligned with the existing single-file app structure.
- Do not introduce a database, auth layer, or framework changes unless the task explicitly requires them.
- Prefer small, explicit API updates that preserve the current in-memory app design and static HTML/JS frontend.
