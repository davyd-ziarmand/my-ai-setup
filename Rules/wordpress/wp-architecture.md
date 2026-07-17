# WordPress Website Architecture

The project architecture is very important and must be followed.

## Project root

### GitHub
- `.github/workflows` -> files for GitHub; required: `deploy.yml`

### IDE: IntelliJ IDEA / PhpStorm / WebStorm
All developers use IntelliJ IDEA / PhpStorm / WebStorm, these are shared settings.

- `.idea` 
- `.idea/project-name.iml` -> excludes from indexing the folders that are not needed for development, speeds up project startup
- `.idea/vcs.xml` -> version control system configuration for the project (git)
- `.idea/modules.xml` -> registry of the project's modules. Without this file the IDE won't know which `.iml` file to load when opening the project
- `.idea/scopes/Project_Source_Files.xml` -> defines the set of files shown in the project file tree, the scope is called "Project Source Files"

### LocalWP
- `conf` -> configuration files of the local server that LocalWP spins up for the site
- `logs` -> log files of the local server
- `app` -> folder for the site itself
- `app/sql` -> database dump
- `app/setup` -> files that should be run on a clean new project, for initial setup
- `app/public` -> WordPress root, what is actually served to the browser

### Other:
- `docs` -> project documentation files, only the important stuff here
- `.gitignore` -> rules for folders and files that should not be added to GitHub
- `AGENTS.md` -> the common standard for AI agents (Codex, Cursor, and others that support it)
- `CLAUDE.md` -> context/instructions for Claude Code
- `GEMINI.md` -> the same for Gemini CLI
- `README.md` -> a regular description of the project for humans

## The WordPress part of the site project

### Plugins:
The following WordPress plugins must be installed for the site to work correctly:
- SCF (secure-custom-fields) -> allows adding custom fields to pages/posts/products, etc
- woocommerce -> if the site is an online store, this plugin is needed for: the cart, payment, product catalog, orders, etc
- all-in-one-wp-migration -> a plugin for site migration, convenient for moving the site between environments (local -> production)

### Theme
The project's theme must always be nicely structured in a single style. 
The project theme must be located in the folder: `app/public/wp-content/themes/{theme-name}`.
More about the structure below:

#### Mandatory for WordPress

- `style.css` -> the main theme file, contains metadata
- `package.json` / `package-lock.json` -> dependencies and build scripts (npm)
- `.prettierignore` / `.prettierrc.json` -> code formatting settings
- `eslint.config.mjs` -> JavaScript linting rules

#### Mandatory for WooCommerce
Overridden WooCommerce templates.

- `woocommerce/`
- `woocommerce/archive-product.php` -> product catalog page
- `woocommerce/content-product.php` -> a single product card
- `woocommerce/single-product.php` -> individual product page
- `woocommerce/cart/cart.php` -> cart page
- `woocommerce/checkout/form-checkout.php` -> order checkout form

#### PHP templates (WordPress core)
- `index.php` -> the base fallback template, a mandatory theme file
- `header.php` -> the site header, included on all pages
- `footer.php` -> the site footer, included on all pages
- `functions.php` -> registration of hooks, custom functions, enqueuing styles/scripts, theme settings
- `front-page.php` -> the home page template
- `page.php` -> the default template for regular pages
- `404.php` -> the "not found" page
- `maintenance.php` -> the maintenance page

#### Custom page templates
If the site has custom pages that require a unique design, then you need to create a file of the form:
`page-{slug}.php` (for example `page-about-us.php`) for a page with the slug `about-us`, WordPress
will automatically pick it up as a template. Or name the file arbitrarily and add at the beginning the comment
`/* Template Name: About Us */`, then the template will appear in the dropdown in the "Page Attributes" menu in the admin panel, from where it
will need to be selected manually. Inside the file you must include `get_header()` and `get_footer()`, and between them,
you can write the page markup.

#### Static assets
Any images, icons, fonts, css, etc. must be stored in the `assets` folder in the theme root; there must be
no php or js code here.

#### Theme core src/
The project uses the FDD pattern - Feature Driven Development. The folder contains:

- `src/theme.css` -> theme styles Tailwind css (separate from `style.css` in the root, which contains only metadata)

##### Features (logical modules)
- `src/features/` -> the folder contains all the theme's logical modules (features)
- `src/features/{feature-name}/` -> each feature (a page or a logical module of the site) has its own folder
- `src/features/{feature-name}/pages/{page-name}.php` -> the page template itself, simply imported in custom templates of the `page-{slug}.php` kind
- `src/features/{feature-name}/sections/{section-name}/` -> individual sections of the page
- `src/features/{feature-name}/sections/{section-name}/{section-name}.fields.php` -> file with the registration of the SCF plugin's custom fields
- `src/features/{feature-name}/sections/{section-name}/{section-name}.php` -> the section markup file
- `src/features/{feature-name}/sections/{section-name}/seed-images/` -> test images for filling the section with default content

##### Shared code (shared)
Among all the features in the theme there may be shared code, NOT tied to a specific feature.
- `src/shared/`
- `src/shared/api/` -> functions for working with external/internal APIs, for example form processing
- `src/shared/helpers/` -> helper PHP functions used in several places in the theme

##### Widgets
These are reusable elements in the theme (for example menu or footer), logically the same as sections in each feature,
but unlike them NOT tied to a specific feature. Widgets are divided into 3 groups by size:
- `src/shared/widgets/atoms/` -> the smallest indivisible elements, for example: button, input, icon, label, etc.
- `src/shared/widgets/molecules/` -> simple sections composed of several atoms, for example a search field (input + button), a card with an icon and text
- `src/shared/widgets/organisms/` -> complex self-contained blocks, assembled from molecules and atoms, for example menu, footer, etc.

Each widget has:
- `src/shared/widgets/{size}/{widget-name}/{widget-name}.php` -> the widget markup file
- `src/shared/widgets/{size}/{widget-name}/{widget-name}.fields.php` -> file with the registration of the SCF plugin's custom fields
- `src/shared/widgets/{size}/{widget-name}/seed-images/` -> test images for filling the widget with default content