# Feature-Driven-Development Architecture

All code must live in the /src/ folder at the project root. The /src/ folder must contain only the layer folders described here: ./features and ./shared. As a rare exception, other files may exist here too, for example payload.config.ts or payload-types.ts, or even folders like /src/admin.

## Main layer: features

Folder /src/features/. 

This folder has a separate subfolder for each feature: /src/features/feature-name-1. Each feature must be isolated and cannot import from another feature, but can import from the shared layer. Each feature must have an index.ts barrel file that exports only what is used externally: components, functions, etc.
Each feature has the following structure:

`./index.ts`
`./components` - folder with all components of the feature
`./pages` or `./screens` - folder with all pages or screens of the feature, depending on the platform (web / native)
`./stores` - usually a folder with all stores used in the feature
`./api` - all API-related hooks or server actions, or other API-related functions of the feature
`./hooks` - all custom, non-API-related hooks of the feature
`./utils` - all helper functions that are core to the feature

Also possible:
`./modals` - modal window components that exist within the feature
`./cards` - folder with card components of the feature
`./lib` - if the feature has no components, functions implementing the core functionality of the feature
`./cms` - folder with all CMS field configs, if a CMS is used in the project

Other folders can also exist at the root of the feature if something non-standard needs to be grouped, for example: `./server-component` - if regular and server components need to be separated within a single feature. Before creating such folders, it is better to ask the user.

If a folder has many files that don't group well (only 1-2 files each, for example), it is better to just create a barrel file.

The feature can also have any nested folders inside the ones described above if they contain many files and grouping is needed to improve readability. Example: the components folder has 10 components, 3 of which can be grouped into one folder, 5 into another, and 2 into a third. Before creating such nested folders, it is better to ask the user.

## Public layer: shared

This is a layer of reusable elements that can be imported in features or anywhere else in the app.

### Services

Folder: `./services`

This is a folder with services, each service has the same structure as a feature, but it is essentially a "reusable feature" that can be used in various other features or directly anywhere in the app. Example: /services/auth or /services/status-modal.

### Components

Folder: `./components`

This is a folder with global reusable components. It must have an Atomic Design structure.

`./components/atoms` - folder with the smallest components that are part of the app's design system. Example: button, textfields, typography, dropdown, etc.
`./components/molecules` - folder with simple components composed of several atoms
`./components/organisms` - folder with complex components with complex logic, can be composed of both molecules and atoms.
`./components/templates` - folder with recurring templates for the app, can be composed of atoms, molecules, and even organisms.

### Helpers

`./helpers` - folder with global reusable functions

### Other rare variations

The following folders may also exist, though rarely, only when needed. Ask the user before creating them:

`./constants` - folder with global reusable constants.
`./configs` - folder with global reusable configuration files. Files can be grouped into folders.
`./types` - folder with global reusable types.
`./libs` - folder with 3rd party library files or folders, one folder per library, which can contain several files needed for setup or for creating additional reusable pieces of code or modules depending on the library.

Technically, any folder here can be used to group any reusable logic.
