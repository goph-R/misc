\# DPress



A WordPress like blog engine but in default the content editor is a syntax colored Markdown editor

(no WYSIWYG, or at least not in default).



Using `dynart-micro` framework and `dynart-micro-entities` ORM. Only MariaDB supported.



The public frontend (not the admin) is server side rendered (.phtml)



The Markdown should have special shortcodes like in WordPress for PHP code.



\## Database structure



The following are the tables:



```

\#User

&nbsp; uuid (sub)

&nbsp; username

&nbsp; display\_name

&nbsp; email

&nbsp; password (hashed, salted)

&nbsp; bio

&nbsp; avatar\_uuid

&nbsp; status (active | banned | pending\_verification)

&nbsp; created\_at

&nbsp; updated\_at

&nbsp; email\_verified\_at (nullable)

&nbsp; password\_reset\_token (nullable)

&nbsp; password\_reset\_expires\_at (nullable)



\#Role

&nbsp; uuid

&nbsp; name



\#User\_Role

&nbsp; user\_sub

&nbsp; role\_uuid



\#Permission

&nbsp; uuid

&nbsp; name



\#Role\_Permission

&nbsp; role\_uuid

&nbsp; permission\_uuid



\#Setting

&nbsp; name

&nbsp; value

&nbsp; group (general | reading | writing | discussion | media | permalink | seo | mail | cache)

&nbsp; autoload (boolean, loaded on every request vs on-demand)



\#Page

&nbsp; uuid

&nbsp; user\_uuid

&nbsp; parent\_uuid (nullable, for hierarchical pages)

&nbsp; title

&nbsp; slug

&nbsp; content

&nbsp; excerpt

&nbsp; status (draft | published | scheduled | trashed)

&nbsp; published\_at (nullable)

&nbsp; featured\_image\_uuid (nullable)

&nbsp; template (nullable, custom template name e.g. "full-width")

&nbsp; menu\_order

&nbsp; meta\_title (nullable, SEO override)

&nbsp; meta\_description (nullable)

&nbsp; canonical\_url (nullable)

&nbsp; og\_image\_uuid (nullable)

&nbsp; created\_at

&nbsp; updated\_at



\#Page\_Archive

&nbsp; uuid

&nbsp; version

&nbsp; original\_uuid

&nbsp; user\_uuid

&nbsp; title

&nbsp; slug

&nbsp; content

&nbsp; excerpt

&nbsp; change\_note (nullable, optional editor note for the version)

&nbsp; created\_at



\#Post

&nbsp; uuid

&nbsp; user\_uuid

&nbsp; title

&nbsp; slug

&nbsp; content

&nbsp; excerpt (nullable, manual; if absent auto-generated from content)

&nbsp; status (draft | published | scheduled | trashed)

&nbsp; published\_at (nullable)

&nbsp; featured\_image\_uuid (nullable)

&nbsp; comment\_status (open | closed)

&nbsp; meta\_title (nullable, SEO override)

&nbsp; meta\_description (nullable)

&nbsp; canonical\_url (nullable)

&nbsp; og\_image\_uuid (nullable)

&nbsp; views (integer hit counter)

&nbsp; created\_at

&nbsp; updated\_at



\#Post\_Archive

&nbsp; uuid

&nbsp; version

&nbsp; original\_uuid

&nbsp; user\_uuid

&nbsp; title

&nbsp; slug

&nbsp; content

&nbsp; excerpt

&nbsp; change\_note (nullable)

&nbsp; created\_at



\#Tag

&nbsp; uuid

&nbsp; name

&nbsp; slug

&nbsp; description



\#Category

&nbsp; uuid

&nbsp; parent\_uuid (nullable, for nested categories)

&nbsp; name

&nbsp; slug

&nbsp; description

&nbsp; featured\_image\_uuid (nullable)

&nbsp; meta\_title (nullable)

&nbsp; meta\_description (nullable)

&nbsp; order



\#Post\_Tag

&nbsp; post\_uuid

&nbsp; tag\_uuid



\#Post\_Category

&nbsp; post\_uuid

&nbsp; category\_uuid



\#Comment

&nbsp; uuid

&nbsp; post\_uuid

&nbsp; parent\_uuid (nullable, for threaded/nested comments)

&nbsp; user\_uuid (nullable, logged-in commenter)

&nbsp; author\_name (for anonymous commenters)

&nbsp; author\_email

&nbsp; author\_url

&nbsp; author\_ip

&nbsp; content

&nbsp; status (pending | approved | spam | trashed)

&nbsp; created\_at

&nbsp; updated\_at



\#Menu

&nbsp; uuid

&nbsp; name

&nbsp; location (theme-registered slot: primary | footer | sidebar)

&nbsp; created\_at

&nbsp; updated\_at



\#Menu\_Item

&nbsp; uuid

&nbsp; menu\_uuid

&nbsp; parent\_uuid (nullable, for nested items)

&nbsp; order

&nbsp; label

&nbsp; type (url | post | page | category | tag)

&nbsp; target\_uuid (nullable, FK to the target entity)

&nbsp; url (nullable, for type=url)

&nbsp; target (\_self | \_blank)

&nbsp; css\_class



\#Redirect

&nbsp; uuid

&nbsp; from\_path

&nbsp; to\_path

&nbsp; code (301 | 302)

&nbsp; created\_at



\#Media

&nbsp; uuid

&nbsp; parent\_uuid (only folders can be parents)

&nbsp; user\_uuid (uploader)

&nbsp; filename

&nbsp; type (can be `folder`, otherwise the mime like `image/jpeg`)

&nbsp; alt\_text

&nbsp; title

&nbsp; caption

&nbsp; size (file size in bytes)

&nbsp; metadata (json, depends on the type, for images width/height for example)

&nbsp; storage\_path (actual path on disk, decoupled from UUID convention)

&nbsp; status (active | trashed)

&nbsp; created\_at

&nbsp; updated\_at



\#Plugin

&nbsp; uuid

&nbsp; namespace (matches directory name)

&nbsp; status (active | inactive)

&nbsp; version

&nbsp; installed\_at

&nbsp; updated\_at



\#Email\_Log

&nbsp; uuid

&nbsp; to\_address

&nbsp; subject

&nbsp; body

&nbsp; status (queued | sent | failed)

&nbsp; created\_at

&nbsp; sent\_at (nullable)



\#Audit\_Log

&nbsp; uuid

&nbsp; user\_uuid

&nbsp; action (e.g. post.published, user.login, setting.changed)

&nbsp; entity\_type

&nbsp; entity\_uuid

&nbsp; payload (json)

&nbsp; ip\_address

&nbsp; created\_at



\#Db\_Migration

&nbsp; uuid

&nbsp; namespace (default is `default`, for plugins later)

&nbsp; filename

&nbsp; hash (md5 hash for checking)

```



