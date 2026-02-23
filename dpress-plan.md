# DPress

A WordPress like blog engine but in default the content editor is a syntax colored Markdown editor
(no WYSIWYG, or at least not in default).

Using `dynart-micro` framework and `dynart-micro-entities` ORM. Only MariaDB supported.

The public frontend (not the admin) is server side rendered (.phtml)

The Markdown should have special shortcodes like in WordPress for PHP code.

## Database structure

The following are the tables:

```
#User
  uuid (sub)
  username
  display_name
  email
  password (hashed, salted)
  bio
  avatar_uuid
  status (active | banned | pending_verification)
  created_at
  updated_at
  email_verified_at (nullable)
  password_reset_token (nullable)
  password_reset_expires_at (nullable)

#Role
  uuid
  name

#User_Role
  user_sub
  role_uuid

#Permission
  uuid
  name

#Role_Permission
  role_uuid
  permission_uuid

#Setting
  name
  value
  group (general | reading | writing | discussion | media | permalink | seo | mail | cache)
  autoload (boolean, loaded on every request vs on-demand)

#Page
  uuid
  user_uuid
  parent_uuid (nullable, for hierarchical pages)
  title
  slug
  content
  excerpt
  status (draft | published | scheduled | trashed)
  published_at (nullable)
  featured_image_uuid (nullable)
  template (nullable, custom template name e.g. "full-width")
  menu_order
  meta_title (nullable, SEO override)
  meta_description (nullable)
  canonical_url (nullable)
  og_image_uuid (nullable)
  created_at
  updated_at

#Page_Archive
  uuid
  version
  original_uuid
  user_uuid
  title
  slug
  content
  excerpt
  change_note (nullable, optional editor note for the version)
  created_at

#Post
  uuid
  user_uuid
  title
  slug
  content
  excerpt (nullable, manual; if absent auto-generated from content)
  status (draft | published | scheduled | trashed)
  published_at (nullable)
  featured_image_uuid (nullable)
  comment_status (open | closed)
  meta_title (nullable, SEO override)
  meta_description (nullable)
  canonical_url (nullable)
  og_image_uuid (nullable)
  views (integer hit counter)
  created_at
  updated_at

#Post_Archive
  uuid
  version
  original_uuid
  user_uuid
  title
  slug
  content
  excerpt
  change_note (nullable)
  created_at

#Tag
  uuid
  name
  slug
  description

#Category
  uuid
  parent_uuid (nullable, for nested categories)
  name
  slug
  description
  featured_image_uuid (nullable)
  meta_title (nullable)
  meta_description (nullable)
  order

#Post_Tag
  post_uuid
  tag_uuid

#Post_Category
  post_uuid
  category_uuid

#Comment
  uuid
  post_uuid
  parent_uuid (nullable, for threaded/nested comments)
  user_uuid (nullable, logged-in commenter)
  author_name (for anonymous commenters)
  author_email
  author_url
  author_ip
  content
  status (pending | approved | spam | trashed)
  created_at
  updated_at

#Menu
  uuid
  name
  location (theme-registered slot: primary | footer | sidebar)
  created_at
  updated_at

#Menu_Item
  uuid
  menu_uuid
  parent_uuid (nullable, for nested items)
  order
  label
  type (url | post | page | category | tag)
  target_uuid (nullable, FK to the target entity)
  url (nullable, for type=url)
  target (_self | _blank)
  css_class

#Redirect
  uuid
  from_path
  to_path
  code (301 | 302)
  created_at

#Media
  uuid
  parent_uuid (only folders can be parents)
  user_uuid (uploader)
  filename
  type (can be `folder`, otherwise the mime like `image/jpeg`)
  alt_text
  title
  caption
  size (file size in bytes)
  metadata (json, depends on the type, for images width/height for example)
  storage_path (actual path on disk, decoupled from UUID convention)
  status (active | trashed)
  created_at
  updated_at

#Plugin
  uuid
  namespace (matches directory name)
  status (active | inactive)
  version
  installed_at
  updated_at

#Email_Log
  uuid
  to_address
  subject
  body
  status (queued | sent | failed)
  created_at
  sent_at (nullable)

#Audit_Log
  uuid
  user_uuid
  action (e.g. post.published, user.login, setting.changed)
  entity_type
  entity_uuid
  payload (json)
  ip_address
  created_at

#Db_Migration
  uuid
  namespace (default is `default`, for plugins later)
  filename
  hash (md5 hash for checking)
```

