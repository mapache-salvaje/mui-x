---
productId: x-tree-view
title: Tree View - Quickstart
components: SimpleTreeView, RichTreeView, TreeItem
packageName: '@mui/x-tree-view'
githubLabel: 'component: tree view'
waiAria: https://www.w3.org/WAI/ARIA/apg/patterns/treeview/
---

# Tree View - Quickstart

<p class="description">Get started with the Tree View. Install the package, configure your application and start using the components.</p>

## Installation

Using your favorite package manager, install `@mui/x-tree-view-pro` for the commercial version, or `@mui/x-tree-view` for the free community version.

<!-- #default-branch-switch -->

{{"component": "modules/components/TreeViewInstallationInstructions.js"}}

### Peer dependencies

#### Material UI

The Tree View package has a peer dependency on `@mui/material`.
If you are not already using it in your project, you can install it with:

<codeblock storageKey="package-manager">

```bash npm
npm install @mui/material @emotion/react @emotion/styled
```

```bash pnpm
pnpm add @mui/material @emotion/react @emotion/styled
```

```bash yarn
yarn add @mui/material @emotion/react @emotion/styled
```

</codeblock>

#### React

<!-- #react-peer-version -->

Please note that [react](https://www.npmjs.com/package/react) and [react-dom](https://www.npmjs.com/package/react-dom) are peer dependencies too:

```json
"peerDependencies": {
  "react": "^17.0.0 || ^18.0.0 || ^19.0.0",
  "react-dom": "^17.0.0 || ^18.0.0 || ^19.0.0"
},
```

## Quickstart

## Accessibility

(WAI-ARIA: https://www.w3.org/WAI/ARIA/apg/patterns/treeview/)

The component follows the WAI-ARIA authoring practices.

To have an accessible Tree View you must use `aria-labelledby` or `aria-label` to reference or provide a label on the TreeView, otherwise, screen readers will announce it as "tree", making it hard to understand the context of a specific tree item.

## Render the Tree View

To make sure that everything is set up correctly, try rendering a Simple Tree View component:

{{"demo": "FirstComponent.js"}}

## TypeScript

### Theme augmentation

In order to benefit from the [CSS overrides](/material-ui/customization/theme-components/#theme-style-overrides) and [default prop customization](/material-ui/customization/theme-components/#theme-default-props) with the theme, TypeScript users need to import the following types.
Internally, it uses module augmentation to extend the default theme structure.

```tsx
import type {} from '@mui/x-tree-view/themeAugmentation';

const theme = createTheme({
  components: {
    MuiRichTreeView: {
      styleOverrides: {
        root: {
          backgroundColor: 'red',
        },
      },
    },
  },
});
```
