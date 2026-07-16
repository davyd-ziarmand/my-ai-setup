# Code Styles

## General

1. Do not write any comments in code, unless requested by user. Comments must be in English only.
2. File names should be lowercase kebab-case like "file-name.ts". 
3. Classes should be in PascalCase like "MyClassName"

## TypeScript rules

1. Prefer `type` over `interface`, unless functions or methods are required, then use `interface`.
2. Inline types or interfaces if they have <= 5 keys.
3. Functions, variables, etc. in camelCase, e.g. `myVariable` or `const myFunction = () => {}`.
4. Prefer arrow functions.
5. Imports: relative paths are allowed within the current folder and its subfolders (`./file`, `./subfolder/file`); anything further (`../file`, `../folder/...`, `../../...`) must use the `@/` alias (enforced by `no-restricted-imports` in eslint)
6. Barrel-file should be ".tsx" for components or rarely for hooks, for other logic use ".ts".

## TypeScript prettier setup

This is minimum allowed setup for all TypeScript projects:
```json
{
  "printWidth": 120,
  "tabWidth": 2,
  "useTabs": false,
  "singleQuote": false,
  "semi": false,
  "trailingComma": "es5",
}
```