The migrations are in the `db-migrations/` folder, no rollback. The entity names used as #Entity (basically a table)
in the SQL files.

## Project structure
```
src/
  Admin/
    Controllers/
      DashboardController.php
      PostController.php
      PageController.php
      CategoryController.php
      TagController.php
      CommentController.php
      MediaController.php
      MenuController.php
      UserController.php
      RoleController.php
      SettingController.php
      PluginController.php
      ThemeController.php
      RedirectController.php
    Forms/
      PostForm.php
      PageForm.php
      CategoryForm.php
      TagForm.php
      CommentForm.php
      UserForm.php
      SettingForm.php
    Lists/
      PostAdminList.php
      PageAdminList.php
      CategoryAdminList.php
      TagAdminList.php
      CommentAdminList.php
      MediaAdminList.php
      UserAdminList.php
  Controllers/
    HomeController.php
    PostController.php      (public post/archive/tag/category pages)
    PageController.php      (public static pages)
    SearchController.php
    CommentController.php   (public comment submission)
    FeedController.php      (RSS/Atom)
    SitemapController.php
    AuthController.php      (admin login/logout, issues JWT)
  Entities/
    User.php
    Role.php
    Permission.php
    Post.php
    Page.php
    PostArchive.php
    PageArchive.php
    Tag.php
    Category.php
    Comment.php
    Menu.php
    MenuItem.php
    Media.php
    Setting.php
    Redirect.php
    Plugin.php
    EmailLog.php
    AuditLog.php
    DbMigration.php
  Repositories/
    UserRepository.php
    PostRepository.php
    PageRepository.php
    TagRepository.php
    CategoryRepository.php
    CommentRepository.php
    MenuRepository.php
    MediaRepository.php
    SettingRepository.php
    RedirectRepository.php
    PluginRepository.php
  Services/
    PostService.php         (create/update/publish/trash, fires events)
    PageService.php
    CommentService.php      (moderation, spam check hook)
    MediaService.php        (upload, resize, thumbnail generation)
    MarkdownService.php     (parse + shortcode processing)
    ShortcodeService.php    (registry + dispatch)
    SlugService.php         (generation + uniqueness check)
    SearchService.php       (fulltext or LIKE-based)
    SettingService.php      (cached get/set)
    EmailService.php        (send + log)
    MigrationService.php    (run pending migrations for core + plugins)
    CacheService.php
    ThemeService.php
    PluginService.php       (discover, activate, deactivate, install from GitHub)
    RssService.php
    SitemapService.php
    SeoService.php          (meta/OG/JSON-LD generation)
    AuditService.php
    PermissionService.php   (check(string $permission): bool)
  Middleware/
    AdminAuthMiddleware.php      (JWT guard for admin API routes)
    MaintenanceModeMiddleware.php
    RedirectMiddleware.php       (process #Redirect table on each request)
    CacheMiddleware.php          (serve cached pages for anonymous public requests)
  Plugin/
    AbstractPlugin.php      (lifecycle: install, activate, deactivate, uninstall)
    PluginRegistry.php      (discover and load active plugins)
  Cli/
    Commands/
      MigrateCommand.php
      MigrateStatusCommand.php
      CacheClearCommand.php
      UserCreateCommand.php
      PluginInstallCommand.php
      PluginActivateCommand.php
      PluginDeactivateCommand.php
      PluginListCommand.php
      ThemeActivateCommand.php
      MaintenanceOnCommand.php
      MaintenanceOffCommand.php
      SearchIndexCommand.php
      CronCommand.php        (scheduled tasks runner)
      ExportCommand.php
      ImportCommand.php
      SeedCommand.php        (dev only, demo content)
db-migrations/
  2026-01-01-1.sql
  2026-01-01-2.sql
plugins/
  CustomPluginFrontend/
    views/
      custom-plugin-frontend-view.phtml
    CustomPluginFrontend.php (extends AbstractPlugin.php)
    CustomPluginFrontendController.php
    plugin.json (name, author, version, requires)
  CustomPluginAdmin/
    db-migrations/
      2026-01-01-1.sql
    CustomPluginAdmin.php (extends AbstractPlugin.php)
    plugin.json (name, author, version, requires)
wwwroot/
  admin/
    (React + shadcn SPA build output)
  content/
    cache/                  (file-based page cache)
    media/
      uploaded files with UUID folders/filename like: f47ac10b/58cc/4372-a567-0e02b2c3d479.jpg
      split at hyphen positions of the UUID, original name/mime stored in db with virtual folders
    views/
      layout.phtml
      home.phtml
      single.phtml          (single post)
      page.phtml            (static page)
      archive.phtml         (post archive)
      category.phtml
      tag.phtml
      author.phtml
      search.phtml
      404.phtml
      pager.phtml
      form-errors.phtml
      form-field.phtml
      form-input.phtml
      emails/
        comment-notification.phtml
        comment-approved.phtml
        password-reset.phtml
        new-user.phtml
    themes/
      default/
        style.css
        functions.php       (registers menus, image sizes, shortcodes, etc.)
        theme.json          (name, author, version, description, requires_dpress, screenshot, supported_features)
  index.php
  index.dev.php (only for development)
  robots.txt
  .htaccess               (mod_rewrite rules)
cli.php                   (CLI entry point)
composer.json
config.ini
config.dev.ini (only for development)
```

