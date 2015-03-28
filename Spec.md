> Note: This document is written as a delta against the ES6 RC4
> specification. Headers illustrate the location in the specification. The use
> of <ins>ins</ins> and <del>del</del> illustrate addition and removal to
> existing sections. Shaded blocks followed by content indicates an existing
> portion of content to be replaced by the followed content. Unadorned content
> is new or contextually unalterned. Omitted content should be assumed
> irrelevant to this proposal unless otherwise noted.

---

# 15 ECMAScript Language: Scripts and Modules

## 15.2 Modules

### 15.2.1 Module Semantics

#### 15.2.1.15 Source Text Module Records

**Table 42 (Informative) — Export Forms Mappings to ExportEntry Records**

Export Statement Form                   | [[ExportName]]    | [[ModuleRequest]] | [[ImportName]]        | [[LocalName]]
---------------------                   | --------------    | ----------------- | --------------        | -------------
`export var v;`                         | `"v"`             | **null**          | **null**              | `"v"`
`export default function f(){};`        | `"default"`       | **null**          | **null**              | `"f"`
`export default function(){};`          | `"default"`       | **null**          | **null**              | `"*default*"`
`export default 42;`                    | `"default"`       | **null**          | **null**              | `"*default*"`
`export {x}`;                           | `"x"`             | **null**          | **null**              | `"x"`
`export {v as x}`;                      | `"x"`             | **null**          | **null**              | `"v"`
<ins>`export v from "mod";`</ins>       | <ins>`"v"`</ins>  | <ins>`"mod"`</ins>| <ins>`"default"`</ins>| <ins>**null**</ins>
<ins>`export * as ns from "mod";`</ins> | <ins>`"ns"`</ins> | <ins>`"mod"`</ins>| <ins>`"*"`</ins>      | <ins>**null**</ins>
`export {x} from "mod"`;                | `"x"`             | `"mod"`           | `"x"`                 | **null**
`export {v as x} from "mod"`;           | `"x"`             | `"mod"`           | `"v"`                 | **null**
`export * from "mod"`;                  | **null**          | `"mod"`           | `"*"`                 | **null**


### 15.2.3 Exports

##### Syntax

> Note: *ExportClause* has been renamed to *NamedExports* for clarity
> and symmetry with *NamedImports*. This rename should be reflected throughout
> the entire ECMA spec document, which this delta document does not fully cover.

*ExportDeclaration* :
  - <del>`export` `*` *FromClause* `;`</del>
  - <del>`export` *ExportClause* *FromClause* `;`</del>
  - <del>`export` *ExportClause* `;`</del>
  - <ins>`export` *ExportFromClause* *FromClause* `;`</ins>
  - <ins>`export` *NamedExports* `;`</ins>
  - `export` *VariableStatement*
  - `export` *Declaration*
  - `export` `default` *HoistableDeclaration*<sub>[Default]</sub>
  - `export` `default` *ClassDeclaration*<sub>[Default]</sub>
  - `export` `default` [lookahead ∉ {`function`, `class`<ins>, `from`</ins>}] *AssignmentExpression*<sub>[In]</sub> `;`

> Note: Open to feedback on how to disambiguate `export default from` between
> *ExportFromClause* and `default` *AssignmentExpression*.


*ExportFromClause* :
  - `*`
  - *ExportedDefaultBinding*
  - *NameSpaceExport*
  - *NamedExports*
  - *ExportedDefaultBinding* `,` *NameSpaceExport*
  - *ExportedDefaultBinding* `,` *NamedExports*

*ExportedDefaultBinding* :
  - *IdentifierName*

*NameSpaceExport* :
  - `*` `as` *IdentifierName*

*<del>ExportClause</del><ins>NamedExports</ins>* :

  - `{` `}`
  - `{` *ExportsList* `}`
  - `{` *ExportsList* `,` `}`

*ExportsList* :
  - *ExportSpecifier*
  - *ExportsList* `,` *ExportSpecifier*

*ExportSpecifier* :
  - *IdentifierName*
  - *IdentifierName* `as` *IdentifierName*


#### 15.2.3.2 Static Semantics: BoundNames

*ExportDeclaration* :
  - <del>`export` `*` *FromClause* `;`</del>
  - <del>`export` *ExportClause* *FromClause* `;`</del>
  - <del>`export` *ExportClause* `;`</del>
  - <ins>`export` *ExportFromClause* *FromClause* `;`</ins>
  - <ins>`export` *NamedExports* `;`</ins>

1. Return a new empty List.


#### 15.2.3.3 Static Semantics: ExportedBindings

*ExportDeclaration* :
  - <del>`export` *ExportClause* *FromClause* `;`</del>
  - <del>`export` `*` *FromClause* `;`</del>
  - <ins>`export` *ExportFromClause* *FromClause* `;`</ins>

1. Return a new empty List.


#### 15.2.3.4 Static Semantics: ExportedNames

> *ExportDeclaration* : `export` `*` *FromClause* `;`
>
> 1. Return a new empty List.
>
> *ExportDeclaration* :
>   - `export` *ExportClause* *FromClause* `;`
>   - `export` *ExportClause* `;`
>
> 1. Return the ExportedNames of *ExportClause*.

