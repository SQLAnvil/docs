# Class: IncrementalTable

When you define an incremental table, sqlanvil builds the incremental table from scratch only for
the first time. During subsequent executions, sqlanvil only inserts or merges new rows into the
incremental table according to the conditions that you configure.

You can create incremental tables in the following ways. Available config options are defined in
[IncrementalTableConfig](configs#sqlanvil-ActionConfig-IncrementalTableConfig), and are shared across all the
following ways of creating tables.

**Using a SQLX file:**

```sql
-- definitions/name.sqlx
config {
  type: "incremental"
}
-- This inserts `1` the first time running, and `2` on subsequent runs.
SELECT ${when(incremental(), 1, 2) }
```

<!-- Action configs files are not yet supported, until a new field incrementalFilename is
configured. Overall without JS templating supported in SQL files consumed by actions config
files, the CUJ kind of sucks anyway. -->

**Using the Javascript API:**

```js
// definitions/file.js
publish("name", { type: "incremental" }).query(
  ctx => `SELECT ${ctx.when(ctx.incremental(), 1, 2) }`
)
```

Note: When using the Javascript API, methods in this class can be accessed by the returned value.
This is where `query` comes from.

## Hierarchy

* ActionBuilder‹Table›

  ↳ **IncrementalTable**

## Index

### Methods

* [assertions](#assertions)
* [bigquery](#bigquery)
* [columns](#columns)
* [database](#database)
* [dependencies](#dependencies)
* [description](#description)
* [disabled](#disabled)
* [hermetic](#hermetic)
* [postOps](#postops)
* [preOps](#preops)
* [protected](#protected)
* [query](#query)
* [schema](#schema)
* [setDependOnDependencyAssertions](#setdependondependencyassertions)
* [tags](#tags)
* [type](#type)
* [uniqueKey](#uniquekey)

## Methods

###  assertions

▸ **assertions**(`tableAssertionsConfig`: TableAssertionsConfig): *IncrementalTable*

**`deprecated`** Deprecated in favor of
[IncrementalTableConfig.assertions](configs#sqlanvil-ActionConfig-IncrementalTableConfig).

Sets in-line assertions for this incremental table.

<!-- Note: this both applies in-line assertions, and acts as a method available via the JS API.
Usage of it via the JS API is deprecated, but the way it applies in-line assertions is still
needed -->

**Parameters:**

Name | Type |
------ | ------ |
`tableAssertionsConfig` | TableAssertionsConfig |

**Returns:** *IncrementalTable*

___

###  bigquery

▸ **bigquery**(`bigquery`: IBigQueryOptions): *this*

**`deprecated`** Deprecated in favor of options available directly on
[IncrementalTableConfig](configs#sqlanvil-ActionConfig-IncrementalTableConfig). For example:
`publish("name", { type: "table", partitionBy: "column" }`) for BigQuery, or the `postgres:`
config block for Postgres.

Sets bigquery options for the action.

**Parameters:**

Name | Type |
------ | ------ |
`bigquery` | IBigQueryOptions |

**Returns:** *this*

___

###  columns

▸ **columns**(`columns`: ColumnDescriptor[]): *this*

**`deprecated`** Deprecated in favor of
[IncrementalTableConfig.columns](configs#sqlanvil-ActionConfig-IncrementalTableConfig).

Sets the column descriptors of columns in this incremental table.

**Parameters:**

Name | Type |
------ | ------ |
`columns` | ColumnDescriptor[] |

**Returns:** *this*

___

###  database

▸ **database**(`database`: string): *this*

**`deprecated`** Deprecated in favor of
[IncrementalTableConfig.project](configs#sqlanvil-ActionConfig-IncrementalTableConfig).

Sets the
Sets the database in which to create the output of this action. For BigQuery targets this is
the Google Cloud project ID; for Postgres/Supabase targets this is the database name in
`workflow_settings.yaml`.

**Parameters:**

Name | Type |
------ | ------ |
`database` | string |

**Returns:** *this*

___

###  dependencies

▸ **dependencies**(`value`: Resolvable | Resolvable[]): *this*

**`deprecated`** Deprecated in favor of
[IncrementalTableConfig.dependencies](configs#sqlanvil-ActionConfig-IncrementalTableConfig).

Sets dependencies of the incremental table.

**Parameters:**

Name | Type |
------ | ------ |
`value` | Resolvable &#124; Resolvable[] |

**Returns:** *this*

___

###  description

▸ **description**(`description`: string): *this*

**`deprecated`** Deprecated in favor of
[IncrementalTableConfig.description](configs#sqlanvil-ActionConfig-IncrementalTableConfig).

Sets the description of this incremental table.

**Parameters:**

Name | Type |
------ | ------ |
`description` | string |

**Returns:** *this*

___

###  disabled

▸ **disabled**(`disabled`: boolean): *this*

**`deprecated`** Deprecated in favor of
[IncrementalTableConfig.disabled](configs#sqlanvil-ActionConfig-IncrementalTableConfig).

If called with `true`, this action is not executed. The action can still be depended upon.
Useful for temporarily turning off broken actions.

**Parameters:**

Name | Type | Default |
------ | ------ | ------ |
`disabled` | boolean | true |

**Returns:** *this*

___

###  hermetic

▸ **hermetic**(`hermetic`: boolean): *void*

**`deprecated`** Deprecated in favor of
[IncrementalTableConfig.hermetic](configs#sqlanvil-ActionConfig-IncrementalTableConfig).

If true, this indicates that the action only depends on data from explicitly-declared
dependencies. Otherwise if false, it indicates that the action depends on data from a source
which has not been declared as a dependency.

**Parameters:**

Name | Type |
------ | ------ |
`hermetic` | boolean |

**Returns:** *void*

___

###  postOps

▸ **postOps**(`posts`: Contextable‹ITableContext, string | string[]›): *this*

Sets a post-operation to run after the query is run. This is often used for revoking temporary
permissions granted to access source tables.

Example:

```js
// definitions/file.js
publish("example")
  .preOps(ctx => `GRANT \`roles/bigquery.dataViewer\` ON TABLE ${ctx.ref("other_table")} TO "group:automation@example.com"`)
  .query(ctx => `SELECT * FROM ${ctx.ref("other_table")}`)
  .postOps(ctx => `REVOKE \`roles/bigquery.dataViewer\` ON TABLE ${ctx.ref("other_table")} TO "group:automation@example.com"`)
```

**Parameters:**

Name | Type |
------ | ------ |
`posts` | Contextable‹ITableContext, string &#124; string[]› |

**Returns:** *this*

___

###  preOps

▸ **preOps**(`pres`: Contextable‹ITableContext, string | string[]›): *this*

Sets a pre-operation to run before the query is run. This is often used for temporarily
granting permission to access source tables.

Example:

```js
// definitions/file.js
publish("example")
  .preOps(ctx => `GRANT \`roles/bigquery.dataViewer\` ON TABLE ${ctx.ref("other_table")} TO "group:automation@example.com"`)
  .query(ctx => `SELECT * FROM ${ctx.ref("other_table")}`)
  .postOps(ctx => `REVOKE \`roles/bigquery.dataViewer\` ON TABLE ${ctx.ref("other_table")} TO "group:automation@example.com"`)
```

**Parameters:**

Name | Type |
------ | ------ |
`pres` | Contextable‹ITableContext, string &#124; string[]› |

**Returns:** *this*

___

###  protected

▸ **protected**(`isProtected`: boolean): *this*

**`deprecated`** Deprecated in favor of
[IncrementalTableConfig.protected](configs#sqlanvil-ActionConfig-IncrementalTableConfig).

If called with `true`, prevents the dataset from being rebuilt from scratch.

**Parameters:**

Name | Type |
------ | ------ |
`isProtected` | boolean |

**Returns:** *this*

___

###  query

▸ **query**(`query`: Contextable‹ITableContext, string›): *this*

Sets the query to generate the table from.

**Parameters:**

Name | Type |
------ | ------ |
`query` | Contextable‹ITableContext, string› |

**Returns:** *this*

___

###  schema

▸ **schema**(`schema`: string): *this*

**`deprecated`** Deprecated in favor of
[IncrementalTableConfig.dataset](configs#sqlanvil-ActionConfig-IncrementalTableConfig).

Sets the schema (BigQuery dataset / Postgres schema) in which to create the output of this
action.

**Parameters:**

Name | Type |
------ | ------ |
`schema` | string |

**Returns:** *this*

___

###  setDependOnDependencyAssertions

▸ **setDependOnDependencyAssertions**(`dependOnDependencyAssertions`: boolean): *this*

**`deprecated`** Deprecated in favor of
[IncrementalTableConfig.dependOnDependencyAssertions](configs#sqlanvil-ActionConfig-IncrementalTableConfig).

When called with `true`, assertions dependent upon any dependency will be add as dedpendency
to this action.

**Parameters:**

Name | Type |
------ | ------ |
`dependOnDependencyAssertions` | boolean |

**Returns:** *this*

___

###  tags

▸ **tags**(`value`: string | string[]): *this*

**`deprecated`** Deprecated in favor of
[IncrementalTableConfig.tags](configs#sqlanvil-ActionConfig-IncrementalTableConfig).

Sets a list of user-defined tags applied to this action.

**Parameters:**

Name | Type |
------ | ------ |
`value` | string &#124; string[] |

**Returns:** *this*

___

###  type

▸ **type**(`type`: TableType): *this*

**`deprecated`** 
Deprecated in favor of action type can being set in the configs passed to action constructor
functions, for example `publish("name", { type: "incremental" })`.

**Parameters:**

Name | Type |
------ | ------ |
`type` | TableType |

**Returns:** *this*

___

###  uniqueKey

▸ **uniqueKey**(`uniqueKey`: string[]): *void*

**`deprecated`** Deprecated in favor of
[IncrementalTableConfig.uniqueKey](configs#sqlanvil-ActionConfig-IncrementalTableConfig).

If set, unique key represents a set of names of columns that will act as a the unique key. To
enforce this, when updating the incremental table, sqlanvil merges rows with `uniqueKey`
instead of appending them.

**Parameters:**

Name | Type |
------ | ------ |
`uniqueKey` | string[] |

**Returns:** *void*
