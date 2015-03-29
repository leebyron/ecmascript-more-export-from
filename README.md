# Additonal export-from statements in ES7

## ES7 Proposal

**Stage:** 1

**Author:** Lee Byron

**Specification:** [Spec.md](./Spec.md)

**AST:** [ESTree.md](./ESTree.md)

**Transpiler:** See Babel's `es7.exportExtensions` option.

## Problem statement and rationale

The `export ___ from "module"` statements are a very useful mechanism for
building up "package" modules in a declarative way. In the ES6 spec, we can:

* export through a single export with `export {x} from "mod"`
* ...optionally renaming it with `export {x as v} from "mod"`
* We can also spread all exports with `export * from "mod"`.

These three export-from statements are easy to understand if you understand the
semantics of the similar looking import statements.

However, there are other import statements which would have very useful
export-from forms.

* Forwarding the *default* export of the referenced module
as a named export of this module: `export v from "mod"`
* Exporting the ModuleNameSpace object as a named export: `export * as ns from "mod"`


### Current ES6 Modules:

Import Statement Form         | [[ModuleRequest]] | [[ImportName]] | [[LocalName]]
---------------------         | ----------------- | -------------- | -------------
`import v from "mod";`        | `"mod"`           | `"default"`    | `"v"`
`import * as ns from "mod";`  | `"mod"`           | `"*"`          | `"ns"`
`import {x} from "mod";`      | `"mod"`           | `"x"`          | `"x"`
`import {x as v} from "mod";` | `"mod"`           | `"x"`          | `"v"`


Export Statement Form           | [[ModuleRequest]] | [[ImportName]] | [[LocalName]] | [[ExportName]]
---------------------           | ----------------- | -------------- | ------------- | --------------
`export var v;`                 | **null**          | **null**       | `"v"`         | `"v"`
`export default function f(){};`| **null**          | **null**       | `"f"`         | `"default"`
`export default function(){};`  | **null**          | **null**       | `"*default*"` | `"default"`
`export default 42;`            | **null**          | **null**       | `"*default*"` | `"default"`
`export {x}`;                   | **null**          | **null**       | `"x"`         | `"x"`
`export {x as v}`;              | **null**          | **null**       | `"x"`         | `"v"`
`export {x} from "mod"`;        | `"mod"`           | `"x"`          | **null**      | `"x"`
`export {x as v} from "mod"`;   | `"mod"`           | `"x"`          | **null**      | `"v"`
`export * from "mod"`;          | `"mod"`           | `"*"`          | **null**      | **null**


### Proposed additions:

Export Statement Form           | [[ModuleRequest]] | [[ImportName]] | [[LocalName]] | [[ExportName]]
---------------------           | ----------------- | -------------- | ------------- | --------------
`export v from "mod";`          | `"mod"`           | `"default"`    | **null**      | `"v"`
`export * as ns from "mod";`    | `"mod"`           | `"*"`          | **null**      | `"ns"`


### Symmetry between import and export

There's a symmetry between the export-from statements and the import statements
they resemble. The semantics of the export-from statements are almost identical
to their symmetric import statement followed by a `export {x}`, but with the
critical difference of not altering local scope.

As an example:

```js
export {v} from "mod";
```

Is very similar to:

```js
import {v} from "mod";
export {v};
```

However, `v` is not introduced as a name in the local scope.

The two proposed additions follow this same pattern:

```js
// proposed:
export v from "mod";
// symmetric to:
import v from "mod";
export {v};
```

```js
// proposed:
export * as ns from "mod";
// symmetric to:
import * as ns from "mod";
export {ns};
```

Using the terminology of Table 42 in ECMA-262 v6 RC4, the export-from form can
be created from the symmetric import form by setting export-from's
**[[ExportName]]** to import's **[[LocalName]]** and export-from's
**[[LocalName]]** to **null**.

#### Table showing symmetry

Statement Form                          | [[ModuleRequest]] | [[ImportName]] | [[LocalName]] | [[ExportName]]
--------------                          | ----------------- | -------------- | ------------- | --------------
`import v from "mod";`                  | `"mod"`           | `"default"`    | `"v"`         |
<ins>`export v from "mod";`</ins>       | `"mod"`           | `"default"`    | **null**      | `"v"`
`import * as ns from "mod";`            | `"mod"`           | `"*"`          | `"ns"`        |
<ins>`export * as ns from "mod";`</ins> | `"mod"`           | `"*"`          | **null**      | `"ns"`
`import {x} from "mod";`                | `"mod"`           | `"x"`          | `"x"`         |
`export {x} from "mod";`                | `"mod"`           | `"x"`          | **null**      | `"x"`
`import {x as v} from "mod";`           | `"mod"`           | `"x"`          | `"v"`         |
`export {x as v} from "mod";`           | `"mod"`           | `"x"`          | **null**      | `"v"`
<del>`import * from "mod"`</del>        | `"mod"`           | **null**       | **null** (many)|
`export * from "mod";`                  | `"mod"`           | `"*"`          | **null**      | **null** (many)

> Note: `import * from "mod"` is only included for illustrative purposes. It is
> not part of any spec or proposal.

### Compound export-from statements

This proposal also includes the symmetric export-from for the compound imports:

```js
// proposed
export v, {x, y as w} from "mod"
// symmetric to
import v, {x, y as w} from "mod"
```

As well as

```js
// proposed
export v, * as ns from "mod"
// symmetric to
import v, * as ns from "mod"
```

### Export default from

One use case is to take the default export of an inner module and export it as
the default export of the outer module. This is written as:

```js
export default from "mod";
```

This is *not* additional syntax above what's already proposed. In fact,
this is just the `export v from "mod"` syntax where the export name happens to
be `default`. This nicely mirrors the other `export default ____` forms in both
syntax and semantics without requiring additional specification.