The migrations are in the `db-migrations/` folder, no rollback. The entity names used as #Entity (basically a table)

in the SQL files.



\## Project structure

```

src/

&nbsp; Admin/

&nbsp;   Controllers/

&nbsp;     DashboardController.php

&nbsp;     PostController.php

&nbsp;     PageController.php

&nbsp;     CategoryController.php

&nbsp;     TagController.php

&nbsp;     CommentController.php

&nbsp;     MediaController.php

&nbsp;     MenuController.php

&nbsp;     UserController.php

&nbsp;     RoleController.php

&nbsp;     SettingController.php

&nbsp;     PluginController.php

&nbsp;     ThemeController.php

&nbsp;     RedirectController.php

&nbsp;   Forms/

&nbsp;     PostForm.php

&nbsp;     PageForm.php

&nbsp;     CategoryForm.php

&nbsp;     TagForm.php

&nbsp;     CommentForm.php

&nbsp;     UserForm.php

&nbsp;     SettingForm.php

&nbsp;   Lists/

&nbsp;     PostAdminList.php

&nbsp;     PageAdminList.php

&nbsp;     CategoryAdminList.php

&nbsp;     TagAdminList.php

&nbsp;     CommentAdminList.php

&nbsp;     MediaAdminList.php

&nbsp;     UserAdminList.php

&nbsp; Controllers/

&nbsp;   HomeController.php

&nbsp;   PostController.php      (public post/archive/tag/category pages)

&nbsp;   PageController.php      (public static pages)

&nbsp;   SearchController.php

&nbsp;   CommentController.php   (public comment submission)

&nbsp;   FeedController.php      (RSS/Atom)

&nbsp;   SitemapController.php

&nbsp;   AuthController.php      (admin login/logout, issues JWT)

&nbsp; Entities/

&nbsp;   User.php

&nbsp;   Role.php

&nbsp;   Permission.php

&nbsp;   Post.php

&nbsp;   Page.php

&nbsp;   PostArchive.php

&nbsp;   PageArchive.php

&nbsp;   Tag.php

&nbsp;   Category.php

&nbsp;   Comment.php

&nbsp;   Menu.php

&nbsp;   MenuItem.php

&nbsp;   Media.php

&nbsp;   Setting.php

&nbsp;   Redirect.php

&nbsp;   Plugin.php

&nbsp;   EmailLog.php

&nbsp;   AuditLog.php

&nbsp;   DbMigration.php

&nbsp; Repositories/

&nbsp;   UserRepository.php

&nbsp;   PostRepository.php

&nbsp;   PageRepository.php

&nbsp;   TagRepository.php

&nbsp;   CategoryRepository.php

&nbsp;   CommentRepository.php

&nbsp;   MenuRepository.php

&nbsp;   MediaRepository.php

&nbsp;   SettingRepository.php

&nbsp;   RedirectRepository.php

&nbsp;   PluginRepository.php

&nbsp; Services/

&nbsp;   PostService.php         (create/update/publish/trash, fires events)

&nbsp;   PageService.php

&nbsp;   CommentService.php      (moderation, spam check hook)

&nbsp;   MediaService.php        (upload, resize, thumbnail generation)

&nbsp;   MarkdownService.php     (parse + shortcode processing)

&nbsp;   ShortcodeService.php    (registry + dispatch)

&nbsp;   SlugService.php         (generation + uniqueness check)

&nbsp;   SearchService.php       (fulltext or LIKE-based)

&nbsp;   SettingService.php      (cached get/set)

&nbsp;   EmailService.php        (send + log)

&nbsp;   MigrationService.php    (run pending migrations for core + plugins)

&nbsp;   CacheService.php

&nbsp;   ThemeService.php

&nbsp;   PluginService.php       (discover, activate, deactivate, install from GitHub)

&nbsp;   RssService.php

&nbsp;   SitemapService.php

&nbsp;   SeoService.php          (meta/OG/JSON-LD generation)

&nbsp;   AuditService.php

&nbsp;   PermissionService.php   (check(string $permission): bool)

&nbsp; Middleware/

&nbsp;   AdminAuthMiddleware.php      (JWT guard for admin API routes)

&nbsp;   MaintenanceModeMiddleware.php

&nbsp;   RedirectMiddleware.php       (process #Redirect table on each request)

&nbsp;   CacheMiddleware.php          (serve cached pages for anonymous public requests)

&nbsp; Plugin/

&nbsp;   AbstractPlugin.php      (lifecycle: install, activate, deactivate, uninstall)

&nbsp;   PluginRegistry.php      (discover and load active plugins)

&nbsp; Cli/

&nbsp;   Commands/

&nbsp;     MigrateCommand.php

&nbsp;     MigrateStatusCommand.php

&nbsp;     CacheClearCommand.php

&nbsp;     UserCreateCommand.php

&nbsp;     PluginInstallCommand.php

&nbsp;     PluginActivateCommand.php

&nbsp;     PluginDeactivateCommand.php

&nbsp;     PluginListCommand.php

&nbsp;     ThemeActivateCommand.php

&nbsp;     MaintenanceOnCommand.php

&nbsp;     MaintenanceOffCommand.php

&nbsp;     SearchIndexCommand.php

&nbsp;     CronCommand.php        (scheduled tasks runner)

&nbsp;     ExportCommand.php

&nbsp;     ImportCommand.php

&nbsp;     SeedCommand.php        (dev only, demo content)

db-migrations/

&nbsp; 2026-01-01-1.sql

&nbsp; 2026-01-01-2.sql

plugins/

&nbsp; CustomPluginFrontend/

&nbsp;   views/

&nbsp;     custom-plugin-frontend-view.phtml

&nbsp;   CustomPluginFrontend.php (extends AbstractPlugin.php)

&nbsp;   CustomPluginFrontendController.php

&nbsp;   plugin.json (name, author, version, requires)

&nbsp; CustomPluginAdmin/

&nbsp;   db-migrations/

&nbsp;     2026-01-01-1.sql

&nbsp;   CustomPluginAdmin.php (extends AbstractPlugin.php)

&nbsp;   plugin.json (name, author, version, requires)

wwwroot/

&nbsp; admin/

&nbsp;   (React + shadcn SPA build output)

&nbsp; content/

&nbsp;   cache/                  (file-based page cache)

&nbsp;   media/

&nbsp;     uploaded files with UUID folders/filename like: f47ac10b/58cc/4372-a567-0e02b2c3d479.jpg

&nbsp;     split at hyphen positions of the UUID, original name/mime stored in db with virtual folders

&nbsp;   views/

&nbsp;     layout.phtml

&nbsp;     home.phtml

&nbsp;     single.phtml          (single post)

&nbsp;     page.phtml            (static page)

&nbsp;     archive.phtml         (post archive)

&nbsp;     category.phtml

&nbsp;     tag.phtml

&nbsp;     author.phtml

&nbsp;     search.phtml

&nbsp;     404.phtml

&nbsp;     pager.phtml

&nbsp;     form-errors.phtml

&nbsp;     form-field.phtml

&nbsp;     form-input.phtml

&nbsp;     emails/

&nbsp;       comment-notification.phtml

&nbsp;       comment-approved.phtml

&nbsp;       password-reset.phtml

&nbsp;       new-user.phtml

&nbsp;   themes/

&nbsp;     default/

&nbsp;       style.css

&nbsp;       functions.php       (registers menus, image sizes, shortcodes, etc.)

&nbsp;       theme.json          (name, author, version, description, requires\_dpress, screenshot, supported\_features)

&nbsp; index.php

&nbsp; index.dev.php (only for development)

&nbsp; robots.txt

&nbsp; .htaccess               (mod\_rewrite rules)

cli.php                   (CLI entry point)

composer.json

config.ini

config.dev.ini (only for development)

```



