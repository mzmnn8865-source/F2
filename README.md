# GameClub HTML frontend

Framework-free HTML/CSS export of the Astro website. Pages use plain `.html` files and the original site styles. Vanilla JavaScript handles theme selection, login/registration, and loading article/group data from the API.

## Pages

Open `index.html` through a local static web server. Pages include the home page, editorial and group indexes, article/group views, login, account/community, management entry, and policy pages.

```sh
python3 -m http.server 8080
```

## Connect the developer's backend

Edit `js/backend-config.js`. The UI calls the endpoints, token format, payload builders, and response normalizers defined there. Change URL paths and normalizer functions to match the developer's backend; the page markup and styles do not need API-specific edits. Configuration loads before `js/site.js` on every page.

Set `apiBase` to the backend origin when the API is hosted separately:

```html
apiBase: "https://api.example.com",
```

For a separate origin, the backend must allow the exact website origin through CORS and permit the `Authorization` and `Content-Type` headers. The default client expects bearer token authentication and stores the token in browser `localStorage`. Update `authScheme` or the auth handlers if the backend uses cookies or another session method. For a same-origin deployment, leave `apiBase` empty and configure the web server to proxy `/api/*` and `/media/*` to the backend.

The default API contract is:

| UI feature | Default request | Expected response fields |
| --- | --- | --- |
| Login | `POST /api/auth/login` with `{username,password}` | `{access_token}` (or adjust `accessToken`) |
| Registration | `POST /api/auth/register` with username, password, display name, email | token response as above |
| Header/account | `GET /api/auth/me`, `GET /api/profile` | username, display name, bio, admin flag |
| Editorial list | `GET /api/articles?page=1&page_size=20` | array or `{items:[...]}`; each article has slug/id, title, excerpt/summary, body/content, category, author, image |
| Article detail | `GET /api/articles/{slug}` | one article object |
| Group list | `GET /api/groups` | array or `{items:[...]}`; groups have id, slug, name, description, member count, post count |
| Group detail | `GET /api/groups/{slug}` | one group object |
| Group posts | `GET /api/groups/{id}/posts` | array or `{items:[...]}`; posts have title, body/content, author/user |

To adapt response formats, edit `articleList`, `groupList`, `article`, `group`, `user`, and `post` in `js/backend-config.js`. For write or moderation features, add the matching API methods and UI handlers; this HTML export currently includes presentation and basic reads/auth only. The original Astro admin and authoring interfaces are available in the sibling Astro export as implementation references.

Article and group detail pages use query strings: `article.html?slug=...` and `group.html?slug=...`.

The static files can be deployed on any static web host. Article and group detail pages use query strings: `article.html?slug=...` and `group.html?slug=...`.
