---
name: obsidian-plugin
kind: framework
coverage: [1]
---
# Obsidian plugin standards

## Base (researched at Obsidian API 1.14, 2026-10-22)

### Project setup
- Start from `obsidianmd/obsidian-sample-plugin`: esbuild bundle to `main.js` (CJS), `manifest.json`, `versions.json`, `version-bump.mjs`, `.npmrc` with `tag-version-prefix=""`, and `eslint-plugin-obsidianmd`'s recommended config. Source: https://github.com/obsidianmd/obsidian-sample-plugin
- Keep `obsidian`, `electron`, `@codemirror/*`, `@lezer/*` and Node builtins external; Obsidian supplies them at runtime. Match `@codemirror/state`/`@codemirror/view` devDependency versions to the `obsidian` package's peerDependencies. Source: same (esbuild.config.mjs); `npm view obsidian peerDependencies`
- Rename the sample's placeholder names (`MyPlugin`, `SampleSettingTab`) and organize multi-file code into folders. Source: https://docs.obsidian.md/Plugins/Releasing/Plugin+guidelines

### General and resource management
- Use `this.app`, never the global `app`/`window.app`. Source: Plugin guidelines (above)
- Register everything through the component so unload cleans up: `registerEvent`, `registerDomEvent`, `registerInterval`, `addCommand`, `register(() => ...)`. Don't detach leaves in `onunload`. Source: same
- Default console output is errors only; no routine logging. Source: same

### Vault and files
- Background-file edits go through `app.vault.process(file, fn)` (atomic); the active note through the `Editor` API, not `Vault.modify`. Source: same
- Frontmatter edits go through `app.fileManager.processFrontMatter`, never hand-parsed YAML. Source: same
- Look files up with `getFileByPath`/`getFolderByPath`/`getAbstractFileByPath`, not by scanning `getFiles()`. Run constructed paths through `normalizePath()`. Prefer the Vault API over the Adapter API. Source: same

### Workspace and commands
- Use `workspace.getActiveViewOfType()` / `workspace.activeEditor?.editor`, never `workspace.activeLeaf`. Don't keep view references from a `registerView` factory; use `getLeavesOfType()`. Source: same
- Don't set default hotkeys. Use `callback`, `checkCallback`, `editorCallback` or `editorCheckCallback` as fits. Source: same

### UI, DOM and styling
- Never use `innerHTML`/`outerHTML`/`insertAdjacentHTML` with user content; build with `createEl`/`createDiv`/`createSpan` and clear with `el.empty()`. Source: same
- UI text is sentence case. Settings headings via `new Setting(el).setName(...).setHeading()`, no top-level heading for a single section, no word "settings" in headings. Source: same
- No hardcoded inline styles; use plugin CSS classes and Obsidian CSS variables so themes can override. Source: same
- Mobile-capable plugins avoid Node/Electron APIs and regex lookbehind. Source: same

### Bases views (1.10+)
- Register with `this.registerBasesView(id, { name, icon, factory, options })`; it returns `false` when Bases is disabled. Source: https://docs.obsidian.md/Reference/TypeScript+API/Plugin/registerBasesView
- Extend `BasesView`, call `super(controller)`, and render in `onDataUpdated()`, which fires on data and config changes. Don't keep references to `this.data` or its `BasesEntry` objects across updates; both are recreated. Source: obsidian.d.ts (`BasesView.data`); https://docs.obsidian.md/plugins/guides/bases-view
- Read view options with `this.config.get(key)` with a fallback; column order from `this.config.getOrder()`; values with `entry.getValue(propId)`; `parsePropertyId` splits `note`/`formula`/`file`. Source: bases-view guide
- Expect thousands of entries (an unfiltered base is the whole vault): reuse DOM across updates and avoid rendering off-screen rows. Source: same
- Open notes with `workspace.openLinkText` honoring `Keymap.isModEvent`; native hover preview by triggering `'hover-link'` with a `HoverParent`. Source: same

### Development
- Develop and test against a separate throwaway vault, never the main vault. Source: same
