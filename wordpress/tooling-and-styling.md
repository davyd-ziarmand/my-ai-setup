# Tooling and styling

## File and Naming Conventions

Rules that tooling cannot enforce are listed here. Everything checkable for JS is set up in `eslint.config.mjs`. There
is no PHP linter (Prettier with `@prettier/plugin-php` only formats), so PHP rules are checked in code review.

- Files use kebab-case (lowercase, `-` separated): `my-name-of-file.php`
- Variables, functions, etc. use camelCase with full, declarative names: `$myVarName`, `myFunctionName` in PHP;
  `const myVarName` in JS (enforced by ESLint)
- Object/array keys use kebab-case: `{ "my-key-1": true }` in JS, `["my-key-1" => true]` in PHP
- PHP functions declare return type and argument types when possible
- Keep PHP functions simple, reasonable cognitive complexity. If too complex, split into a separate file with small
  functions, export only what's needed (JS enforced by ESLint)
- Use classes minimally, only when truly needed
- Don't write comments unless necessary; if needed, keep them minimal, 1-2 sentences max

## Styling

Tailwind CSS v4 (CLI) is used for styling. The theme configuration happens in the file: `/src/theme.css`.
Tailwind v4 automatically scans all sources, so all classes in the `src/` folder are picked up.
To build the styles, a script must run from the theme directory. First you need to make sure Tailwind is installed:

```bash
npm install
```

Then you need to run:
```npm run dev``` - when you need to develop components, make changes and rebuild automatically
```npm run build``` - when you need to produce a minified version of the styles for production

After running either of the scripts we get the output file in `assets/css/style.css`. This is a generated file that must
be committed to git; it is never edited manually. This file is enqueued in `functions.php` with a `filemtime` version
(this is needed for cache busting).

## Icons

To add an icon to the site you need to save the svg file in `assets/icons/<name>.svg`. Such icons can then be output
via:

```php
icon("instagram", "size-6 text-brand");
```

All monochrome icons use `currentColor`, so the color is set with a `text-*` class.
For an icon with a fixed color, use a separate "baked-color" SVG file instead of parameterizing the color.
The `icon()` function inserts the SVG inline and merges `$class` into the root `<svg>`.
You should always use this logic instead of inserting a raw `<svg>` into templates.

## Linting and formatting

Before every commit it is advisable to do a full code formatting and linting. This can be done with the following
commands:

```bash
npm run format        # Prettier --write (PHP, JS, CSS, JSON + sorting Tailwind classes)
npm run format:check
npm run lint          # ESLint (flat config) for src/*.js
npm run lint:fix
```