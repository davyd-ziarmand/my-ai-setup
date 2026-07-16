# Basic utilities

The site has basic utilities without which it will not work properly, because of the structure described in the [architecture.md](./architecture.md) file.

## Helpers

### Import helper

`src/shared/helpers/import-functions.php`

This helper is needed so that you can conveniently import a page/section or widget/icon from a feature.

### Helper for working with SCF custom fields

`src/shared/helpers/scf.php`

This helper is needed for reading custom fields at any point in the theme via `theme_field()`.
It also adds a guard for the case when the SCF plugin is not installed.
It also has functions for customizing the admin panel sidebar, to add there an item in Ukrainian "Site Content" with
a breakdown by pages (the list of pages can be edited, depends on the project).

### Helper for working with the site menu

`src/shared/helpers/get-website-menu.php`

This helper is needed for getting the links and names of the site menu items. The menu itself can be edited in the admin panel in
Appearance -> Customize -> Add/Edit menu.

### Seeding helper

`src/shared/helpers/seed.php`

This helper is needed to create fields and fill the site with default values.
For sections in features, the default values are taken from the `*.fields.php` files and from the `seed-images` folders, the same for widgets.

## API utilities

### Forms

`src/shared/api/forms.php`
`src/shared/api/forms.js`

This utility is needed for all custom forms on the site that can send AJAX.

In the `forms.php` file, the message is validated, sanitized and sent to the `admin_email`, which is registered
on `wp_ajax_send_contact_form` and `wp_ajax_nopriv_send_contact_form`.

In the `forms.js` file, an attribute is bound to each `[data-website-form]` element, which sends
a request to `window.WEBSITE_FORMS_AJAX.url` with a "nonce" field `window.WEBSITE_FORMS_AJAX.nonce`, then the loading
state is handled and a success or error message is shown via the `[data-form-message]` attribute.

The script handle `website-forms` and the global variable `WEBSITE_FORMS_AJAX` are registered in the `functions.php` file.
When a widget or section needs a form handler, you need to pass `"website-forms"` through the `$deps` dependencies,
for example `widget("contact-us", [], ["website-forms"])`.