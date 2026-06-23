# Class: Test

sqlanvil test actions can be used to write unit tests for your generated SQL

You can create unit tests in the following ways.

**Using a SQLX file:**

```sql
-- definitions/name.sqlx
config {
  type: "test"
}

input "foo" {
  SELECT 1 AS bar
}

SELECT 1 AS bar
```

**Using the Javascript API:**

```js
// definitions/file.js
test("name")
  .input("sample_data", `SELECT 1 AS bar`)
  .expect(`SELECT 1 AS bar`);

publish("sample_data", { type: "table" }).query("SELECT 1 AS bar")
```

Note: When using the Javascript API, methods in this class can be accessed by the returned value.
This is where `input` and `expect` come from.

## Hierarchy

* ActionBuilder‹Test›

  ↳ **Test**

## Index

### Methods

* [dataset](#dataset)
* [expect](#expect)
* [input](#input)

## Methods

###  dataset

▸ **dataset**(`ref`: Resolvable): *this*

Sets the schema (BigQuery dataset / Postgres schema) in which to create the output of this
action.

**Parameters:**

Name | Type |
------ | ------ |
`ref` | Resolvable |

**Returns:** *this*

___

###  expect

▸ **expect**(`contextableQuery`: Contextable‹IActionContext, string›): *this*

Sets the expected output of the query to being tested against.

**Parameters:**

Name | Type |
------ | ------ |
`contextableQuery` | Contextable‹IActionContext, string› |

**Returns:** *this*

___

###  input

▸ **input**(`refName`: string | string[], `contextableQuery`: Contextable‹IActionContext, string›): *this*

Sets the input query to unit test against.

**Parameters:**

Name | Type |
------ | ------ |
`refName` | string &#124; string[] |
`contextableQuery` | Contextable‹IActionContext, string› |

**Returns:** *this*
