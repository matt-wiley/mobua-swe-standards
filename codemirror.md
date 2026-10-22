---
name: codemirror
kind: framework
coverage: [6]
---
# CodeMirror standards

## Base (researched at @codemirror/state 6.7 / view 6.43, 2026-10-22)

- Treat `EditorState` and `Text` as immutable. Change anything by building a transaction with `state.update(spec)` and applying it with `view.dispatch`. Source: https://codemirror.net/docs/guide/
- Batch related changes into one transaction (several specs to one `update`/`dispatch`); positions in `changes` refer to the start document, selections and effects to the new one. Track positions across edits with `mapPos`. Source: same
- Mark transaction origin with annotations (`Transaction.userEvent`, `Transaction.addToHistory`) so extensions and listeners can tell user edits from programmatic ones. Source: same
- Never dispatch from inside an update (re-entrant dispatch throws). Route external changes (e.g. a file changed on disk) through a normal dispatched transaction, outside update listeners. Source: same
- To load a different document, create a new `EditorState` (`view.setState`) instead of dispatching a replace, so undo history and other per-document state reset. Source: same
- Reconfigure with `Compartment.reconfigure` or `StateEffect.reconfigure`/`appendConfig`, never by rebuilding the view. Source: same
- Data that belongs to the document state lives in a `StateField`; imperative DOM and event work lives in a `ViewPlugin`, which holds no non-derived state and undoes its changes in `destroy`. Source: same
- Use facets as configuration points, keep private config in module-private facets, and return the same extension instances from constructor functions so deduplication works. Source: same
- Never touch DOM the view manages; change presentation with decorations (mark, widget, replace, line) through the `decorations` facet. Source: same
- Always call `view.destroy()` when the editor is removed. Source: same