\## Plugins



Plugin system with downloading from `github` (only zips) and a `https://dpress.dynart.net/api/v1/plugins` endpoint for

getting the list and metadata of the current plugins.



A `plugin.json` would contain the current information about the plugin like `name`, `author`, `version`,

`description`, and optional `requires` (plugin dependencies). A `db-migrations/` folder would have the DB migrations

like the core but with the plugin namespace.



`AbstractPlugin` defines a lifecycle contract: `install()`, `activate()`, `deactivate()`, `uninstall()`.



`PluginRegistry` discovers plugins from the `plugins/` directory, reads `plugin.json`, checks the `#Plugin` table for

active status, and loads active plugins before routes are registered. Plugins hook into the application via

`EventService` events — they subscribe in their `activate()` method.



Plugin-provided routes, admin menu items, and shortcodes are registered by calling the relevant services from

`activate()`. Plugin update availability is checked by comparing the installed version in `#Plugin` against the

`https://dpress.dynart.net/api/v1/plugins` endpoint. Plugins run with full application privileges (no PHP sandboxing in v1).



\## Lists / Forms



The admin app would have named lists and forms like `CategoryList`, `CategoryFilterForm` (for the list)

and `CategoryForm` (for editing the category), so the plugins could extend these.



There would be factories to create these.