## Plugins

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

## Lists / Forms

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

## Admin API

The React + shadcn admin is a SPA. PHP serves `/admin/index.html` as a catch-all for all `/admin/*` routes; all data
goes through a REST API at `/admin/api/v1/`. Authentication uses JWT — the login endpoint returns a signed token, the
SPA stores it and sends `Authorization: Bearer <token>` on every request. `dynart-micro`'s existing `JwtAuth` and
`JwtValidator` infrastructure is reused directly.

Consistent response envelopes:
- Success: `{"data": {...}, "meta": {"total": N, "page": N}}`
- Error: `{"error": {"code": 422, "message": "...", "fields": {...}}}`

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

## Authentication (Admin)

The admin uses JWT. Flow:

- `AuthController` handles `POST /admin/api/v1/auth/login` — validates email + password, returns a signed JWT
- `AuthController` handles `POST /admin/api/v1/auth/logout` — client discards token (stateless; optionally a token
  denylist in `#Setting` or Redis for immediate revocation)
- The SPA stores the token (memory or `localStorage`) and attaches it as `Authorization: Bearer <token>`
- `AdminAuthMiddleware` (wrapping `dynart-micro`'s `JwtValidator`) validates the token on every admin API request
- Rate limiting: lockout after N failed login attempts (configurable in `#Setting`)
- Forgot password: generates `password_reset_token`, stores expiry, emails a reset link
- Email verification: optional, sends a verification link to `email_verified_at`

## User Roles & Permissions (RBAC)

Built-in roles: `super_admin`, `admin`, `editor`, `author`, `contributor`, `subscriber`.

Built-in permissions (examples):
- `posts.create`, `posts.edit_own`, `posts.edit_any`, `posts.delete_any`, `posts.publish`
- `pages.create`, `pages.edit_any`, `pages.publish`
- `comments.moderate`, `comments.delete`
- `media.upload`, `media.delete_any`
- `users.manage`, `settings.manage`, `plugins.manage`, `themes.manage`

`PermissionService::check(string $permission): bool` — checks the currently authenticated user's permissions.
Roles are seeded by migrations. Custom roles can be created via the admin UI.

## Shortcode System

`ShortcodeService` maintains a registry: `name => callable`. Callable signature: `fn(array $attrs, string $content): string`.

Syntax: `[shortcode attr="value"]inner content[/shortcode]` and self-closing `[shortcode /]`.

Built-in shortcodes:
- `[code lang="php"]` — syntax-highlighted code block (server-side via `scrivo/highlight.php`, emits `<pre>` with class)
- `[gallery ids="uuid1,uuid2"]` — renders a gallery grid from media UUIDs
- `[caption]` — figure + figcaption wrapper
- `[embed url="..."]` — oEmbed-style (YouTube, Vimeo, etc.)

`MarkdownService` runs the shortcode pass **before** Markdown parsing (avoids conflicts with fenced code blocks),
then passes the result through `league/commonmark`.

Plugins register shortcodes via `ShortcodeService::register(string $name, callable $handler)`.
Events: `shortcode:before_render`, `shortcode:after_render`.

## Markdown Editor

Markdown parser: `league/commonmark` (extensible, PSR-compliant). Added to `composer.json`.

Admin editor (React): CodeMirror with Markdown mode, split-pane live preview. Supports:
- Syntax highlighting of code blocks (client-side, Prism or similar)
- Image drag-and-drop: uploads to `POST /admin/api/v1/media`, inserts `![alt](url)` or `[gallery]`
- Shortcode autocompletion (v2 feature)

Frontend rendering: `MarkdownService::render(string $markdown): string` — shortcode pass → CommonMark → sanitize HTML output.

## Theme System

`ThemeService` resolves the active theme from `#Setting` (`theme.active`). Themes live in `wwwroot/content/themes/`.

`theme.json` fields: `name`, `author`, `version`, `description`, `requires_dpress`, `screenshot`, `supported_features`
(e.g., `["post-thumbnails", "custom-header", "menus", "custom-logo"]`).

`functions.php` in the theme root is auto-loaded when the theme activates. It registers nav menu locations, image sizes,
shortcodes, and widget areas.

Template hierarchy (fallback chain, WordPress-style):
- Single post: `single-{slug}.phtml` → `single.phtml` → `index.phtml`
- Static page: `page-{slug}.phtml` → `page-{template}.phtml` → `page.phtml` → `index.phtml`
- Category archive: `category-{slug}.phtml` → `category.phtml` → `archive.phtml` → `index.phtml`
- Tag archive: `tag-{slug}.phtml` → `tag.phtml` → `archive.phtml` → `index.phtml`
- Author archive: `author-{username}.phtml` → `author.phtml` → `archive.phtml` → `index.phtml`
- Search results: `search.phtml` → `index.phtml`
- 404: `404.phtml`

Child theme support: child theme path checked first, falls back to parent theme.

## Permalinks / Slug

Global permalink structure configured in `#Setting` (`permalink.structure`). Options:
- Plain: `/?p={uuid}`
- Post name: `/{slug}` (default)
- Date-based: `/{year}/{month}/{day}/{slug}`
- Custom: `/{category}/{slug}`

`SlugService::generate(string $title): string` — transliterates, lowercases, replaces spaces with `-`, ensures uniqueness
by appending `-2`, `-3`, etc. `RedirectMiddleware` processes the `#Redirect` table on each request to handle old slugs
after a rename.

## SEO

`SeoService` generates all SEO output for templates (meta tags, Open Graph, Twitter Cards, JSON-LD):

- `<title>`: `meta_title ?? title . ' | ' . site_name`
- `<meta name="description">`: `meta_description ?? excerpt`
- Open Graph: `og:title`, `og:description`, `og:image`, `og:type`, `og:url`
- Twitter Card: `twitter:card`, `twitter:title`, `twitter:description`, `twitter:image`
- JSON-LD: `Article` for posts, `WebSite` for homepage, `BreadcrumbList`
- `<link rel="canonical">`: `canonical_url ?? generated_url`
- Per-post/page `noindex` flag; global `noindex` for archives, tags, search (configurable)

## RSS / Atom Feeds

`FeedController` + `RssService`:

- `/feed` — RSS 2.0 for all posts
- `/feed/atom` — Atom 1.0
- `/category/{slug}/feed` — per-category RSS
- `/tag/{slug}/feed` — per-tag RSS
- `/author/{username}/feed` — per-author RSS
- `/comments/feed` — comment feed

Feed items include: title, link, description (excerpt or full content per setting), `pubDate`, `author`, enclosures
for featured images. Feed autodiscovery `<link>` tags injected in HTML `<head>`. `Content-Type: application/rss+xml; charset=UTF-8`.

## Sitemap

`SitemapController` + `SitemapService`:

- `/sitemap.xml` — sitemap index referencing sub-sitemaps
- `/sitemap-posts.xml`, `/sitemap-pages.xml`, `/sitemap-categories.xml`, `/sitemap-tags.xml`
- Each `<url>`: `<loc>`, `<lastmod>`, `<changefreq>`, `<priority>`
- Image sitemap extension for posts with featured images
- Auto-ping Google/Bing on publish (optional, setting-controlled)
- `robots.txt` includes `Sitemap:` directive

## Search

`SearchController` + `SearchService`. Route: `/search?q=...`.

- MariaDB `FULLTEXT` index on `#Post.title`, `#Post.content`, `#Post.excerpt`; falls back to `LIKE` if FULLTEXT unavailable
- Minimum query length (configurable in settings), input sanitized
- Only `published` posts included in results
- Result snippets with matched terms wrapped in `<mark>`
- Plugin extension: `search:query_prepared` event

## Comments

- Public comment form: anonymous (name + email) or authenticated (logged-in user). CSRF protected.
- `CommentController` (public): `POST /comments` — validates, saves as `pending` or `approved` (auto-approve if
  `comments.auto_approve_known = 1` and commenter has a prior approved comment with same email)
- Threaded comments up to configurable max depth (`comments.max_depth = 3`)
- Spam protection: honeypot field; `comment:before_save` event for Akismet or custom plugins
- Comment moderation: admin approves/marks spam/trashes — bulk actions in admin list
- Notification emails: to post author (new comment awaiting moderation), to commenter (reply received, comment approved)
- Comment status: `pending | approved | spam | trashed`

## Caching

`CacheService` with `get`, `set`, `delete`, `flush(prefix)`. Default driver: file (`wwwroot/content/cache/`).
Driver interface allows Redis or other backends as optional plugins.

`CacheMiddleware` checks cache before routing for anonymous public requests. On hit, serves cached HTML and exits.
Admin requests and authenticated users always bypass cache.

Cache key conventions: `post:{uuid}`, `archive:page:{n}`, `category:{slug}:page:{n}`.

Cache invalidation triggers:
- Post published/updated → invalidate post page, archive pages, category/tag pages, sitemap, RSS
- Comment approved → invalidate post page
- Setting changed → full flush

Per-post/page opt-out flag (entity-level cache control).

## Media Management

- Upload: `POST /admin/api/v1/media` (multipart). Validates mime type (whitelist) and size (from settings).
- Image processing: auto-generate thumbnails at configured sizes (e.g., `thumbnail:150x150`, `medium:300x300`,
  `large:1024x1024`). Sizes registered by theme in `functions.php`. Uses `GD` (default) or `Imagick`.
- Storage driver interface: local (default), S3-compatible (plugin).
- UUID folder split: uses the UUID's natural hyphen positions — `f47ac10b/58cc/4372-a567-0e02b2c3d479.jpg`.
- Virtual folders via `#Media.parent_uuid`. Folder rename/move/delete cascades to children.
- Media library: grid view with search/filter by type and folder.
- Bulk operations: delete, move to folder.

## Email / Notifications

`EmailService` wraps PHP `mail()` by default. Driver interface supports SMTP (configurable in settings via `mail.*` keys).
All sends go through `EmailService::queue()` → stored in `#Email_Log` → dispatched by the cron runner.

Built-in notifications:
- New comment awaiting moderation (to admin/post author)
- Comment approved (to commenter)
- Reply to comment (to parent commenter)
- New user registration (to admin)
- Password reset link (to user)

Email templates are `.phtml` files in `wwwroot/content/views/emails/`.

## Scheduled Tasks / Cron

`php cli.php cron` runs scheduled tasks (called from system cron, e.g., `* * * * *`):

- Publish scheduled posts (`status = scheduled` and `published_at <= NOW()`)
- Dispatch queued emails from `#Email_Log`
- Auto-trash old spam comments (configurable retention days)
- Refresh sitemap cache

Alternatively: pseudo-cron triggered on requests if last run > 1 minute ago (stored in `#Setting`).

## Maintenance Mode

`#Setting` `app.maintenance_mode = 1` enables maintenance mode. `MaintenanceModeMiddleware` returns a 503 page for
non-admin users. Admins bypass via a valid JWT. CLI: `php cli.php maintenance:on` / `php cli.php maintenance:off`.

## Import / Export

- Export: all posts, pages, categories, tags, settings as DPress JSON or WXR (WordPress eXtended RSS, for WordPress migration)
- Import: parse WXR or DPress JSON, create entities, re-map UUIDs
- CLI: `php cli.php export --output=dpress-export.json` and `php cli.php import dpress-export.json`

## Architecture Notes

- **ORM**: `dynart-micro-entities` — blocking dependency; entity, repository, and query builder APIs must be defined before implementation.
- **UUID generation**: PHP-side with `ramsey/uuid` v4. Consistent across all entities.
- **Admin SPA**: PHP router has a wildcard catch-all for `/admin/*` that returns the SPA shell (`admin/index.html`). All data via `/admin/api/v1/`.
- **Transactions**: post save + archive creation must be atomic. Repository layer must support DB transactions.
- **Character set**: all tables use `utf8mb4` / `utf8mb4_unicode_ci`. Explicit in migrations.
- **Database service**: PDO connection managed by `dynart-micro-entities`. DPress only provides the connection config (`db.*` keys in `config.ini`).
- **Testing**: PHPUnit tests live in a sibling `dpress-test/` repository, mirroring the `dynart-micro` convention.

## Config Reference (draft)

```ini
[app]
name = "My DPress Site"
tagline = "Just another DPress site"
environment = prod
base_url = https://example.com
root_path = /var/www/dpress
maintenance_mode = 0
admin_email = admin@example.com
posts_per_page = 10
date_format = "Y-m-d"
time_format = "H:i"
timezone = UTC

[theme]
active = default

[permalink]
structure = /{postname}

[db]
dsn = "mysql:host=localhost;dbname=dpress;charset=utf8mb4"
username = dpress
password = secret

[cache]
driver = file     ; file | redis | none
ttl = 3600
path = ~/wwwroot/content/cache

[mail]
driver = mail     ; mail | smtp
from_address = noreply@example.com
from_name = "My DPress Site"
smtp_host = smtp.example.com
smtp_port = 587
smtp_user =
smtp_pass =
smtp_encryption = tls

[markdown]
parser = league/commonmark

[upload]
max_size = 10485760
allowed_mimes = image/jpeg,image/png,image/gif,image/webp,application/pdf
path = ~/wwwroot/content/media
thumbnail_sizes = "thumbnail:150x150,medium:300x300,large:1024x1024"

[search]
min_length = 3
fulltext = 1

[comments]
require_approval = 1
auto_approve_known = 1
max_depth = 3
close_after_days = 0

[plugins]
api_url = https://dpress.dynart.net/api/v1/plugins
```

## Implementation Phases (draft)

### Phase 1 — Foundation

Goal: a running application that can connect to the database and run migrations.

- `composer.json` with all dependencies, autoload config
- `config.ini` structure, `config.dev.ini`
- `wwwroot/index.php` entry point wiring `Micro::run()`
- `dynart-micro-entities` integration: `Database` service configured from `db.*` config keys
- `MigrationService`: discovers and runs pending `.sql` files from `db-migrations/`, records in `#Db_Migration`
  (with `namespace` column from day one)
- `MigrateCommand` + `MigrateStatusCommand` CLI commands
- `cli.php` entry point
- First migration: all core tables
- `SettingService`: cached `get(string $key, mixed $default)` / `set(string $key, mixed $value)`
- `SlugService`: `generate(string $title): string` with uniqueness check
- `.htaccess` with mod_rewrite rules
- Basic `SeedCommand` (dev only) for a default admin user and sample content

---

### Phase 2 — Authentication & Users

Goal: JWT login/logout, user management, RBAC in place.

- `User`, `Role`, `Permission` entities + repositories
- `UserRepository::findByEmail()`, `findByUuid()`
- `AuthController`: `POST /admin/api/v1/auth/login` → validates credentials, returns signed JWT
- `AuthController`: `POST /admin/api/v1/auth/logout` (stateless, client discards token)
- `AdminAuthMiddleware`: wraps `dynart-micro` `JwtValidator`, guards all `/admin/api/v1/*` routes
- `PermissionService::check(string $permission): bool`
- `#[Authorize]` attribute applied to admin controllers via `dynart-micro` `JwtAuth`
- Password hashing (PHP `password_hash` / `password_verify`)
- Forgot password: `password_reset_token` generation + `EmailService::queue()` (email sending deferred to Phase 10)
- `UserCreateCommand` CLI command (for bootstrapping the first admin)
- Seed migration: built-in roles (`super_admin`, `admin`, `editor`, `author`, `contributor`, `subscriber`) and permissions

---

### Phase 3 — Content Core

Goal: posts, pages, categories, tags fully manageable via admin API.

- `Post`, `Page`, `Category`, `Tag`, `PostArchive`, `PageArchive` entities + repositories
- `PostService`: create, update, publish, trash — emits events (`post:created`, `post:published`, etc.)
- `PageService`: same lifecycle + events
- `MarkdownService`: shortcode pass → `league/commonmark` → sanitized HTML
- `ShortcodeService`: registry; built-in `[code]` shortcode with `scrivo/highlight.php`
- `FormFactory` and `ListFactory` with event emissions (`*:created`, `*:queryPrepared`) — plugin hooks in place even
  though no plugins exist yet
- Admin API endpoints:
  - `GET/POST /admin/api/v1/posts`, `GET/PUT/DELETE /admin/api/v1/posts/{uuid}`
  - `GET/POST /admin/api/v1/pages`, `GET/PUT/DELETE /admin/api/v1/pages/{uuid}`
  - `GET/POST /admin/api/v1/categories`, `GET/PUT/DELETE /admin/api/v1/categories/{uuid}`
  - `GET/POST /admin/api/v1/tags`, `GET/PUT/DELETE /admin/api/v1/tags/{uuid}`
- Slug auto-generation on create; uniqueness enforced
- Version archiving: every save of a post/page creates a `#Post_Archive` / `#Page_Archive` row (atomic, in a transaction)
- Scheduled post support: `status = scheduled` + `published_at`
- `CronCommand` stub (activates scheduled posts; extended in Phase 10)

---

### Phase 4 — Media

Goal: file uploads work, images have thumbnails, media library browsable.

- `Media` entity + repository
- `MediaService`: upload, mime/size validation, UUID-based storage path, thumbnail generation with GD
- Image sizes registered via config (`upload.thumbnail_sizes`)
- Virtual folder support via `#Media.parent_uuid`
- Admin API endpoints:
  - `POST /admin/api/v1/media` (multipart upload)
  - `GET /admin/api/v1/media` (list with folder/type filter)
  - `DELETE /admin/api/v1/media/{uuid}`
- Storage driver interface (local default; S3-compatible deferred to plugins)

---

### Phase 5 — Public Frontend

Goal: a fully working server-side rendered public site.

- `HomeController`: paginated post list
- `PostController`: single post, archive, category archive, tag archive, author archive
- `PageController`: static pages resolved by slug
- `SearchController` + `SearchService`: fulltext search with MariaDB `FULLTEXT` index
- `RedirectMiddleware`: processes `#Redirect` table on each request
- Theme system: `ThemeService`, `functions.php` auto-load, template hierarchy fallback chain
- Default theme: `layout.phtml`, `home.phtml`, `single.phtml`, `page.phtml`, `archive.phtml`,
  `category.phtml`, `tag.phtml`, `author.phtml`, `search.phtml`, `404.phtml`, `pager.phtml`
- `SeoService`: meta tags, Open Graph, Twitter Card, JSON-LD, canonical URL — used in all public templates
- `MaintenanceModeMiddleware`

---

### Phase 6 — Admin SPA

Goal: usable admin interface for all content management.

- React + shadcn project scaffolding in `wwwroot/admin/`
- PHP wildcard catch-all `/admin/*` → serves SPA shell
- SPA views:
  - Login
  - Dashboard (post/comment/media counts, recent activity)
  - Post list + editor (CodeMirror Markdown, live preview, media picker)
  - Page list + editor
  - Category list + form
  - Tag list + form
  - Media library (grid, upload, folder navigation)
  - Settings (general, reading, writing, mail, permalink)
  - User list + form
- JWT stored in memory or `localStorage`; attached as `Authorization: Bearer` on all API requests
- Build output committed to `wwwroot/admin/` or generated by CI

---

### Phase 7 — Comments

Goal: threaded public comments with admin moderation.

- `Comment` entity + repository
- `CommentController` (public): `POST /comments` — CSRF, honeypot, auto-approve logic
- Threaded display in `single.phtml` up to `comments.max_depth`
- Admin API: `GET/PUT/DELETE /admin/api/v1/comments` (approve, spam, trash, bulk actions)
- Admin SPA: comment moderation list
- `comment:before_save` event (plugin hook for Akismet etc.)

---

### Phase 8 — RSS, Sitemap & Feeds

Goal: feed autodiscovery and sitemap for SEO/indexing.

- `RssService` + `FeedController`: `/feed`, `/feed/atom`, per-category/tag/author feeds
- Feed autodiscovery `<link>` tags in all public `<head>` output
- `SitemapService` + `SitemapController`: `/sitemap.xml` index + sub-sitemaps
- `robots.txt` with `Sitemap:` directive
- Auto-ping on publish (optional, setting-controlled, via `post:published` event)

---

### Phase 9 — Caching & Performance

Goal: anonymous public pages served from cache; cache invalidated on content changes.

- `CacheService`: file-based driver, `get/set/delete/flush(prefix)`
- `CacheMiddleware`: intercepts anonymous requests before routing
- Cache invalidation wired into `PostService`, `CommentService`, `SettingService` via events
- `CacheClearCommand` CLI command
- Admin and authenticated requests always bypass cache

---

### Phase 10 — Email, Cron & Notifications

Goal: email notifications sent reliably; scheduled tasks run automatically.

- `EmailService`: `mail()` default driver, SMTP driver, `queue()` → `#Email_Log`
- Email templates: comment notification, comment approved, password reset, new user
- `CronCommand` extended: dispatch email queue, publish scheduled posts, auto-trash old spam, refresh sitemap cache
- System cron entry documented (`* * * * * php /path/to/cli.php cron`)
- Menus: `Menu`, `MenuItem` entities + repositories, admin API endpoints, SPA menu editor, frontend rendering
- Redirects: admin API + SPA UI for `#Redirect` management
- Audit log: `AuditService` wired into key service events

---

### Phase 11 — Import / Export

Goal: content can be backed up and migrated.

- `ExportCommand`: dumps posts, pages, categories, tags, settings to DPress JSON
- `ImportCommand`: parses DPress JSON or WordPress WXR, creates entities, re-maps UUIDs
- Media import: download linked images or accept a ZIP bundle

---

### Phase 12 — Plugins *(deferred)*

Goal: third-party extensions without modifying core.

- `AbstractPlugin` lifecycle contract (`install`, `activate`, `deactivate`, `uninstall`)
- `PluginRegistry`: discovers `plugins/*/plugin.json`, checks `#Plugin` table, loads active plugins
- `PluginService`: install from GitHub ZIP, activate, deactivate
- Plugin CLI commands: `plugin:install`, `plugin:activate`, `plugin:deactivate`, `plugin:list`
- Admin SPA: plugin management page
- Update check against `https://dpress.dynart.net/api/v1/plugins`
- Dependency resolution (`requires` in `plugin.json`)

> Note: event hooks, `FormFactory`/`ListFactory` emissions, and `#Db_Migration.namespace` are in place from
> Phase 3 onward, so this phase is purely additive — no core refactoring needed.

---

## Composer Dependencies (draft)

```json
{
    "require": {
        "dynart/micro": "^0.8",
        "dynart/micro-entities": "^1.0",
        "firebase/php-jwt": "^7.0",
        "league/commonmark": "^2.0",
        "scrivo/highlight.php": "^9.0",
        "ramsey/uuid": "^4.0",
        "ext-pdo": "*",
        "ext-gd": "*",
        "ext-mbstring": "*",
        "ext-json": "*"
    },
    "require-dev": {
        "phpunit/phpunit": "^9.5",
        "fakerphp/faker": "^1.0"
    }
}
```