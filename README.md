# PHUC LONG clubhouse

Local, unpublished React frontend and read-only backend. Nothing has been uploaded to GitHub or written to Google Sheets. All bundled preview data is fictional. The source Sheet, Telegram IDs, scores and secrets are absent from the frontend bundle.

## Run

Node 24+. Run `npm ci`, `npm run dev`, then visit the printed local URL. Choose **Explore sample clubhouse** to review all screens without any external data connection. `npm test` checks authorization, data filtering and statistics; `npm run build` produces the static frontend in `dist/`.

## Authentication and private data

GitHub Pages serves public static assets. Private data is fetched only from a separately hosted HTTPS Node API. Google Identity Services signs members in; the browser sends its short-lived ID token in an Authorization header. The backend uses Google's verifier to check signature, issuer, expiration and client ID audience. It looks up the verified stable Google `sub` in the server-only MEMBER_MAP, then verifies that the corresponding Sheet UserID is active and unique before returning data. Display names and email addresses do not grant access. The ninth active player is not automatically admitted.

No token or private data is persisted in browser storage; sign-out drops the current in-memory dashboard. Tokens are used only for the initial authenticated read, so refresh requires another sign-in. API responses use no-store; CORS permits one exact origin. No third-party analytics. Hosting must use TLS and redact Authorization headers in logs; add ingress rate limiting before production. Google Fonts is a public visual dependency and may be replaced with locally hosted fonts.

The backend uses Application Default Credentials with the Sheets **readonly** scope. Prefer workload identity; otherwise keep the service-account credential exclusively in the backend host's secret store, never in this repository or any VITE variable. The chat's Drive connection does not supply deployed-app credentials. Read access must be granted separately after approval. Google OAuth client IDs are public identifiers, not secrets.

Copy `.env.example` to `.env` locally after choosing configuration. Set exact FRONTEND_ORIGIN, GOOGLE_CLIENT_ID and SPREADSHEET_ID on the server. MEMBER_MAP maps Google subject IDs to `{ "alias": "soopy", "playerId": "verified Sheet UserID" }`. It starts empty and denies all accounts. Confirm each mapping privately; don't commit member identity details. Set the matching public VITE_GOOGLE_CLIENT_ID and VITE_API_URL before building. Register the actual frontend origin in Google OAuth's authorized JavaScript origins. Run `npm run server` separately from the frontend.

## Observed Sheet differences

Read-only inspection confirmed seven tabs. Players has nine active entries; there is no verified one-to-one mapping from the eight supplied aliases to login identities. Alleys actually has 16 columns: Alley, Contact, then MonOpen/MonClose through SunOpen/SunClose. The adapter supports that observed layout and the supplied `opening hours` variant without changing either.

Only approved active members' ACTIVE integer scores from 0 through 300 enter statistics; identity joins use UserID. Empty, invalid, and non-ACTIVE scores are excluded. Sessions retain their recorded dates and missing venue/time fields. There is no minimum-game rule for averages. Series records remain deferred until completeness rules are agreed. Venue hours are displayed as recorded; overnight closing times retain the original clock time.

The adapter reads only Players, Schedule, Sessions, Scores and Alleys. It never reads Replies or Config, which may contain bot-specific material. Five read-only ranges cover the current grid bounds: Players 999, Schedule 1000, Sessions 993, Scores 988 and Alleys 999 rows. If row capacity changes or any range/schema is invalid, reads fail closed; expand/paginate after review rather than silently displaying partial history. No background sync and no writes. Scores are not snapshotted into source or builds.

## Score submission

The form validates a score and displays an in-memory draft review. It deliberately has no save action. The API rejects all write methods. Enabling actual submission requires approval and a separate design for trusted author identity, open-session enforcement, duplicates/idempotency, concurrency with Telegram, validation and audit history. Existing tabs and bot behavior remain untouched.

## Publishing proposal — approval required

A manual-only GitHub Pages workflow is included. It does not run on push. After permission, choose the target repository, backend host, OAuth project, confirmed eight account mappings and read-access method. Configure repository public variables and a protected github-pages environment with required reviewers, then trigger the workflow only after approval. Deploy **only dist/** to Pages; the Node backend must live on a separate HTTPS service. No repository has been created or modified.

Inspected GitHub access exposed one unrelated private repository; no bowling repository was found. Do not reuse it without explicit selection.

References: https://developers.google.com/identity/gsi/web/guides/verify-google-id-token and https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages
