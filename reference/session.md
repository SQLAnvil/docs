# Class: Session

Contains methods that are published globally, so can be invoked anywhere in the `/definitions`
folder of a sqlanvil project.

## Hierarchy

* **Session**

## Index

### Properties

* [projectConfig](#projectconfig)

### Methods

* [assert](#assert)
* [declare](#declare)
* [notebook](#notebook)
* [operate](#operate)
* [publish](#publish)
* [test](#test)

## Properties

###  projectConfig

• **projectConfig**: *ProjectConfig*

Stores the sqlanvil project configuration of the current sqlanvil project. Can be accessed via
the `sqlanvil` global variable.

Example:

```js
sqlanvil.projectConfig.vars.myVariableName === "myVariableValue"
```

## Methods

###  assert

▸ **assert**(`name`: string, `queryOrConfig?`: AContextable‹string› | AssertionConfig): *[Assertion](assertion)*

Adds a sqlanvil assertion the compiled graph.

Available only in the `/definitions` directory.

**`see`** [assertion](Assertion) for examples on how to use.

**Parameters:**

Name | Type |
------ | ------ |
`name` | string |
`queryOrConfig?` | AContextable‹string› &#124; AssertionConfig |

**Returns:** *[Assertion](assertion)*

___

###  declare

▸ **declare**(`config`: DeclarationConfig | any): *[Declaration](declaration)*

Declares the dataset as a sqlanvil data source.

Available only in the `/definitions` directory.

**`see`** [Declaration](Declaration) for examples on how to use.

**Parameters:**

Name | Type |
------ | ------ |
`config` | DeclarationConfig &#124; any |

**Returns:** *[Declaration](declaration)*

___

###  notebook

▸ **notebook**(`config`: NotebookConfig): *[Notebook](notebook)*

Creates a Notebook action.

Available only in the `/definitions` directory.

**`see`** [Notebook](Notebook) for examples on how to use.

**Parameters:**

Name | Type |
------ | ------ |
`config` | NotebookConfig |

**Returns:** *[Notebook](notebook)*

___

###  operate

▸ **operate**(`name`: string, `queryOrConfig?`: Contextable‹IActionContext, string | string[]› | OperationConfig): *[Operation](operation)*

Defines a SQL operation.

Available only in the `/definitions` directory.

**`see`** [operation](Operation) for examples on how to use.

**Parameters:**

Name | Type |
------ | ------ |
`name` | string |
`queryOrConfig?` | Contextable‹IActionContext, string &#124; string[]› &#124; OperationConfig |

**Returns:** *[Operation](operation)*

___

###  publish

▸ **publish**(`name`: string, `queryOrConfig?`: Contextable‹ITableContext, string› | TableConfig | ViewConfig | IncrementalTableConfig | ILegacyTableConfig | any): *[Table](table) | [IncrementalTable](incrementaltable) | [View](view)*

Creates a table, view, or incremental table.

Available only in the `/definitions` directory.

**`see`** [Operation](Operation) for examples on how to make tables.

**`see`** [View](View) for examples on how to make views.

**`see`** [IncrementalTable](IncrementalTable) for examples on how to make incremental tables.

**Parameters:**

Name | Type |
------ | ------ |
`name` | string |
`queryOrConfig?` | Contextable‹ITableContext, string› &#124; TableConfig &#124; ViewConfig &#124; IncrementalTableConfig &#124; ILegacyTableConfig &#124; any |

**Returns:** *[Table](table) | [IncrementalTable](incrementaltable) | [View](view)*

___

###  test

▸ **test**(`name`: string): *[Test](test)*

Creates a Test action.

Available only in the `/definitions` directory.

**`see`** [Test](Test) for examples on how to use.

<!-- This doesn't currently support passing of config blocks as the second argument, but it
could in the future. -->
<!-- Note: this action type isn't very thoroughly tested, so careful with changes -->

**Parameters:**

Name | Type |
------ | ------ |
`name` | string |

**Returns:** *[Test](test)*