`FormFactory::create(string $name)`



The plugins could listen for a `CategoryFilterForm:created` event (for every named form we would emit signals)



`ListFactory::prepareQuery(string $name)`



The `CategoryList` would emit a `CategoryList:queryPrepared` signal so the plugins could listen to this, then

could add new fields/conditions for the query.



We would have different lists for the different use cases, for example: `CategoryAdminList`, `CategoryList`, one is for

the admin, one is for the frontend.



\## Admin API



The React + shadcn admin is a SPA. PHP serves `/admin/index.html` as a catch-all for all `/admin/\*` routes; all data

goes through a REST API at `/admin/api/v1/`. Authentication uses JWT — the login endpoint returns a signed token, the

SPA stores it and sends `Authorization: Bearer <token>` on every request. `dynart-micro`'s existing `JwtAuth` and

`JwtValidator` infrastructure is reused directly.



Consistent response envelopes:

\- Success: `{"data": {...}, "meta": {"total": N, "page": N}}`

\- Error: `{"error": {"code": 422, "message": "...", "fields": {...}}}`



Key endpoints (draft, not exhaustive):



```

POST   /admin/api/v1/auth/login

DELETE /admin/api/v1/auth/logout

GET    /admin/api/v1/dashboard           (stats: counts, recent activity)



GET/POST           /admin/api/v1/posts

GET/PUT/DELETE     /admin/api/v1/posts/{uuid}



GET/POST           /admin/api/v1/pages

GET/PUT/DELETE     /admin/api/v1/pages/{uuid}



GET/POST           /admin/api/v1/categories

GET/PUT/DELETE     /admin/api/v1/categories/{uuid}



GET/POST           /admin/api/v1/tags

GET/PUT/DELETE     /admin/api/v1/tags/{uuid}



GET/POST           /admin/api/v1/comments

GET/PUT/DELETE     /admin/api/v1/comments/{uuid}



GET/POST           /admin/api/v1/media    (POST = multipart upload)

GET/DELETE         /admin/api/v1/media/{uuid}



GET/POST           /admin/api/v1/menus

GET/PUT/DELETE     /admin/api/v1/menus/{uuid}



GET/PUT            /admin/api/v1/settings



GET/POST           /admin/api/v1/plugins

POST               /admin/api/v1/plugins/{namespace}/activate

POST               /admin/api/v1/plugins/{namespace}/deactivate



GET/POST           /admin/api/v1/users

GET/PUT/DELETE     /admin/api/v1/users/{uuid}



GET/POST           /admin/api/v1/redirects

GET/PUT/DELETE     /admin/api/v1/redirects/{uuid}

```



