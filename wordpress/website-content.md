# Editable site content (SCF)

All site content, texts and images, is edited from `wp-admin -> Site Content`, a single parent menu with
subpages for each page. All subpages write into one global options store.

## How it works

1. Fields are declared in code, not in the DB. Each section or widget has a `*.fields.php` file located next to it.
2. The `theme_field_sections()` function in the `src/shared/helpers/scf.php` helper collects them by several patterns:

- `src/features/*/sections/*/*.fields.php`
- `src/shared/widgets/atoms/*/*.fields.php`
- `src/shared/widgets/molecules/*/*.fields.php`
- `src/shared/widgets/organisms/*/*.fields.php`

3. Fields are sorted by the `order` key, grouped by the `group` key and registered as a single group split into tabs.
   Each tab is a separate subpage in the admin panel.
4. Any sections/widgets and other templates in the theme read values through the `theme_field($selector)` function from
   the
   `src/shared/helpers/scf.php` file. This function is a thin wrapper over `get_field($selector, "option")`. Templates
   contain no inline default values.

Usage example:

```php
<h1><?= esc_html(theme_field("hero_title")) ?></h1>
<img src="<?= esc_url(theme_field("hero_bg")) ?>" alt="...">
```

## Dependency on the SCF plugin

The site requires the free ["Secure Custom Fields"](https://wordpress.org/plugins/secure-custom-fields/) plugin.
This plugin is a mandatory dependency of the theme and is declared via `Requires Plugins: secure-custom-fields` in
`style.css` in the theme root.
The `src/shared/helpers/scf.php` file completely blocks the frontend (HTTP 503) and shows a message in the admin panel
when the plugin is inactive.
The plugin itself is in `.gitignore`, so SCF must be installed manually in every environment (locally and on
production).

## Admin subpages

In the admin panel, for convenience each custom website page is placed in the left sidebar as a child item of the "Site
Content" item. All pages must be set manually through the `theme_content_pages()` function in the `src/shared/helpers/scf.php` file.

## Format of *.fields.php files

Each *.fields.php file must be in a special format:

```php
return [
  "name" => "our-story", // internal arbitrary name of the field group
  "label" => "Our story", // name of the field group, shown as the tab name in the admin panel
  "order" => 20, // ordinal number of the tab from 0 to 100
  "group" => "custom-furniture", // merges several fields into one tab (shared group key)
  "fields" => [], // list of fields that exist in this tab
];
```

Fields can have different parameters depending on their type.

### Text field (single-line)

```php
return [
  "type" => "text",
  "key" => "field_hero_title", // unique field key across the whole site, prefixed with field_
  "label" => "My hero title", // human-readable label shown in the admin panel
  "name" => "hero_title", // selector used to read the value: theme_field("hero_title")
  "default_value" => "Furnitures for your house", // value shown until the editor changes it
  
  // Optional
  "placeholder" => "Enter a title", // grey hint text inside an empty input
  "instructions" => "Shown in the header", // description under the field
  "maxlength" => 120, // maximum number of characters
  "required" => 0, // 0 or 1 whether the field is mandatory
];
```

### Text field (multi-line)

```php
return [
  "type" => "textarea",
  "key" => "field_hero_description", // unique field key across the whole site, prefixed with field_
  "label" => "Description", // human-readable label shown in the admin panel
  "name" => "hero_description", // selector used to read the value: theme_field("hero_description")
  
  // Optional
  "rows" => 3, // height of the field in rows
  "default_value" => "We make furniture...", // value shown until the editor changes it
  "placeholder" => "Enter a description", // grey hint text inside an empty input
  "instructions" => "Brief description of the section", // description under the field
];
```

### Image

```php
return [
  "type" => "image",
  "key" => "field_hero_bg", // unique field key across the whole site, prefixed with field_
  "label" => "Background image", // human-readable label shown in the admin panel
  "name" => "hero_bg", // selector used to read the value: theme_field("hero_bg")
  "return_format" => "url", // what theme_field() returns: "url" (default in the theme), "array" or "id"
  "seed" => "hero.jpg", // theme extension: default image file name from the seed-images folder
  
  // Optional
  "instructions" => "Recommended size 1920×1080", // description under the field
];
```

### WYSIWYG (rich text)

```php
return [
  "type" => "wysiwyg",
  "key" => "field_privacy_policy_content", // unique field key across the whole site, prefixed with field_
  "label" => "Текст", // human-readable label shown in the admin panel
  "name" => "privacy_policy_content", // selector used to read the value: theme_field("privacy_policy_content")
  
  // Optional
  "default_value" => "<p>We respect your privacy...</p>", // default HTML value
];
```

### Number

```php
return [
  "type" => "number",
  "key" => "field_testimonials_rating", // unique field key across the whole site, prefixed with field_
  "label" => "Rating (1-5)", // human-readable label shown in the admin panel
  "name" => "rating", // selector used to read the value: theme_field("rating")
  
  // Optional
  "default_value" => 5, // default value
  "min" => 1, // minimum allowed value
  "max" => 5, // maximum allowed value
  "step" => "0.05", // increment step
];
```

### Email

```php
return [
  "type" => "email",
  "key" => "field_contact_email", // unique field key across the whole site, prefixed with field_
  "label" => "Email", // human-readable label shown in the admin panel
  "name" => "contact_email", // selector used to read the value: theme_field("contact_email")
  "default_value" => "example@gmail.com", // default value
];
```

### URL

```php
return [
  "type" => "url",
  "key" => "field_contact_instagram", // unique field key across the whole site, prefixed with field_
  "label" => "Instagram", // human-readable label shown in the admin panel
  "name" => "contact_instagram", // selector used to read the value: theme_field("contact_instagram")
  "default_value" => "#", // default value
];
```

### Repeater (list of repeating items)

```php
return [
  "type" => "repeater",
  "key" => "field_faq_items", // unique field key across the whole site, prefixed with field_
  "label" => "Питання та відповіді", // human-readable label shown in the admin panel
  "name" => "faq_items", // selector used to read the value: theme_field("faq_items")
  "layout" => "block", // row layout: "table", "block" or "row"
  "button_label" => "Add a question", // text of the "add row" button
  "seed" => $faq_defaults, // theme extension: array of default rows for seeding
  "sub_fields" => [ // fields inside each row (any of the types above)
    ["key" => "field_faq_question", "label" => "Question", "name" => "question", "type" => "text"],
    ["key" => "field_faq_answer", "label" => "Answer", "name" => "answer", "type" => "textarea"],
  ],
];
```

### Gallery (set of images)

```php
return [
  "type" => "gallery",
  "key" => "field_work_gallery", // unique field key across the whole site, prefixed with field_
  "label" => "Gallery of work", // human-readable label shown in the admin panel
  "name" => "work_gallery", // selector used to read the value: theme_field("work_gallery")
  "return_format" => "url", // format of each returned image: "url", "array" or "id"
];
```

### Select (dropdown)

```php
return [
  "type" => "select",
  "key" => "field_layout_variant", // unique field key across the whole site, prefixed with field_
  "label" => "Layout variant", // human-readable label shown in the admin panel
  "name" => "layout_variant", // selector used to read the value: theme_field("layout_variant")
  "choices" => ["grid" => "Grid", "list" => "List"], // options in the value => label format
  "default_value" => "grid", // default value
];
```

### True/False (toggle)

```php
return [
  "type" => "true_false",
  "key" => "field_show_section", // unique field key across the whole site, prefixed with field_
  "label" => "Show section", // human-readable label shown in the admin panel
  "name" => "show_section", // selector used to read the value: theme_field("show_section")
  "ui" => 1, // show as a switch instead of a checkbox
  "default_value" => 1, // default value
];
```

### Link (link with text)

```php
return [
  "type" => "link",
  "key" => "field_hero_cta", // unique field key across the whole site, prefixed with field_
  "label" => "CTA", // human-readable label shown in the admin panel
  "name" => "hero_cta", // selector used to read the value: theme_field("hero_cta")
  "return_format" => "array", // returns an array with the "url", "title" and "target" keys
];
```