*ExportDeclaration* : `export` *ExportFromClause* *FromClause* `;`

1. Return the ExportedNames of *ExportFromClause*.

*ExportFromClause* : `*`

1. Return a new empty List.

*ExportFromClause* : *ExportedDefaultBinding* `,` *NameSpaceExport*

1. Let *names* be the ExportedNames of *ExportedDefaultBinding*.
2. Append to *names* the elements of the ExportedNames of *NameSpaceExport*.
3. Return *names*.

*ExportFromClause* : *ExportedDefaultBinding* `,` *NamedExports*

1. Let *names* be the ExportedNames of *ExportedDefaultBinding*.
2. Append to *names* the elements of the ExportedNames of *NamedExports*.
3. Return *names*.


#### 15.2.3.5 Static Semantics: ExportEntries

> *ExportDeclaration* : `export` `*` *FromClause* `;`
>
> 1. Let *module* be the sole element of ModuleRequests of *FromClause*.
> 2. Let *entry* be the Record {[[ModuleRequest]]: *module*, [[ImportName]]: **"*"**, [[LocalName]]: **null**, [[ExportName]]: **null** }.
> 3. Return a new List containing *entry*.
>
> *ExportDeclaration* : `export` *ExportClause* *FromClause* `;`
>
> 1. Let *module* be the sole element of ModuleRequests of *FromClause*.
> 2. Return ExportEntriesForModule of *ExportClause* with argument *module*.

*ExportDeclaration* : `export` *ExportFromClause* *FromClause* `;`

1. Let *module* be the sole element of ModuleRequests of *FromClause*.
2. Return ExportEntriesForModule of *ExportFromClause* with argument *module*.


#### 15.2.3.6 Static Semantics: ExportEntriesForModule

*ExportFromClause* : `*`

1. Return a new List containing the Record {[[ModuleRequest]]: *module*, [[ImportName]]: **"*"**, [[LocalName]]: **null**, [[ExportName]]: **null** }.

*ExportFromClause* : *ExportedDefaultBinding* `,` *NameSpaceExport*

1. Let *entries* be ExportEntriesForModule of *ExportedDefaultBinding* with argument *module*.
2. Append to *entries* the elements of the ExportEntriesForModule of *NameSpaceExport* with argument *module*.
3. Return *entries*.

*ExportFromClause* : *ExportedDefaultBinding* `,` *NamedExports*

1. Let *entries* be ExportEntriesForModule of *ExportedDefaultBinding* with argument *module*.
2. Append to *entries* the elements of the ExportEntriesForModule of *NamedExports* with argument *module*.
3. Return *entries*.

*ExportedDefaultBinding* : *IdentifierName*

1. Let *exportName* be the StringValue of *IdentifierName*.
2. Return a new List containing the Record {[[ModuleRequest]]: *module*, [[ImportName]]: **"default"**, [[LocalName]]: **null**, [[ExportName]]: *exportName* }.

*NameSpaceExport* : `*` `as` *IdentifierName*

1. Let *exportName* be the StringValue of *IdentifierName*.
2. Return a new List containing the Record {[[ModuleRequest]]: *module*, [[ImportName]]: **"*"**, [[LocalName]]: **null**, [[ExportName]]: *exportName* }.


#### 15.2.3.7 Static Semantics: IsConstantDeclaration

*ExportDeclaration* :
  - <del>`export` `*` *FromClause* `;`</del>
  - <del>`export` *ExportClause* *FromClause* `;`</del>
  - <del>`export` *ExportClause* `;`</del>
  - <ins>`export` *ExportFromClause* *FromClause* `;`</ins>
  - <ins>`export` *NamedExports* `;`</ins>
  - `export` `default` *AssignmentExpression* `;`

1. Return false.


#### 15.2.3.8 Static Semantics: LexicallyScopedDeclarations

*ExportDeclaration* :
  - <del>`export` `*` *FromClause* `;`</del>
  - <del>`export` *ExportClause* *FromClause* `;`</del>
  - <del>`export` *ExportClause* `;`</del>
  - <ins>`export` *ExportFromClause* *FromClause* `;`</ins>
  - <ins>`export` *NamedExports* `;`</ins>
  - `export` *VariableStatement*

1. Return a new empty List.


#### 15.2.3.9 Static Semantics: ModuleRequests

*ExportDeclaration* :
  - <del>`export` `*` *FromClause* `;`</del>
  - <del>`export` *ExportClause* *FromClause* `;`</del>
  - <ins>`export` *ExportFromClause* *FromClause* `;`</ins>

1. Return the ModuleRequests of *FromClause*.


#### 15.2.3.11 Runtime Semantics: Evaluation

*ExportDeclaration* :
  - <del>`export` `*` *FromClause* `;`</del>
  - <del>`export` *ExportClause* *FromClause* `;`</del>
  - <del>`export` *ExportClause* `;`</del>
  - <ins>`export` *ExportFromClause* *FromClause* `;`</ins>
  - <ins>`export` *NamedExports* `;`</ins>

1. Return NormalCompletion(empty).


# Annex A (informative) Grammar Summary

## A.5 Scripts and Modules

> Note: See alterations in 15.2.3.