\## Authentication (Admin)



The admin uses JWT. Flow:



\- `AuthController` handles `POST /admin/api/v1/auth/login` — validates email + password, returns a signed JWT

\- `AuthController` handles `POST /admin/api/v1/auth/logout` — client discards token (stateless; optionally a token

&nbsp; denylist in `#Setting` or Redis for immediate revocation)

\- The SPA stores the token (memory or `localStorage`) and attaches it as `Authorization: Bearer <token>`

\- `AdminAuthMiddleware` (wrapping `dynart-micro`'s `JwtValidator`) validates the token on every admin API request

\- Rate limiting: lockout after N failed login attempts (configurable in `#Setting`)

\- Forgot password: generates `password\_reset\_token`, stores expiry, emails a reset link

\- Email verification: optional, sends a verification link to `email\_verified\_at`



\## User Roles \& Permissions (RBAC)



Built-in roles: `super\_admin`, `admin`, `editor`, `author`, `contributor`, `subscriber`.



Built-in permissions (examples):

\- `posts.create`, `posts.edit\_own`, `posts.edit\_any`, `posts.delete\_any`, `posts.publish`

\- `pages.create`, `pages.edit\_any`, `pages.publish`

\- `comments.moderate`, `comments.delete`

\- `media.upload`, `media.delete\_any`

\- `users.manage`, `settings.manage`, `plugins.manage`, `themes.manage`



`PermissionService::check(string $permission): bool` — checks the currently authenticated user's permissions.

Roles are seeded by migrations. Custom roles can be created via the admin UI.



\## Shortcode System



`ShortcodeService` maintains a registry: `name => callable`. Callable signature: `fn(array $attrs, string $content): string`.



Syntax: `\[shortcode attr="value"]inner content\[/shortcode]` and self-closing `\[shortcode /]`.



Built-in shortcodes:

\- `\[code lang="php"]` — syntax-highlighted code block (server-side via `scrivo/highlight.php`, emits `<pre>` with class)

\- `\[gallery ids="uuid1,uuid2"]` — renders a gallery grid from media UUIDs

\- `\[caption]` — figure + figcaption wrapper

\- `\[embed url="..."]` — oEmbed-style (YouTube, Vimeo, etc.)



`MarkdownService` runs the shortcode pass \*\*before\*\* Markdown parsing (avoids conflicts with fenced code blocks),

then passes the result through `league/commonmark`.



Plugins register shortcodes via `ShortcodeService::register(string $name, callable $handler)`.

Events: `shortcode:before\_render`, `shortcode:after\_render`.



\## Markdown Editor



Markdown parser: `league/commonmark` (extensible, PSR-compliant). Added to `composer.json`.



Admin editor (React): CodeMirror with Markdown mode, split-pane live preview. Supports:

\- Syntax highlighting of code blocks (client-side, Prism or similar)

\- Image drag-and-drop: uploads to `POST /admin/api/v1/media`, inserts `!\[alt](url)` or `\[gallery]`

\- Shortcode autocompletion (v2 feature)



Frontend rendering: `MarkdownService::render(string $markdown): string` — shortcode pass → CommonMark → sanitize HTML output.



\## Theme System



`ThemeService` resolves the active theme from `#Setting` (`theme.active`). Themes live in `wwwroot/content/themes/`.



`theme.json` fields: `name`, `author`, `version`, `description`, `requires\_dpress`, `screenshot`, `supported\_features`

(e.g., `\["post-thumbnails", "custom-header", "menus", "custom-logo"]`).



`functions.php` in the theme root is auto-loaded when the theme activates. It registers nav menu locations, image sizes,

shortcodes, and widget areas.



Template hierarchy (fallback chain, WordPress-style):

\- Single post: `single-{slug}.phtml` → `single.phtml` → `index.phtml`

\- Static page: `page-{slug}.phtml` → `page-{template}.phtml` → `page.phtml` → `index.phtml`

\- Category archive: `category-{slug}.phtml` → `category.phtml` → `archive.phtml` → `index.phtml`

\- Tag archive: `tag-{slug}.phtml` → `tag.phtml` → `archive.phtml` → `index.phtml`

\- Author archive: `author-{username}.phtml` → `author.phtml` → `archive.phtml` → `index.phtml`

\- Search results: `search.phtml` → `index.phtml`

\- 404: `404.phtml`



Child theme support: child theme path checked first, falls back to parent theme.



\## Permalinks / Slug



Global permalink structure configured in `#Setting` (`permalink.structure`). Options:

\- Plain: `/?p={uuid}`

\- Post name: `/{slug}` (default)

\- Date-based: `/{year}/{month}/{day}/{slug}`

\- Custom: `/{category}/{slug}`



`SlugService::generate(string $title): string` — transliterates, lowercases, replaces spaces with `-`, ensures uniqueness

by appending `-2`, `-3`, etc. `RedirectMiddleware` processes the `#Redirect` table on each request to handle old slugs

after a rename.



\## SEO



`SeoService` generates all SEO output for templates (meta tags, Open Graph, Twitter Cards, JSON-LD):



\- `<title>`: `meta\_title ?? title . ' | ' . site\_name`

\- `<meta name="description">`: `meta\_description ?? excerpt`

\- Open Graph: `og:title`, `og:description`, `og:image`, `og:type`, `og:url`

\- Twitter Card: `twitter:card`, `twitter:title`, `twitter:description`, `twitter:image`

\- JSON-LD: `Article` for posts, `WebSite` for homepage, `BreadcrumbList`

\- `<link rel="canonical">`: `canonical\_url ?? generated\_url`

\- Per-post/page `noindex` flag; global `noindex` for archives, tags, search (configurable)



\## RSS / Atom Feeds



`FeedController` + `RssService`:



\- `/feed` — RSS 2.0 for all posts

\- `/feed/atom` — Atom 1.0

\- `/category/{slug}/feed` — per-category RSS

\- `/tag/{slug}/feed` — per-tag RSS

\- `/author/{username}/feed` — per-author RSS

\- `/comments/feed` — comment feed



Feed items include: title, link, description (excerpt or full content per setting), `pubDate`, `author`, enclosures

for featured images. Feed autodiscovery `<link>` tags injected in HTML `<head>`. `Content-Type: application/rss+xml; charset=UTF-8`.



\## Sitemap



`SitemapController` + `SitemapService`:



\- `/sitemap.xml` — sitemap index referencing sub-sitemaps

\- `/sitemap-posts.xml`, `/sitemap-pages.xml`, `/sitemap-categories.xml`, `/sitemap-tags.xml`

\- Each `<url>`: `<loc>`, `<lastmod>`, `<changefreq>`, `<priority>`

\- Image sitemap extension for posts with featured images

\- Auto-ping Google/Bing on publish (optional, setting-controlled)

\- `robots.txt` includes `Sitemap:` directive



\## Search



`SearchController` + `SearchService`. Route: `/search?q=...`.



\- MariaDB `FULLTEXT` index on `#Post.title`, `#Post.content`, `#Post.excerpt`; falls back to `LIKE` if FULLTEXT unavailable

\- Minimum query length (configurable in settings), input sanitized

\- Only `published` posts included in results

\- Result snippets with matched terms wrapped in `<mark>`

\- Plugin extension: `search:query\_prepared` event



\## Comments



\- Public comment form: anonymous (name + email) or authenticated (logged-in user). CSRF protected.

\- `CommentController` (public): `POST /comments` — validates, saves as `pending` or `approved` (auto-approve if

&nbsp; `comments.auto\_approve\_known = 1` and commenter has a prior approved comment with same email)

\- Threaded comments up to configurable max depth (`comments.max\_depth = 3`)

\- Spam protection: honeypot field; `comment:before\_save` event for Akismet or custom plugins

\- Comment moderation: admin approves/marks spam/trashes — bulk actions in admin list

\- Notification emails: to post author (new comment awaiting moderation), to commenter (reply received, comment approved)

\- Comment status: `pending | approved | spam | trashed`



\## Caching



`CacheService` with `get`, `set`, `delete`, `flush(prefix)`. Default driver: file (`wwwroot/content/cache/`).

Driver interface allows Redis or other backends as optional plugins.



`CacheMiddleware` checks cache before routing for anonymous public requests. On hit, serves cached HTML and exits.

Admin requests and authenticated users always bypass cache.



Cache key conventions: `post:{uuid}`, `archive:page:{n}`, `category:{slug}:page:{n}`.



Cache invalidation triggers:

\- Post published/updated → invalidate post page, archive pages, category/tag pages, sitemap, RSS

\- Comment approved → invalidate post page

\- Setting changed → full flush



Per-post/page opt-out flag (entity-level cache control).



\## Media Management



\- Upload: `POST /admin/api/v1/media` (multipart). Validates mime type (whitelist) and size (from settings).

\- Image processing: auto-generate thumbnails at configured sizes (e.g., `thumbnail:150x150`, `medium:300x300`,

&nbsp; `large:1024x1024`). Sizes registered by theme in `functions.php`. Uses `GD` (default) or `Imagick`.

\- Storage driver interface: local (default), S3-compatible (plugin).

\- UUID folder split: uses the UUID's natural hyphen positions — `f47ac10b/58cc/4372-a567-0e02b2c3d479.jpg`.

\- Virtual folders via `#Media.parent\_uuid`. Folder rename/move/delete cascades to children.

\- Media library: grid view with search/filter by type and folder.

\- Bulk operations: delete, move to folder.



\## Email / Notifications



`EmailService` wraps PHP `mail()` by default. Driver interface supports SMTP (configurable in settings via `mail.\*` keys).

All sends go through `EmailService::queue()` → stored in `#Email\_Log` → dispatched by the cron runner.



Built-in notifications:

\- New comment awaiting moderation (to admin/post author)

\- Comment approved (to commenter)

\- Reply to comment (to parent commenter)

\- New user registration (to admin)

\- Password reset link (to user)



Email templates are `.phtml` files in `wwwroot/content/views/emails/`.



\## Scheduled Tasks / Cron



`php cli.php cron` runs scheduled tasks (called from system cron, e.g., `\* \* \* \* \*`):



\- Publish scheduled posts (`status = scheduled` and `published\_at <= NOW()`)

\- Dispatch queued emails from `#Email\_Log`

\- Auto-trash old spam comments (configurable retention days)

\- Refresh sitemap cache



Alternatively: pseudo-cron triggered on requests if last run > 1 minute ago (stored in `#Setting`).



\## Maintenance Mode



`#Setting` `app.maintenance\_mode = 1` enables maintenance mode. `MaintenanceModeMiddleware` returns a 503 page for

non-admin users. Admins bypass via a valid JWT. CLI: `php cli.php maintenance:on` / `php cli.php maintenance:off`.



\## Import / Export



\- Export: all posts, pages, categories, tags, settings as DPress JSON or WXR (WordPress eXtended RSS, for WordPress migration)

\- Import: parse WXR or DPress JSON, create entities, re-map UUIDs

\- CLI: `php cli.php export --output=dpress-export.json` and `php cli.php import dpress-export.json`



\## Architecture Notes



\- \*\*ORM\*\*: `dynart-micro-entities` — blocking dependency; entity, repository, and query builder APIs must be defined before implementation.

\- \*\*UUID generation\*\*: PHP-side with `ramsey/uuid` v4. Consistent across all entities.

\- \*\*Admin SPA\*\*: PHP router has a wildcard catch-all for `/admin/\*` that returns the SPA shell (`admin/index.html`). All data via `/admin/api/v1/`.

\- \*\*Transactions\*\*: post save + archive creation must be atomic. Repository layer must support DB transactions.

\- \*\*Character set\*\*: all tables use `utf8mb4` / `utf8mb4\_unicode\_ci`. Explicit in migrations.

\- \*\*Database service\*\*: PDO connection managed by `dynart-micro-entities`. DPress only provides the connection config (`db.\*` keys in `config.ini`).

\- \*\*Testing\*\*: PHPUnit tests live in a sibling `dpress-test/` repository, mirroring the `dynart-micro` convention.



\## Config Reference (draft)



```ini

\[app]

name = "My DPress Site"

tagline = "Just another DPress site"

environment = prod

base\_url = https://example.com

root\_path = /var/www/dpress

maintenance\_mode = 0

admin\_email = admin@example.com

posts\_per\_page = 10

date\_format = "Y-m-d"

time\_format = "H:i"

timezone = UTC



\[theme]

active = default



\[permalink]

structure = /{postname}



\[db]

dsn = "mysql:host=localhost;dbname=dpress;charset=utf8mb4"

username = dpress

password = secret



\[cache]

driver = file     ; file | redis | none

ttl = 3600

path = ~/wwwroot/content/cache



\[mail]

driver = mail     ; mail | smtp

from\_address = noreply@example.com

from\_name = "My DPress Site"

smtp\_host = smtp.example.com

smtp\_port = 587

smtp\_user =

smtp\_pass =

smtp\_encryption = tls



\[markdown]

parser = league/commonmark



\[upload]

max\_size = 10485760

allowed\_mimes = image/jpeg,image/png,image/gif,image/webp,application/pdf

path = ~/wwwroot/content/media

thumbnail\_sizes = "thumbnail:150x150,medium:300x300,large:1024x1024"



\[search]

min\_length = 3

fulltext = 1



\[comments]

require\_approval = 1

auto\_approve\_known = 1

max\_depth = 3

close\_after\_days = 0



\[plugins]

api\_url = https://dpress.dynart.net/api/v1/plugins

```



\## Composer Dependencies (draft)



```json

"require": {

&nbsp;   "dynart/micro": "^0.8",

&nbsp;   "dynart/micro-entities": "^1.0",

&nbsp;   "firebase/php-jwt": "^7.0",

&nbsp;   "league/commonmark": "^2.0",

&nbsp;   "scrivo/highlight.php": "^9.0",

&nbsp;   "ramsey/uuid": "^4.0",

&nbsp;   "ext-pdo": "\*",

&nbsp;   "ext-gd": "\*",

&nbsp;   "ext-mbstring": "\*",

&nbsp;   "ext-json": "\*"

},

"require-dev": {

&nbsp;   "phpunit/phpunit": "^9.5",

&nbsp;   "fakerphp/faker": "^1.0"

}

```

