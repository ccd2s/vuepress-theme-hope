---
title: Sidebar
icon: fas fa-window-maximize fa-rotate-270
order: 2
category:
  - Layout
tag:
  - Layout
  - Sidebar
---

The sidebar contains list of related documents, document titles, and blogger information in blog mode.

<!-- markdownlint-disable MD051 -->

The theme allows you to generate side bar from [file structure](#generate-from-file-structure) or [headers](#generate-from-headers) automatically, or you can [customize](#sidebar-links) it manually.

<!-- markdownlint-enable MD051 -->

<!-- more -->

## Sidebar Links

You should use `sidebar` in theme options to control sidebar.

### String Format

Just like navbar, you can fill in an array of multiple file links as the basic configuration of the sidebar:

```ts twoslash {4,6-7} title=".vuepress/theme.ts"
import { hopeTheme } from "vuepress-theme-hope";

export default hopeTheme({
  sidebar: ["/README.md", "/guide/README.md", "/config/README.md"],

  // equivalent to:
  // sidebar: ["/", "/guide/", "/config/"],
});
```

Each item of the array will be rendered as a sidebar item.

::: tip

You can omit the `.md` extension, and paths ending with `/` are inferred as `/README.md`.

:::

### Object Format

Just like navbar, if you are not satisfied with the page's icon or feel that the page title is too long, you can configure an object instead. Available configuration items are:

- `text:`: item text
- `link`: item link
- `icon`: item icon (optional)
- `activeMatch`: item active math (optional), support regexp strings

```ts twoslash {4-19} title=".vuepress/theme.ts"
import { hopeTheme } from "vuepress-theme-hope";

export default hopeTheme({
  sidebar: [
    {
      text: "Guide",
      link: "/guide/README.md",
      icon: "lightbulb",
    },
    { text: "Config", link: "/config/README.md", icon: "config" },
    {
      text: "FAQ",
      link: "/faq.md",
      icon: "circle-question",
      // active in path starting with `/faq`
      // so it will active in path like `/faq/xxx.html`
      activeMatch: "^/faq/",
    },
  ],
});
```

::: tip Advanced usage of activeMatch

`activeMatch` gives you the ability to control whether the path is active through RegExps.

:::

### Grouping and Nesting

If you need a sidebar that displays a nested structure, you can group similar links.

You should use [object format](#object-format) and provide an additional `children` option to set the list of links.

Like navbar, you can use `prefix` in the sidebar to add a default path prefix to each link in the group.

The sidebar additionally supports setting `collapsible: true` to make the menu group collapsible, and you can se `expanded: true` to make the menu group default expanded.

```ts twoslash title=".vuepress/theme.ts"
import { hopeTheme } from "vuepress-theme-hope";

export default hopeTheme({
  sidebar: [
    {
      // required, title of group
      text: "Group 1",
      // optional, icon of group
      icon: "tip",
      // optional, link of group title
      link: "/foo/",
      // optional, will be appended to each item link
      prefix: "/foo/",
      // optional, defaults to false
      collapsible: true,
      // optional, representing the original state of a collapsible sidebar group,
      // defaults to false
      expanded: true,
      // required, items of group
      children: [
        "README.md" /* /foo/index.html */,
        /* ... */
        "geo.md" /* /foo/geo.html */,
      ],
    },
    {
      text: "Group 2",
      children: [
        "bar.md" /* /ray/bar.html */,
        "baz.md" /* /ray/baz.html */,
        // ...
      ],
    },
  ],
});
```

You can also nest sidebar grouping:

```ts twoslash {11-22} title=".vuepress/theme.ts"
import { hopeTheme } from "vuepress-theme-hope";

export default hopeTheme({
  sidebar: [
    {
      text: "Group",
      prefix: "/",
      children: [
        "baz" /* /baz.html */,
        {
          text: "Sub Group 1",
          children: ["quz" /* /quz.html */, "xyzzy" /* /xyzzy.html */],
        },
        {
          text: "Sub Group 2",
          prefix: "corge/",
          children: [
            "fred" /* /corge/fred.html */,
            "grault" /* /corge/grault.html */,
          ],
        },
        "foo" /* /foo.html */,
      ],
    },
  ],
});
```

You may want to use it with `prefix` to restore the structure of the document easily.

For example, suppose you have a following directory structure:

```
.
├─ README.md
├─ contact.md
├─ about.md
├─ foo/
│   ├─ README.md
│   ├─ one.md
│   └─ two.md
└─ bar/
    ├─ README.md
    ├─ three.md
    └─ four.md
```

Then you can use the following config:

```ts twoslash title=".vuepress/theme.ts"
import { hopeTheme } from "vuepress-theme-hope";

export default hopeTheme({
  sidebar: [
    "/" /* / */,
    {
      text: "Foo",
      prefix: "/foo/",
      children: [
        "" /* /foo/ */,
        "one" /* /foo/one.html */,
        "two" /* /foo/two.html */,
      ],
    },
    {
      text: "Bar",
      prefix: "/bar/",
      children: [
        "" /* /bar/ */,
        "three" /* /bar/three.html */,
        "four" /* /bar/four.html */,
      ],
    },
    "/contact" /* /contact.html */,
    "/about" /* /about.html */,
  ],
});
```

### Multiple Sidebars

To display different sidebars for different page groups, set an object for the sidebar in the format of `path: config`.

For example, if you have the following structure:

```
.
├─ README.md
├─ contact.md
├─ about.md
├─ foo/
│   ├─ README.md
│   ├─ one.md
│   └─ two.md
└─ bar/
    ├─ README.md
    ├─ three.md
    └─ four.md
```

You can define your sidebar for each section using below configuration:

```ts twoslash title=".vuepress/theme.ts"
import { hopeTheme } from "vuepress-theme-hope";

export default hopeTheme({
  sidebar: {
    "/foo/": [
      "" /* /foo/ */,
      "one" /* /foo/one.html */,
      "two" /* /foo/two.html */,
    ],

    "/bar/": [
      "" /* /bar/ */,
      "three" /* /bar/three.html */,
      "four" /* /bar/four.html */,
    ],

    // fallback
    "/": ["" /* / */, "contact" /* /contact.html */, "about" /* /about.html */],
  },
});
```

::: warning

You need to pay special attention to the order of object key declaration. Generally speaking, you should put the more precise path first, because VuePress will traverse the key names of the sidebar configuration to find the matching configuration. Once a key name is successfully matched with the current path, it will display the corresponding sidebar configuration.

In this case, the fallback sidebar must be defined last for this reason.

:::

## Generate Sidebar from File Structure <Badge text="New" type="tip" />

You can replace the original "sidebarConfig array" with `"structure"` keyword in any of the above sidebar config. This will allow the theme to automatically read local files, then generate sidebar from file structure for you, to reduce your config workload.

For example, for the following example mentioned earlier in [multiple sidebars](#multiple-sidebars):

```
.
├─ README.md
├─ contact.md
├─ about.md
├─ foo/
│   ├─ README.md
│   ├─ one.md
│   └─ two.md
└─ bar/
    ├─ README.md
    ├─ three.md
    └─ four.md
```

You can change the original config to:

```ts twoslash {5,7} title=".vuepress/theme.ts"
import { hopeTheme } from "vuepress-theme-hope";

export default hopeTheme({
  sidebar: {
    "/foo/": "structure",

    "/bar/": "structure",

    // fallback
    "/": ["" /* / */, "contact" /* /contact.html */, "about" /* /about.html */],
  },
});
```

In the above modification, since the original sidebar array is all files under the relevant path, you can easily replace it with the `"structure"` keyword.

If you use the structure to generate a folder with other folders nested under it, the corresponding folder will be rendered as a group. So you can even be more aggressive, for example setting `sidebar: "structure"` to have your sidebars all auto-generated from the file structure.

::: warning Limitations

Since structure sidebar is depending on file structure and markdown frontmatter, any changes in markdown may update the structure sidebar. (E.g: setting `index: false` in frontmatter as described below)

However, recalculating the sidebar could be expensive for large sites, so the theme will only recalculate with [`hotReload` enabled](../../config/theme/basic.md#hotreload).

:::

### Advanced Control

During the automatic generation from structure, you can control whether files in the same folder are included through the `index` option in the page Frontmatter, and control how they are sorted through `order`.

When you don't want the page to be included in the sidebar, you need to set `index: false` in Frontmatter.

By default, the sidebar will be sorted according to the current language according to the title text of the file name. You can control how they are sorted by `order`. When you set a positive number, they will appear at the front of the group, the smaller the more forward, when you set a negative number, it will appear at the back of the group, and the larger the more backward:

- page -> order: 1
- page -> order: 2
- page -> order: 3
- ...
- pages with positive `order` will be sorted by `order` here
- ...
- page without `order` option -> title: Axxx
- ...
- pages without `order` option will be sorted by title here
- ...
- page without `order` option -> title: Zxxx
- ...
- pages with negative `order` will be sorted by `order` here
- ...
- page -> order: -3
- page -> order: -2
- page -> order: -1

::: tip

`README.md` is an exception, as long as you don't disable it from the sidebar via `index: false` or make it as group link, it will always be the first item after sorting.

:::

For nested folders, the grouping information is controlled by `README.md` under that folder. You can control the behavior of folder grouping through the `dir` option in Frontmatter. The relevant optional items are as follows:

- `dir.text`: Directory title, default to `README.md` title
- `dir.icon`: Directory icon, default to `README.md` icon
- `dir.collapsible`: Whether the directory is collapsible, default to `true`
- `dir.expanded`: Whether the directory is default expanded, default to `false`
- `dir.link`: Whether the directory is clickable, default to `false`
- `dir.index`: Whether index current dir, default to `true`
- `dir.order`: Dir order in sidebar, default to `0`

Here is an example:

```md
---
dir:
  order: 1
  text: Group 1
---
```

If no `README.md` file exists for the corresponding folder, only the group header will be generated from the folder name.

#### Customize Sorter

In addition to the above implementation, we also added a more powerful `sidebarSorter` option to the theme options. You can pass one or a series of built-in sorter names, or you can pass a sorter function you need to sort sidebar items at the same level.

Available keywords are:

- `readme`: `README.md` or `readme.md` first
- `order`: positive order first with its value ascending, negative order last with its value descending
- `date`: sort by date ascending
- `date-desc`: sort by date descending
- `title`: alphabetically sort by title
- `filename`: alphabetically sort by filename

Corresponding to the above advanced control, its default value is `["readme", "order", "title", "filename"]`

### Disabling Sidebar

You can disable the sidebar on a specific page via frontmatter:

```md
---
sidebar: false
---
```

::: note

Sidebar is disabled by default in home page.

:::

### Active Header Links

By default, the nested header links and the hash in the URL are updated as the user scrolls to view the different sections of the page. This behavior can be disabled with the following theme config:

```ts twoslash {5} title=".vuepress/theme.ts"
import { hopeTheme } from "vuepress-theme-hope";

export default hopeTheme({
  plugins: {
    activeHeaderLinks: false,
  },
});
```

## I18n Support

The theme's navbar supports [I18n](https://vuejs.press/guide/i18n.html), so you can set sidebar individually in each language:

```ts twoslash {6-8,11-13} title=".vuepress/theme.ts"
import { hopeTheme } from "vuepress-theme-hope";

export default hopeTheme({
  locales: {
    "/": {
      sidebar: [
        /* English config under root */
      ],
    },
    "/zh/": {
      sidebar: [
        /* Chinese config under zh folder */
      ],
    },
  },
});
```

## Types and Helpers

`vuepress-theme-hope` exports the type of sidebar as `SidebarOptions`, and provides a `sidebar` helper function.

::: tip

To deal with the situation when you split [multi-sidebar configuration](#multiple-sidebars) into multiple parts, we also provide `SidebarArrayOptions` `SidebarObjectOptions` type and `arraySidebar` and `objectSidebar` helper functions.

:::

```ts twoslash {6} title=".vuepress/sidebar.ts"
import { sidebar } from "vuepress-theme-hope";

export default sidebar([
  /* Your sidebar configuration */
]);
```

## Demo

::: details Configuration of this documentation

```ts twoslash
import { sidebar } from "vuepress-theme-hope";

<!-- @include: ../../.vuepress/sidebar/en.ts#config -->
```

:::
