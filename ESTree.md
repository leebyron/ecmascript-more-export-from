This document specifies the extensions to the [ESTree ES6 AST](https://github.com/estree/estree/blob/master/es6.md)
types to support this module export extensions proposal.


# Modules

## ExportNamedDeclaration

```js
extend interface ExportNamedDeclaration {
    specifiers: [ ExportSpecifier | ExportDefaultSpecifier | ExportNamespaceSpecifier ];
}
```

Extends the [ExportNamedDeclaration](https://github.com/estree/estree/blob/master/es6.md#exportnameddeclaration),
e.g., `export {foo} from "mod";` to allow two new types of specifiers:
*ExportDefaultSpecifier* and *ExportNamespaceSpecifier*.

_Note: When `source` is `null`, having `specifiers` include either
*ExportDefaultSpecifier* or *ExportNamespaceSpecifier* results in an invalid state._

_Note: Having `specifiers` include more than one of either
*ExportDefaultSpecifier* or *ExportNamespaceSpecifier* results in an invalid state._

_Note: Having `specifiers` include both *ExportNamespaceSpecifier* and
*ExportSpecifier* results in an invalid state._



## ExportDefaultSpecifier

```js
interface ExportDefaultSpecifier <: Node {
    type: "ExportDefaultSpecifier";
    exported: Identifier;
}
```

An exported binding `foo` in `export foo from "mod";`. The `exported` field
refers to the name exported in this module. That name is bound to the
`"default"` export from the `source` of the parent *ExportNamedDeclaration*.


## ExportNamespaceSpecifier

```js
interface ExportNamespaceSpecifier <: Node {
    type: "ExportNamespaceSpecifier";
    exported: Identifier;
}
```

An exported binding `* as ns` in `export * as ns from "mod";`. The `exported`
field refers to the name exported in this module. That name is bound to the
ModuleNameSpace exotic object from the `source` of the parent *ExportNamedDeclaration*.
