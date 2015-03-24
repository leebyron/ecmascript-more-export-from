# Additonal export-from statements in ES7

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
`export var v;`                 | `null`            | `null`         | `"v"`         | `"v"`
`export default function f(){};`| `null`            | `null`         | `"f"`         | `"default"`
`export default function(){};`  | `null`            | `null`         | `"*default*"` | `"default"`
`export default 42;`            | `null`            | `null`         | `"*default*"` | `"default"`
`export {x}`;                   | `null`            | `null`         | `"x"`         | `"x"`
`export {x as v}`;              | `null`            | `null`         | `"x"`         | `"v"`
`export {x} from "mod"`;        | `"mod"`           | `"x"`          | `null`        | `"x"`
`export {x as v} from "mod"`;   | `"mod"`           | `"x"`          | `null`        | `"v"`
`export * from "mod"`;          | `"mod"`           | `"*"`          | `null`        | `null`


### Proposed additions:

Export Statement Form           | [[ModuleRequest]] | [[ImportName]] | [[LocalName]] | [[ExportName]]
---------------------           | ----------------- | -------------- | ------------- | --------------
`export v from "mod";`          | `"mod"`           | `"default"`    | `null`        | `"v"`
`export * as ns from "mod";`    | `"mod"`           | `"*"`          | `null`        | `"ns"`


### Common relationship between import and export

There's a common pattern of the export-from statements in that they mirror a
similar import statement, but with the only difference being not altering local
scope.

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
// proposed: export v from "mod";
import v from "mod";
export {v};
```

```js
// proposed: export * as ns from "mod";
import * as ns from "mod";
export {ns};
```

Using the terminology of Table 42, the export-from form can be created from the
import-then-export form by setting export-from's **[[ExportName]]** to import's
**[[LocalName]]** and export-from's **[[LocalName]]** to **null**.

#### Table showing relationship

Statement Form                          | [[ModuleRequest]] | [[ImportName]] | [[LocalName]] | [[ExportName]]
--------------                          | ----------------- | -------------- | ------------- | --------------
`import v from "mod";`                  | `"mod"`           | `"default"`    | `"v"`         |
<ins>`export v from "mod";`</ins>       | `"mod"`           | `"default"`    | `null`        | `"v"`
`import * as ns from "mod";`            | `"mod"`           | `"*"`          | `"ns"`        |
<ins>`export * as ns from "mod";`</ins> | `"mod"`           | `"*"`          | `null`        | `"ns"`
`import {x} from "mod";`                | `"mod"`           | `"x"`          | `"x"`         |
`export {x} from "mod";`                | `"mod"`           | `"x"`          | `null`        | `"x"`
`import {x as v} from "mod";`           | `"mod"`           | `"x"`          | `"v"`         |
`export {x as v} from "mod";`           | `"mod"`           | `"x"`          | `null`        | `"v"`
<del>`import * from "mod"`</del>        | `"mod"`           | `null`         | `null` (many) |
`export * from "mod";`                  | `"mod"`           | `"*"`          | `null`        | `null` (many)
