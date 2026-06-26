# Protocol Documentation
<a name="top"></a>

## Table of Contents

- [configs.proto](#configs-proto)
    - [ActionConfig](#sqlanvil-ActionConfig)
    - [ActionConfig.AssertionConfig](#sqlanvil-ActionConfig-AssertionConfig)
    - [ActionConfig.ColumnDescriptor](#sqlanvil-ActionConfig-ColumnDescriptor)
    - [ActionConfig.DataPreparationConfig](#sqlanvil-ActionConfig-DataPreparationConfig)
    - [ActionConfig.DataPreparationConfig.ErrorTableConfig](#sqlanvil-ActionConfig-DataPreparationConfig-ErrorTableConfig)
    - [ActionConfig.DeclarationConfig](#sqlanvil-ActionConfig-DeclarationConfig)
    - [ActionConfig.DeclarationConfig.ColumnTypesEntry](#sqlanvil-ActionConfig-DeclarationConfig-ColumnTypesEntry)
    - [ActionConfig.ExportConfig](#sqlanvil-ActionConfig-ExportConfig)
    - [ActionConfig.ExportOptions](#sqlanvil-ActionConfig-ExportOptions)
    - [ActionConfig.ExportOptions.OptionsEntry](#sqlanvil-ActionConfig-ExportOptions-OptionsEntry)
    - [ActionConfig.ForeignWrapperConfig](#sqlanvil-ActionConfig-ForeignWrapperConfig)
    - [ActionConfig.ForeignWrapperConfig.OptionsEntry](#sqlanvil-ActionConfig-ForeignWrapperConfig-OptionsEntry)
    - [ActionConfig.IcebergTableConfig](#sqlanvil-ActionConfig-IcebergTableConfig)
    - [ActionConfig.ImportConfig](#sqlanvil-ActionConfig-ImportConfig)
    - [ActionConfig.ImportOptions](#sqlanvil-ActionConfig-ImportOptions)
    - [ActionConfig.ImportOptions.OptionsEntry](#sqlanvil-ActionConfig-ImportOptions-OptionsEntry)
    - [ActionConfig.IncrementalTableConfig](#sqlanvil-ActionConfig-IncrementalTableConfig)
    - [ActionConfig.IncrementalTableConfig.AdditionalOptionsEntry](#sqlanvil-ActionConfig-IncrementalTableConfig-AdditionalOptionsEntry)
    - [ActionConfig.IncrementalTableConfig.LabelsEntry](#sqlanvil-ActionConfig-IncrementalTableConfig-LabelsEntry)
    - [ActionConfig.LoadModeConfig](#sqlanvil-ActionConfig-LoadModeConfig)
    - [ActionConfig.Metadata](#sqlanvil-ActionConfig-Metadata)
    - [ActionConfig.NotebookConfig](#sqlanvil-ActionConfig-NotebookConfig)
    - [ActionConfig.OperationConfig](#sqlanvil-ActionConfig-OperationConfig)
    - [ActionConfig.RealtimePublicationConfig](#sqlanvil-ActionConfig-RealtimePublicationConfig)
    - [ActionConfig.RlsPolicyConfig](#sqlanvil-ActionConfig-RlsPolicyConfig)
    - [ActionConfig.TableAssertionsConfig](#sqlanvil-ActionConfig-TableAssertionsConfig)
    - [ActionConfig.TableAssertionsConfig.UniqueKey](#sqlanvil-ActionConfig-TableAssertionsConfig-UniqueKey)
    - [ActionConfig.TableConfig](#sqlanvil-ActionConfig-TableConfig)
    - [ActionConfig.TableConfig.AdditionalOptionsEntry](#sqlanvil-ActionConfig-TableConfig-AdditionalOptionsEntry)
    - [ActionConfig.TableConfig.LabelsEntry](#sqlanvil-ActionConfig-TableConfig-LabelsEntry)
    - [ActionConfig.Target](#sqlanvil-ActionConfig-Target)
    - [ActionConfig.VectorIndexConfig](#sqlanvil-ActionConfig-VectorIndexConfig)
    - [ActionConfig.VectorIndexConfig.ParamsEntry](#sqlanvil-ActionConfig-VectorIndexConfig-ParamsEntry)
    - [ActionConfig.ViewConfig](#sqlanvil-ActionConfig-ViewConfig)
    - [ActionConfig.ViewConfig.AdditionalOptionsEntry](#sqlanvil-ActionConfig-ViewConfig-AdditionalOptionsEntry)
    - [ActionConfig.ViewConfig.LabelsEntry](#sqlanvil-ActionConfig-ViewConfig-LabelsEntry)
    - [ActionConfigs](#sqlanvil-ActionConfigs)
    - [BigQueryConnection](#sqlanvil-BigQueryConnection)
    - [ConnectionConfig](#sqlanvil-ConnectionConfig)
    - [DefaultIcebergConfig](#sqlanvil-DefaultIcebergConfig)
    - [Environment](#sqlanvil-Environment)
    - [Environment.VarsEntry](#sqlanvil-Environment-VarsEntry)
    - [MysqlConnection](#sqlanvil-MysqlConnection)
    - [MysqlOptions](#sqlanvil-MysqlOptions)
    - [MysqlOptions.Index](#sqlanvil-MysqlOptions-Index)
    - [MysqlOptions.Partition](#sqlanvil-MysqlOptions-Partition)
    - [MysqlOptions.Partition.Bound](#sqlanvil-MysqlOptions-Partition-Bound)
    - [NotebookRuntimeOptionsConfig](#sqlanvil-NotebookRuntimeOptionsConfig)
    - [PostgresConnection](#sqlanvil-PostgresConnection)
    - [PostgresOptions](#sqlanvil-PostgresOptions)
    - [PostgresOptions.Index](#sqlanvil-PostgresOptions-Index)
    - [PostgresOptions.Partition](#sqlanvil-PostgresOptions-Partition)
    - [PostgresOptions.Partition.Bound](#sqlanvil-PostgresOptions-Partition-Bound)
    - [RepositorySnapshotDestinationConfig](#sqlanvil-RepositorySnapshotDestinationConfig)
    - [SupabaseConnection](#sqlanvil-SupabaseConnection)
    - [SupabaseOptions](#sqlanvil-SupabaseOptions)
    - [SupabaseOptions.VectorConfig](#sqlanvil-SupabaseOptions-VectorConfig)
    - [SupabaseOptions.VectorConfig.ParamsEntry](#sqlanvil-SupabaseOptions-VectorConfig-ParamsEntry)
    - [WarehouseConfig](#sqlanvil-WarehouseConfig)
    - [WorkflowSettings](#sqlanvil-WorkflowSettings)
    - [WorkflowSettings.ConnectionsEntry](#sqlanvil-WorkflowSettings-ConnectionsEntry)
    - [WorkflowSettings.EnvironmentsEntry](#sqlanvil-WorkflowSettings-EnvironmentsEntry)
    - [WorkflowSettings.VarsEntry](#sqlanvil-WorkflowSettings-VarsEntry)
  
    - [ActionConfig.IcebergTableConfig.FileFormat](#sqlanvil-ActionConfig-IcebergTableConfig-FileFormat)
    - [ActionConfig.LoadMode](#sqlanvil-ActionConfig-LoadMode)
    - [ActionConfig.OnSchemaChange](#sqlanvil-ActionConfig-OnSchemaChange)
    - [MysqlOptions.Partition.Kind](#sqlanvil-MysqlOptions-Partition-Kind)
    - [PostgresOptions.Index.Method](#sqlanvil-PostgresOptions-Index-Method)
    - [PostgresOptions.Partition.Kind](#sqlanvil-PostgresOptions-Partition-Kind)
    - [SupabaseOptions.VectorConfig.IndexType](#sqlanvil-SupabaseOptions-VectorConfig-IndexType)
  
- [Scalar Value Types](#scalar-value-types)



<a name="configs-proto"></a>
<p align="right"><a href="#top">Top</a></p>

## configs.proto



<a name="sqlanvil-ActionConfig"></a>

### ActionConfig
Action config defines the contents of `actions.yaml` configuration files.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| table | [ActionConfig.TableConfig](#sqlanvil-ActionConfig-TableConfig) |  |  |
| view | [ActionConfig.ViewConfig](#sqlanvil-ActionConfig-ViewConfig) |  |  |
| incrementalTable | [ActionConfig.IncrementalTableConfig](#sqlanvil-ActionConfig-IncrementalTableConfig) |  |  |
| assertion | [ActionConfig.AssertionConfig](#sqlanvil-ActionConfig-AssertionConfig) |  |  |
| operation | [ActionConfig.OperationConfig](#sqlanvil-ActionConfig-OperationConfig) |  |  |
| declaration | [ActionConfig.DeclarationConfig](#sqlanvil-ActionConfig-DeclarationConfig) |  |  |
| notebook | [ActionConfig.NotebookConfig](#sqlanvil-ActionConfig-NotebookConfig) |  |  |
| dataPreparation | [ActionConfig.DataPreparationConfig](#sqlanvil-ActionConfig-DataPreparationConfig) |  |  |
| rlsPolicy | [ActionConfig.RlsPolicyConfig](#sqlanvil-ActionConfig-RlsPolicyConfig) |  |  |
| realtimePublication | [ActionConfig.RealtimePublicationConfig](#sqlanvil-ActionConfig-RealtimePublicationConfig) |  |  |
| foreignWrapper | [ActionConfig.ForeignWrapperConfig](#sqlanvil-ActionConfig-ForeignWrapperConfig) |  |  |
| vectorIndex | [ActionConfig.VectorIndexConfig](#sqlanvil-ActionConfig-VectorIndexConfig) |  |  |
| export | [ActionConfig.ExportConfig](#sqlanvil-ActionConfig-ExportConfig) |  |  |
| import | [ActionConfig.ImportConfig](#sqlanvil-ActionConfig-ImportConfig) |  |  |






<a name="sqlanvil-ActionConfig-AssertionConfig"></a>

### ActionConfig.AssertionConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  | The name of the assertion. |
| dataset | [string](#string) |  | The dataset (schema) of the assertion. |
| project | [string](#string) |  | The Google Cloud project (database) of the assertion. |
| dependencyTargets | [ActionConfig.Target](#sqlanvil-ActionConfig-Target) | repeated | Targets of actions that this action is dependent on. |
| filename | [string](#string) |  | Path to the source file that the contents of the action is loaded from. |
| tags | [string](#string) | repeated | A list of user-defined tags with which the action should be labeled. |
| disabled | [bool](#bool) |  | If set to true, this action will not be executed. However, the action can still be depended upon. Useful for temporarily turning off broken actions. |
| description | [string](#string) |  | Description of the assertion. |
| hermetic | [bool](#bool) |  | If true, this indicates that the action only depends on data from explicitly-declared dependencies. Otherwise if false, it indicates that the action depends on data from a source which has not been declared as a dependency. |
| dependOnDependencyAssertions | [bool](#bool) |  | If true, assertions dependent upon any of the dependencies are added as dependencies as well. |
| reservation | [string](#string) |  | Optional. The BigQuery reservation to use for execution. If unset, the value from workflow_settings.yaml is used. If neither is set, default BigQuery behavior applies. sqlanvil CLI only (GCP sqlanvil support pending). |
| metadata | [ActionConfig.Metadata](#sqlanvil-ActionConfig-Metadata) |  | Metadata for this assertion. |






<a name="sqlanvil-ActionConfig-ColumnDescriptor"></a>

### ActionConfig.ColumnDescriptor



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| path | [string](#string) | repeated | The identifier for the column, using multiple parts for nested records. |
| description | [string](#string) |  | A text description of the column. |
| bigqueryPolicyTags | [string](#string) | repeated | A list of BigQuery policy tags that will be applied to the column. |
| tags | [string](#string) | repeated | A list of tags for this column which will be applied. |






<a name="sqlanvil-ActionConfig-DataPreparationConfig"></a>

### ActionConfig.DataPreparationConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  | The name of the data preparation. |
| dataset | [string](#string) |  | The dataset (schema) of the destination table. |
| project | [string](#string) |  | The Google Cloud project (database) of the destination table. |
| dependencyTargets | [ActionConfig.Target](#sqlanvil-ActionConfig-Target) | repeated | Targets of actions that this action is dependent on. |
| filename | [string](#string) |  | Path to the source file that the contents of the action is loaded from. |
| tags | [string](#string) | repeated | A list of user-defined tags with which the action should be labeled. |
| disabled | [bool](#bool) |  | If set to true, this action will not be executed. However, the action can still be depended upon. Useful for temporarily turning off broken actions. |
| description | [string](#string) |  | Description of the data preparation. |
| errorTable | [ActionConfig.DataPreparationConfig.ErrorTableConfig](#sqlanvil-ActionConfig-DataPreparationConfig-ErrorTableConfig) |  |  |
| loadMode | [ActionConfig.LoadModeConfig](#sqlanvil-ActionConfig-LoadModeConfig) |  |  |






<a name="sqlanvil-ActionConfig-DataPreparationConfig-ErrorTableConfig"></a>

### ActionConfig.DataPreparationConfig.ErrorTableConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  | The name of the error table. |
| dataset | [string](#string) |  | The dataset (schema) of the error table. |
| project | [string](#string) |  | The Google Cloud project (database) of the error table. |
| retentionDays | [int32](#int32) |  |  |






<a name="sqlanvil-ActionConfig-DeclarationConfig"></a>

### ActionConfig.DeclarationConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  | The name of the declaration. |
| dataset | [string](#string) |  | The dataset (schema) of the declaration. |
| project | [string](#string) |  | The Google Cloud project (database) of the declaration. |
| description | [string](#string) |  | Description of the declaration. |
| columns | [ActionConfig.ColumnDescriptor](#sqlanvil-ActionConfig-ColumnDescriptor) | repeated | Descriptions of columns within the declaration. |
| filename | [string](#string) |  | Path to the source file that the contents of the action is loaded from. |
| tags | [string](#string) | repeated | A list of user-defined tags with which the action should be labeled. |
| connection | [string](#string) |  | Optional. Name of a connection (from WorkflowSettings.connections) that this declaration reads from. Only valid on declarations. |
| columnTypes | [ActionConfig.DeclarationConfig.ColumnTypesEntry](#sqlanvil-ActionConfig-DeclarationConfig-ColumnTypesEntry) | repeated | Optional. Column name -&gt; SQL type, used to generate the foreign table when `connection` bridges via FDW. Distinct from `columns` (descriptions). |






<a name="sqlanvil-ActionConfig-DeclarationConfig-ColumnTypesEntry"></a>

### ActionConfig.DeclarationConfig.ColumnTypesEntry



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| key | [string](#string) |  |  |
| value | [string](#string) |  |  |






<a name="sqlanvil-ActionConfig-ExportConfig"></a>

### ActionConfig.ExportConfig
Configuration for a `type: &#34;export&#34;` action: writes a SELECT result to a
Parquet/CSV/JSON file at a cloud or local location.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  | The name of the export. |
| dataset | [string](#string) |  | The dataset (schema) used to qualify the export&#39;s target name. |
| project | [string](#string) |  | The Google Cloud project (database) of the export. |
| dependencyTargets | [ActionConfig.Target](#sqlanvil-ActionConfig-Target) | repeated | Targets of actions that this action is dependent on. |
| filename | [string](#string) |  | Path to the source file that the contents of the action is loaded from. |
| tags | [string](#string) | repeated | A list of user-defined tags with which the action should be labeled. |
| disabled | [bool](#bool) |  | If set to true, this action will not be executed. |
| description | [string](#string) |  | Description of the export. |
| hermetic | [bool](#bool) |  | If true, this action only depends on data from explicitly-declared dependencies. |
| export | [ActionConfig.ExportOptions](#sqlanvil-ActionConfig-ExportOptions) |  | The export destination &#43; format options (the `export: {}` block). |






<a name="sqlanvil-ActionConfig-ExportOptions"></a>

### ActionConfig.ExportOptions
The user-facing `export: {}` block on a `type: &#34;export&#34;` action.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| location | [string](#string) |  | Destination folder/prefix URI: gs:// | s3:// | local:// | relative/absolute path. |
| format | [string](#string) |  | &#34;parquet&#34; | &#34;csv&#34; | &#34;json&#34; (json = JSONL). |
| overwrite | [bool](#bool) |  | Overwrite an existing object/file. Defaults to true (defaulted in core when absent). |
| filename | [string](#string) |  | Output base filename; defaults to the action name. |
| options | [ActionConfig.ExportOptions.OptionsEntry](#sqlanvil-ActionConfig-ExportOptions-OptionsEntry) | repeated | Format-specific passthrough options (e.g. compression, csv header/delimiter). |






<a name="sqlanvil-ActionConfig-ExportOptions-OptionsEntry"></a>

### ActionConfig.ExportOptions.OptionsEntry



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| key | [string](#string) |  |  |
| value | [string](#string) |  |  |






<a name="sqlanvil-ActionConfig-ForeignWrapperConfig"></a>

### ActionConfig.ForeignWrapperConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  |  |
| wrapper | [string](#string) |  |  |
| server | [string](#string) |  |  |
| options | [ActionConfig.ForeignWrapperConfig.OptionsEntry](#sqlanvil-ActionConfig-ForeignWrapperConfig-OptionsEntry) | repeated |  |
| filename | [string](#string) |  |  |
| dependencyTargets | [ActionConfig.Target](#sqlanvil-ActionConfig-Target) | repeated |  |






<a name="sqlanvil-ActionConfig-ForeignWrapperConfig-OptionsEntry"></a>

### ActionConfig.ForeignWrapperConfig.OptionsEntry



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| key | [string](#string) |  |  |
| value | [string](#string) |  |  |






<a name="sqlanvil-ActionConfig-IcebergTableConfig"></a>

### ActionConfig.IcebergTableConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| fileFormat | [ActionConfig.IcebergTableConfig.FileFormat](#sqlanvil-ActionConfig-IcebergTableConfig-FileFormat) |  | The file format for the BigQuery table. |
| connection | [string](#string) |  | The connection specifying the credentials to be used to read and write to external storage, such as Cloud Storage. The connection can have the form `{project}.{location}.{connection_id}` or `projects/{project}/locations/{location}/connections/{connection_id}&#34;, or be set to DEFAULT. |
| bucketName | [string](#string) |  | The name of the Cloud Storage bucket where table data is stored. This value is be used to construct the storage URI in the following way: `gs://{bucket_name}/{table_folder_root}/{table_folder_subpath}``. If `storage_uri` is provided, this value is ignored. |
| tableFolderRoot | [string](#string) |  | The name of the first-level folder inside the Cloud Storage bucket where table data is stored. This value will be used to construct the storage URI in the following way: `gs://{bucket_name}/{table_folder_root}/{table_folder_subpath}``. If `storage_uri` is provided, this value is ignored. |
| tableFolderSubpath | [string](#string) |  | The path under the first-level folder of the Cloud Storage bucket where table data is stored. This value will be used to construct the storage URI in the following way: `gs://{bucket_name}/{table_folder_root}/{table_folder_subpath}``. If `storage_uri` is provided, this value is ignored. |






<a name="sqlanvil-ActionConfig-ImportConfig"></a>

### ActionConfig.ImportConfig
Configuration for a `type: &#34;import&#34;` action: loads a Parquet/CSV/JSON file into a table in
the warehouse (the inverse of `type: &#34;export&#34;`). The resulting table is ref()-able.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  | The name of the import (the destination table name). |
| dataset | [string](#string) |  | The dataset (schema) used to qualify the import&#39;s target name. |
| project | [string](#string) |  | The Google Cloud project (database) of the import. |
| dependencyTargets | [ActionConfig.Target](#sqlanvil-ActionConfig-Target) | repeated | Targets of actions that this action is dependent on. |
| filename | [string](#string) |  | Path to the source .sqlx file the action is defined in (generated). |
| tags | [string](#string) | repeated | A list of user-defined tags with which the action should be labeled. |
| disabled | [bool](#bool) |  | If set to true, this action will not be executed. |
| description | [string](#string) |  | Description of the import. |
| hermetic | [bool](#bool) |  | If true, this action only depends on data from explicitly-declared dependencies. |
| import | [ActionConfig.ImportOptions](#sqlanvil-ActionConfig-ImportOptions) |  | The import source &#43; format options (the `import: {}` block). |






<a name="sqlanvil-ActionConfig-ImportOptions"></a>

### ActionConfig.ImportOptions
The user-facing `import: {}` block on a `type: &#34;import&#34;` action.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| location | [string](#string) |  | Source file/glob/URI to read: gs:// | s3:// | local:// | relative/absolute path. Used verbatim (unlike export, where the filename is derived) — point it at the exact file or glob (e.g. gs://bucket/orders/*.parquet). |
| format | [string](#string) |  | &#34;parquet&#34; | &#34;csv&#34; | &#34;json&#34; (json = JSONL). |
| overwrite | [bool](#bool) |  | Replace the destination table (drop &#43; create). Defaults to true. When false, rows are appended (INSERT … SELECT) into an existing table. |
| options | [ActionConfig.ImportOptions.OptionsEntry](#sqlanvil-ActionConfig-ImportOptions-OptionsEntry) | repeated | Format-specific passthrough options (reserved for future reader options). |






<a name="sqlanvil-ActionConfig-ImportOptions-OptionsEntry"></a>

### ActionConfig.ImportOptions.OptionsEntry



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| key | [string](#string) |  |  |
| value | [string](#string) |  |  |






<a name="sqlanvil-ActionConfig-IncrementalTableConfig"></a>

### ActionConfig.IncrementalTableConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  | The name of the incremental table. |
| dataset | [string](#string) |  | The dataset (schema) of the incremental table. |
| project | [string](#string) |  | The Google Cloud project (database) of the incremental table. |
| dependencyTargets | [ActionConfig.Target](#sqlanvil-ActionConfig-Target) | repeated | Targets of actions that this action is dependent on. |
| filename | [string](#string) |  | Path to the source file that the contents of the action is loaded from. |
| tags | [string](#string) | repeated | A list of user-defined tags with which the action should be labeled. |
| disabled | [bool](#bool) |  | If set to true, this action will not be executed. However, the action can still be depended upon. Useful for temporarily turning off broken actions. |
| preOperations | [string](#string) | repeated | Queries to run before `query`. This can be useful for granting permissions. |
| postOperations | [string](#string) | repeated | Queries to run after `query`. |
| protected | [bool](#bool) |  | If true, prevents the dataset from being rebuilt from scratch. |
| uniqueKey | [string](#string) | repeated | If set, unique key represents a set of names of columns that will act as a the unique key. To enforce this, when updating the incremental table, sqlanvil merges rows with `uniqueKey` instead of appending them. |
| description | [string](#string) |  | Description of the incremental table. |
| columns | [ActionConfig.ColumnDescriptor](#sqlanvil-ActionConfig-ColumnDescriptor) | repeated | Descriptions of columns within the table. |
| partitionBy | [string](#string) |  | The key by which to partition the table. Typically the name of a timestamp or the date column. See https://cloud.google.com/dataform/docs/partitions-clusters. |
| partitionExpirationDays | [int32](#int32) |  | The number of days for which BigQuery stores data in each partition. The setting applies to all partitions in a table, but is calculated independently for each partition based on the partition time. |
| requirePartitionFilter | [bool](#bool) |  | Declares whether the partitioned table requires a WHERE clause predicate filter that filters the partitioning column. |
| updatePartitionFilter | [string](#string) |  | SQL-based filter for when incremental updates are applied. |
| clusterBy | [string](#string) | repeated | The keys by which to cluster partitions by. See https://cloud.google.com/dataform/docs/partitions-clusters. |
| labels | [ActionConfig.IncrementalTableConfig.LabelsEntry](#sqlanvil-ActionConfig-IncrementalTableConfig-LabelsEntry) | repeated | Key-value pairs for BigQuery labels. |
| additionalOptions | [ActionConfig.IncrementalTableConfig.AdditionalOptionsEntry](#sqlanvil-ActionConfig-IncrementalTableConfig-AdditionalOptionsEntry) | repeated | Key-value pairs of additional options to pass to the BigQuery API. Some options, for example, partitionExpirationDays, have dedicated type/validity checked fields. For such options, use the dedicated fields. |
| dependOnDependencyAssertions | [bool](#bool) |  | When set to true, assertions dependent upon any dependency will be add as dedpendency to this action |
| assertions | [ActionConfig.TableAssertionsConfig](#sqlanvil-ActionConfig-TableAssertionsConfig) |  | Assertions to be run on the dataset. If configured, relevant assertions will automatically be created and run as a dependency of this dataset. |
| hermetic | [bool](#bool) |  | If true, this indicates that the action only depends on data from explicitly-declared dependencies. Otherwise if false, it indicates that the action depends on data from a source which has not been declared as a dependency. |
| onSchemaChange | [ActionConfig.OnSchemaChange](#sqlanvil-ActionConfig-OnSchemaChange) |  | Defines the action behavior if the selected columns in the query don&#39;t the match columns in the target table. |
| iceberg | [ActionConfig.IcebergTableConfig](#sqlanvil-ActionConfig-IcebergTableConfig) |  | Configuration options for an Iceberg table. |
| metadata | [ActionConfig.Metadata](#sqlanvil-ActionConfig-Metadata) |  | Metadata for this incremental table. |
| reservation | [string](#string) |  | Optional. The BigQuery reservation to use for execution. If unset, the value from workflow_settings.yaml is used. If neither is set, default BigQuery behavior applies. sqlanvil CLI only (GCP sqlanvil support pending). |
| postgres | [PostgresOptions](#sqlanvil-PostgresOptions) |  | Postgres and Supabase specific options. |
| supabase | [SupabaseOptions](#sqlanvil-SupabaseOptions) |  |  |
| mysql | [MysqlOptions](#sqlanvil-MysqlOptions) |  | MySQL/MariaDB specific options (engine/charset/collation &#43; indexes). |






<a name="sqlanvil-ActionConfig-IncrementalTableConfig-AdditionalOptionsEntry"></a>

### ActionConfig.IncrementalTableConfig.AdditionalOptionsEntry



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| key | [string](#string) |  |  |
| value | [string](#string) |  |  |






<a name="sqlanvil-ActionConfig-IncrementalTableConfig-LabelsEntry"></a>

### ActionConfig.IncrementalTableConfig.LabelsEntry



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| key | [string](#string) |  |  |
| value | [string](#string) |  |  |






<a name="sqlanvil-ActionConfig-LoadModeConfig"></a>

### ActionConfig.LoadModeConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| mode | [ActionConfig.LoadMode](#sqlanvil-ActionConfig-LoadMode) |  |  |
| incrementalColumn | [string](#string) |  | Required when mode is MAXIMUM or UNIQUE |
| uniqueKey | [string](#string) | repeated | required when mode is MERGE |






<a name="sqlanvil-ActionConfig-Metadata"></a>

### ActionConfig.Metadata



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| overview | [string](#string) |  | A detailed description of the data object. |
| extraProperties | [google.protobuf.Struct](#google-protobuf-Struct) |  | Extra properties of the data object. |






<a name="sqlanvil-ActionConfig-NotebookConfig"></a>

### ActionConfig.NotebookConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  | The name of the notebook. |
| location | [string](#string) |  | The Google Cloud location of the notebook. |
| project | [string](#string) |  | The Google Cloud project (database) of the notebook. |
| dependencyTargets | [ActionConfig.Target](#sqlanvil-ActionConfig-Target) | repeated | Targets of actions that this action is dependent on. |
| filename | [string](#string) |  | Path to the source file that the contents of the action is loaded from. |
| tags | [string](#string) | repeated | A list of user-defined tags with which the action should be labeled. |
| disabled | [bool](#bool) |  | If set to true, this action will not be executed. However, the action can still be depended upon. Useful for temporarily turning off broken actions. |
| description | [string](#string) |  | Description of the notebook. |
| dependOnDependencyAssertions | [bool](#bool) |  | When set to true, assertions dependent upon any dependency will be add as dedpendency to this action |






<a name="sqlanvil-ActionConfig-OperationConfig"></a>

### ActionConfig.OperationConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  | The name of the operation. |
| dataset | [string](#string) |  | The dataset (schema) of the operation. |
| project | [string](#string) |  | The Google Cloud project (database) of the operation. |
| dependencyTargets | [ActionConfig.Target](#sqlanvil-ActionConfig-Target) | repeated | Targets of actions that this action is dependent on. |
| filename | [string](#string) |  | Path to the source file that the contents of the action is loaded from. |
| tags | [string](#string) | repeated | A list of user-defined tags with which the action should be labeled. |
| disabled | [bool](#bool) |  | If set to true, this action will not be executed. However, the action can still be depended upon. Useful for temporarily turning off broken actions. |
| hasOutput | [bool](#bool) |  | Declares that this action creates a dataset which should be referenceable as a dependency target, for example by using the `ref` function. |
| description | [string](#string) |  | Description of the operation. |
| columns | [ActionConfig.ColumnDescriptor](#sqlanvil-ActionConfig-ColumnDescriptor) | repeated | Descriptions of columns within the operation. Can only be set if hasOutput is true. |
| dependOnDependencyAssertions | [bool](#bool) |  | When set to true, assertions dependent upon any dependency will be add as dedpendency to this action |
| hermetic | [bool](#bool) |  | If true, this indicates that the action only depends on data from explicitly-declared dependencies. Otherwise if false, it indicates that the action depends on data from a source which has not been declared as a dependency. |
| reservation | [string](#string) |  | Optional. The BigQuery reservation to use for execution. If unset, the value from workflow_settings.yaml is used. If neither is set, default BigQuery behavior applies. sqlanvil CLI only (GCP sqlanvil support pending). |






<a name="sqlanvil-ActionConfig-RealtimePublicationConfig"></a>

### ActionConfig.RealtimePublicationConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  |  |
| table | [string](#string) |  |  |
| events | [string](#string) | repeated |  |
| filename | [string](#string) |  |  |
| dependencyTargets | [ActionConfig.Target](#sqlanvil-ActionConfig-Target) | repeated |  |






<a name="sqlanvil-ActionConfig-RlsPolicyConfig"></a>

### ActionConfig.RlsPolicyConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  |  |
| table | [string](#string) |  |  |
| command | [string](#string) |  |  |
| roles | [string](#string) | repeated |  |
| using | [string](#string) |  |  |
| withCheck | [string](#string) |  |  |
| filename | [string](#string) |  |  |
| dependencyTargets | [ActionConfig.Target](#sqlanvil-ActionConfig-Target) | repeated |  |






<a name="sqlanvil-ActionConfig-TableAssertionsConfig"></a>

### ActionConfig.TableAssertionsConfig
Options for shorthand specifying assertions, useable for some table-based
action types.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| uniqueKey | [string](#string) | repeated | Column(s) which constitute the dataset&#39;s unique key index. If set, the resulting assertion will fail if there is more than one row in the dataset with the same values for all of these column(s). |
| uniqueKeys | [ActionConfig.TableAssertionsConfig.UniqueKey](#sqlanvil-ActionConfig-TableAssertionsConfig-UniqueKey) | repeated |  |
| nonNull | [string](#string) | repeated | Column(s) which may never be `NULL`. If set, the resulting assertion will fail if any row contains `NULL` values for these column(s). |
| rowConditions | [string](#string) | repeated | General condition(s) which should hold true for all rows in the dataset. If set, the resulting assertion will fail if any row violates any of these condition(s). |






<a name="sqlanvil-ActionConfig-TableAssertionsConfig-UniqueKey"></a>

### ActionConfig.TableAssertionsConfig.UniqueKey
Combinations of column(s), each of which should constitute a unique key
index for the dataset. If set, the resulting assertion(s) will fail if
there is more than one row in the dataset with the same values for all of
the column(s) in the unique key(s).


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| uniqueKey | [string](#string) | repeated |  |






<a name="sqlanvil-ActionConfig-TableConfig"></a>

### ActionConfig.TableConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  | The name of the table. |
| dataset | [string](#string) |  | The dataset (schema) of the table. |
| project | [string](#string) |  | The Google Cloud project (database) of the table. |
| dependencyTargets | [ActionConfig.Target](#sqlanvil-ActionConfig-Target) | repeated | Targets of actions that this action is dependent on. |
| filename | [string](#string) |  | Path to the source file that the contents of the action is loaded from. |
| tags | [string](#string) | repeated | A list of user-defined tags with which the action should be labeled. |
| disabled | [bool](#bool) |  | If set to true, this action will not be executed. However, the action can still be depended upon. Useful for temporarily turning off broken actions. |
| preOperations | [string](#string) | repeated | Queries to run before `query`. This can be useful for granting permissions. |
| postOperations | [string](#string) | repeated | Queries to run after `query`. |
| description | [string](#string) |  | Description of the table. |
| columns | [ActionConfig.ColumnDescriptor](#sqlanvil-ActionConfig-ColumnDescriptor) | repeated | Descriptions of columns within the table. |
| partitionBy | [string](#string) |  | The key by which to partition the table. Typically the name of a timestamp or the date column. See https://cloud.google.com/dataform/docs/partitions-clusters. |
| partitionExpirationDays | [int32](#int32) |  | The number of days for which BigQuery stores data in each partition. The setting applies to all partitions in a table, but is calculated independently for each partition based on the partition time. |
| requirePartitionFilter | [bool](#bool) |  | Declares whether the partitioned table requires a WHERE clause predicate filter that filters the partitioning column. |
| clusterBy | [string](#string) | repeated | The keys by which to cluster partitions by. See https://cloud.google.com/dataform/docs/partitions-clusters. |
| labels | [ActionConfig.TableConfig.LabelsEntry](#sqlanvil-ActionConfig-TableConfig-LabelsEntry) | repeated | Key-value pairs for BigQuery labels. |
| additionalOptions | [ActionConfig.TableConfig.AdditionalOptionsEntry](#sqlanvil-ActionConfig-TableConfig-AdditionalOptionsEntry) | repeated | Key-value pairs of additional options to pass to the BigQuery API. Some options, for example, partitionExpirationDays, have dedicated type/validity checked fields. For such options, use the dedicated fields. |
| dependOnDependencyAssertions | [bool](#bool) |  | When set to true, assertions dependent upon any dependency will be add as dedpendency to this action |
| assertions | [ActionConfig.TableAssertionsConfig](#sqlanvil-ActionConfig-TableAssertionsConfig) |  | Assertions to be run on the dataset. If configured, relevant assertions will automatically be created and run as a dependency of this dataset. |
| hermetic | [bool](#bool) |  | If true, this indicates that the action only depends on data from explicitly-declared dependencies. Otherwise if false, it indicates that the action depends on data from a source which has not been declared as a dependency. |
| iceberg | [ActionConfig.IcebergTableConfig](#sqlanvil-ActionConfig-IcebergTableConfig) |  | Configuration options for an Iceberg table. |
| metadata | [ActionConfig.Metadata](#sqlanvil-ActionConfig-Metadata) |  | Metadata for this table. |
| reservation | [string](#string) |  | Optional. The BigQuery reservation to use for execution. If unset, the value from workflow_settings.yaml is used. If neither is set, default BigQuery behavior applies. sqlanvil CLI only (GCP sqlanvil support pending). |
| postgres | [PostgresOptions](#sqlanvil-PostgresOptions) |  | Postgres and Supabase specific options. |
| supabase | [SupabaseOptions](#sqlanvil-SupabaseOptions) |  |  |
| mysql | [MysqlOptions](#sqlanvil-MysqlOptions) |  | MySQL/MariaDB specific options (engine/charset/collation &#43; indexes). |






<a name="sqlanvil-ActionConfig-TableConfig-AdditionalOptionsEntry"></a>

### ActionConfig.TableConfig.AdditionalOptionsEntry



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| key | [string](#string) |  |  |
| value | [string](#string) |  |  |






<a name="sqlanvil-ActionConfig-TableConfig-LabelsEntry"></a>

### ActionConfig.TableConfig.LabelsEntry



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| key | [string](#string) |  |  |
| value | [string](#string) |  |  |






<a name="sqlanvil-ActionConfig-Target"></a>

### ActionConfig.Target
Target represents a unique action identifier.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| project | [string](#string) |  | The Google Cloud project (database) of the action. |
| dataset | [string](#string) |  | The dataset (schema) of the action. For notebooks, this is the location. |
| name | [string](#string) |  | The name of the action. |
| includeDependentAssertions | [bool](#bool) |  | flag for when we want to add assertions of this dependency in dependency_targets as well. |






<a name="sqlanvil-ActionConfig-VectorIndexConfig"></a>

### ActionConfig.VectorIndexConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  |  |
| table | [string](#string) |  |  |
| column | [string](#string) |  |  |
| dimensions | [uint32](#uint32) |  |  |
| indexType | [string](#string) |  |  |
| params | [ActionConfig.VectorIndexConfig.ParamsEntry](#sqlanvil-ActionConfig-VectorIndexConfig-ParamsEntry) | repeated |  |
| filename | [string](#string) |  |  |
| dependencyTargets | [ActionConfig.Target](#sqlanvil-ActionConfig-Target) | repeated |  |






<a name="sqlanvil-ActionConfig-VectorIndexConfig-ParamsEntry"></a>

### ActionConfig.VectorIndexConfig.ParamsEntry



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| key | [string](#string) |  |  |
| value | [string](#string) |  |  |






<a name="sqlanvil-ActionConfig-ViewConfig"></a>

### ActionConfig.ViewConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  | The name of the view. |
| dataset | [string](#string) |  | The dataset (schema) of the view. |
| project | [string](#string) |  | The Google Cloud project (database) of the view. |
| dependencyTargets | [ActionConfig.Target](#sqlanvil-ActionConfig-Target) | repeated | Targets of actions that this action is dependent on. |
| filename | [string](#string) |  | Path to the source file that the contents of the action is loaded from. |
| tags | [string](#string) | repeated | A list of user-defined tags with which the action should be labeled. |
| disabled | [bool](#bool) |  | If set to true, this action will not be executed. However, the action can still be depended upon. Useful for temporarily turning off broken actions. |
| preOperations | [string](#string) | repeated | Queries to run before `query`. This can be useful for granting permissions. |
| postOperations | [string](#string) | repeated | Queries to run after `query`. |
| materialized | [bool](#bool) |  | Applies the materialized view optimization, see https://cloud.google.com/bigquery/docs/materialized-views-intro. |
| partitionBy | [string](#string) |  | Optional. Applicable only to materialized view. The key by which to partition the materialized view. Typically the name of a timestamp or the date column. See https://cloud.google.com/bigquery/docs/materialized-views-create#partitioned_materialized_views. |
| clusterBy | [string](#string) | repeated | Optional. Applicable only to materialized view. The keys by which to cluster partitions by. See https://cloud.google.com/bigquery/docs/materialized-views-create#cluster_materialized_views. |
| description | [string](#string) |  | Description of the view. |
| columns | [ActionConfig.ColumnDescriptor](#sqlanvil-ActionConfig-ColumnDescriptor) | repeated | Descriptions of columns within the table. |
| labels | [ActionConfig.ViewConfig.LabelsEntry](#sqlanvil-ActionConfig-ViewConfig-LabelsEntry) | repeated | Key-value pairs for BigQuery labels. |
| additionalOptions | [ActionConfig.ViewConfig.AdditionalOptionsEntry](#sqlanvil-ActionConfig-ViewConfig-AdditionalOptionsEntry) | repeated | Key-value pairs of additional options to pass to the BigQuery API. Some options, for example, partitionExpirationDays, have dedicated type/validity checked fields. For such options, use the dedicated fields. |
| dependOnDependencyAssertions | [bool](#bool) |  | When set to true, assertions dependent upon any dependency will be add as dedpendency to this action |
| hermetic | [bool](#bool) |  | If true, this indicates that the action only depends on data from explicitly-declared dependencies. Otherwise if false, it indicates that the action depends on data from a source which has not been declared as a dependency. |
| assertions | [ActionConfig.TableAssertionsConfig](#sqlanvil-ActionConfig-TableAssertionsConfig) |  | Assertions to be run on the dataset. If configured, relevant assertions will automatically be created and run as a dependency of this dataset. |
| metadata | [ActionConfig.Metadata](#sqlanvil-ActionConfig-Metadata) |  | Metadata for this view. |
| reservation | [string](#string) |  | Optional. The BigQuery reservation to use for execution. If unset, the value from workflow_settings.yaml is used. If neither is set, default BigQuery behavior applies. sqlanvil CLI only (GCP sqlanvil support pending). |
| postgres | [PostgresOptions](#sqlanvil-PostgresOptions) |  | Postgres-native options. For a materialized view (materialized: true), `no_data` (CREATE ... WITH NO DATA) and `refresh_policy` (&#34;on_dependency_change&#34; → in-place REFRESH instead of drop&#43;recreate) apply; indexes also apply to materialized views. |






<a name="sqlanvil-ActionConfig-ViewConfig-AdditionalOptionsEntry"></a>

### ActionConfig.ViewConfig.AdditionalOptionsEntry



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| key | [string](#string) |  |  |
| value | [string](#string) |  |  |






<a name="sqlanvil-ActionConfig-ViewConfig-LabelsEntry"></a>

### ActionConfig.ViewConfig.LabelsEntry



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| key | [string](#string) |  |  |
| value | [string](#string) |  |  |






<a name="sqlanvil-ActionConfigs"></a>

### ActionConfigs
Action configs defines the contents of `actions.yaml` configuration files.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| actions | [ActionConfig](#sqlanvil-ActionConfig) | repeated |  |






<a name="sqlanvil-BigQueryConnection"></a>

### BigQueryConnection
BigQueryConnection — connection params for warehouse.kind = &#34;bigquery&#34;.
Mirrors the legacy flat fields on WorkflowSettings (default_project,
default_location, default_dataset) but namespaced under warehouse.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| project | [string](#string) |  | The Google Cloud project (database). |
| location | [string](#string) |  | BigQuery location, e.g. &#34;US&#34;, &#34;EU&#34;, &#34;europe-west4&#34;. |
| defaultDataset | [string](#string) |  | Default dataset (schema). |






<a name="sqlanvil-ConnectionConfig"></a>

### ConnectionConfig
A named connection: the warehouse (read/write target) or a read-only source.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| platform | [string](#string) |  | Optional. One of &#34;bigquery&#34;, &#34;postgres&#34;, &#34;supabase&#34;, &#34;mysql&#34;. (&#34;mysql&#34; is supported by `sqlanvil introspect` for schema scaffolding; a runtime cross-warehouse MySQL *source* bridge is not yet implemented — see issue #35.) |
| project | [string](#string) |  | Optional. BigQuery source default project. |
| dataset | [string](#string) |  | Optional. BigQuery source default dataset. |
| saKeyId | [string](#string) |  | Optional. Non-secret Vault secret id used in generated BigQuery FDW server DDL. |
| host | [string](#string) |  | Optional. Postgres source host (non-secret; password lives in .df-credentials.json). |
| port | [uint32](#uint32) |  | Optional. Postgres source port. |
| database | [string](#string) |  | Optional. Postgres source database name. |
| defaultSchema | [string](#string) |  | Optional. Postgres/Supabase default schema. |






<a name="sqlanvil-DefaultIcebergConfig"></a>

### DefaultIcebergConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| bucketName | [string](#string) |  | Optional. Bucket name used to construct a storage URI when creating an Iceberg table. |
| tableFolderRoot | [string](#string) |  | Optional. Table folder root used to construct a storage URI when creating an Iceberg table. |
| tableFolderSubpath | [string](#string) |  | Optional. Table folder subpath used to construct a storage URI when creating an Iceberg table. |
| connection | [string](#string) |  | Optional. The connection specifying the credentials to be used to read and write to external storage, such as Cloud Storage. |






<a name="sqlanvil-Environment"></a>

### Environment
A named environment (dev/staging/prod). Holds only NON-SECRET overrides plus a
pointer to a gitignored credentials file — never secrets themselves.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| schemaSuffix | [string](#string) |  |  |
| vars | [Environment.VarsEntry](#sqlanvil-Environment-VarsEntry) | repeated |  |
| defaultDatabase | [string](#string) |  |  |
| defaultLocation | [string](#string) |  |  |
| credentials | [string](#string) |  | Path (relative to the project dir) to this environment&#39;s gitignored .df-credentials file. |






<a name="sqlanvil-Environment-VarsEntry"></a>

### Environment.VarsEntry



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| key | [string](#string) |  |  |
| value | [string](#string) |  |  |






<a name="sqlanvil-MysqlConnection"></a>

### MysqlConnection



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| host | [string](#string) |  |  |
| port | [uint32](#uint32) |  |  |
| database | [string](#string) |  |  |
| user | [string](#string) |  |  |
| password | [string](#string) |  |  |
| sslMode | [string](#string) |  | SSL mode: &#34;disable&#34; | &#34;require&#34;. MySQL/MariaDB over TLS. |






<a name="sqlanvil-MysqlOptions"></a>

### MysqlOptions
MySQL/MariaDB table options &#43; secondary indexes. First cut: engine/charset/
collation and plain/unique secondary indexes. (No partitioning, FULLTEXT/
SPATIAL/prefix indexes, or row_format yet — those are later follow-ups.)


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| engine | [string](#string) |  | Storage engine, emitted as ENGINE=&lt;engine&gt; (e.g. &#34;InnoDB&#34;, &#34;MyISAM&#34;). Omit to use the server default. |
| charset | [string](#string) |  | Default character set, emitted as DEFAULT CHARSET=&lt;charset&gt; (e.g. &#34;utf8mb4&#34;). |
| collation | [string](#string) |  | Default collation, emitted as COLLATE=&lt;collation&gt; (e.g. &#34;utf8mb4_unicode_ci&#34;). |
| indexes | [MysqlOptions.Index](#sqlanvil-MysqlOptions-Index) | repeated |  |
| partition | [MysqlOptions.Partition](#sqlanvil-MysqlOptions-Partition) |  |  |






<a name="sqlanvil-MysqlOptions-Index"></a>

### MysqlOptions.Index



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  | Optional; when omitted, derived as &lt;table&gt;_&lt;cols&gt;_idx (or _key if unique), capped at 63 chars. |
| columns | [string](#string) | repeated |  |
| unique | [bool](#bool) |  |  |






<a name="sqlanvil-MysqlOptions-Partition"></a>

### MysqlOptions.Partition
Native MySQL/MariaDB partitioning. NB: MySQL requires every column used in the
partitioning expression to be part of every UNIQUE/PRIMARY key — so a partitioned
incremental table&#39;s `uniqueKey` must include the partition column(s).


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| kind | [MysqlOptions.Partition.Kind](#sqlanvil-MysqlOptions-Partition-Kind) |  |  |
| expression | [string](#string) |  | The expression/columns inside `PARTITION BY &lt;kind&gt; (...)`, emitted verbatim. For RANGE/LIST: a column or expression (e.g. &#34;id&#34;, &#34;YEAR(created_at)&#34;). For HASH/KEY: a column list. (RANGE COLUMNS / LIST COLUMNS not modeled in v1.) |
| partitions | [MysqlOptions.Partition.Bound](#sqlanvil-MysqlOptions-Partition-Bound) | repeated |  |
| count | [uint32](#uint32) |  | HASH/KEY partition count, emitted as `PARTITIONS &lt;n&gt;`. Ignored for RANGE/LIST. |






<a name="sqlanvil-MysqlOptions-Partition-Bound"></a>

### MysqlOptions.Partition.Bound
RANGE/LIST child partitions. `values` is the raw clause body after the name,
e.g. &#34;VALUES LESS THAN (2024)&#34; (range, use MAXVALUE for a catch-all) or
&#34;VALUES IN (&#39;us&#39;, &#39;ca&#39;)&#34; (list).


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  |  |
| values | [string](#string) |  |  |






<a name="sqlanvil-NotebookRuntimeOptionsConfig"></a>

### NotebookRuntimeOptionsConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| outputBucket | [string](#string) |  | Storage bucket to output notebooks to after their execution. |
| runtimeTemplateName | [string](#string) |  | Colab runtime template (https://cloud.google.com/colab/docs/runtimes), from which a runtime is created for notebook executions. |
| repositorySnapshotDestination | [RepositorySnapshotDestinationConfig](#sqlanvil-RepositorySnapshotDestinationConfig) |  | Storage URI to upload the snapshot to. For empty URI it defaults to the provided output_bucket. |






<a name="sqlanvil-PostgresConnection"></a>

### PostgresConnection
PostgresConnection — libpq-style connection params for
warehouse.kind = &#34;postgres&#34;. Standard Postgres host/port/database/user.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| host | [string](#string) |  |  |
| port | [uint32](#uint32) |  |  |
| database | [string](#string) |  |  |
| user | [string](#string) |  |  |
| password | [string](#string) |  |  |
| sslMode | [string](#string) |  | SSL mode: &#34;disable&#34; | &#34;allow&#34; | &#34;prefer&#34; | &#34;require&#34; | &#34;verify-ca&#34; | &#34;verify-full&#34;. See https://www.postgresql.org/docs/current/libpq-ssl.html. |
| defaultSchema | [string](#string) |  |  |






<a name="sqlanvil-PostgresOptions"></a>

### PostgresOptions
PostgresOptions — Postgres-native table-level options. Mirrors what
BigQueryOptions-style fields do in TableConfig but in idiomatic Postgres.

Used as a peer of the existing `bigquery: {...}` shape on action configs:
  publish(&#34;daily_orders&#34;, { postgres: { tablespace: &#34;fast_ssd&#34;, ... } })


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| tablespace | [string](#string) |  | Physical storage placement (CREATE TABLE ... TABLESPACE &lt;name&gt;). |
| fillfactor | [uint32](#uint32) |  | Storage parameter — fraction of each page to fill on insert (1-100). |
| unlogged | [bool](#bool) |  | CREATE UNLOGGED TABLE — faster writes, lost on crash. For staging/temp tables where durability isn&#39;t required. |
| partition | [PostgresOptions.Partition](#sqlanvil-PostgresOptions-Partition) |  |  |
| indexes | [PostgresOptions.Index](#sqlanvil-PostgresOptions-Index) | repeated |  |
| noData | [bool](#bool) |  | Materialized view: create WITH NO DATA (empty until first refresh). Default (false) is WITH DATA. Named for the non-default so proto3&#39;s false default means the sensible WITH DATA. |
| refreshPolicy | [string](#string) |  | Materialized view refresh on re-run: &#34;on_dependency_change&#34; refreshes an existing matview in place (REFRESH MATERIALIZED VIEW) instead of dropping &#43; recreating. Default (unset) drops &#43; recreates each run (safe — also picks up definition changes, which REFRESH does not). |






<a name="sqlanvil-PostgresOptions-Index"></a>

### PostgresOptions.Index
Indexes to create alongside the table.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  |  |
| columns | [string](#string) | repeated |  |
| method | [PostgresOptions.Index.Method](#sqlanvil-PostgresOptions-Index-Method) |  |  |
| where | [string](#string) |  | Partial index predicate (WHERE &lt;expr&gt;). |
| unique | [bool](#bool) |  |  |
| include | [string](#string) | repeated | INCLUDE non-key columns for covering indexes. |
| opclass | [string](#string) |  | Operator class applied to each indexed column, e.g. &#34;gin_trgm_ops&#34; (pg_trgm), &#34;jsonb_path_ops&#34;, or &#34;vector_l2_ops&#34; (pgvector). Required for gin/gist indexes on types without a default opclass. |






<a name="sqlanvil-PostgresOptions-Partition"></a>

### PostgresOptions.Partition
Native Postgres declarative partitioning.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| kind | [PostgresOptions.Partition.Kind](#sqlanvil-PostgresOptions-Partition-Kind) |  |  |
| columns | [string](#string) | repeated |  |
| partitions | [PostgresOptions.Partition.Bound](#sqlanvil-PostgresOptions-Partition-Bound) | repeated |  |
| includeDefault | [bool](#bool) |  | Also create a catch-all DEFAULT partition so rows outside every bound still insert (recommended for declarative full-refresh loads). |






<a name="sqlanvil-PostgresOptions-Partition-Bound"></a>

### PostgresOptions.Partition.Bound
Child partitions. `values` is the raw FOR VALUES clause body matching the
kind, e.g. &#34;FROM (&#39;2024-01-01&#39;) TO (&#39;2025-01-01&#39;)&#34; (range),
&#34;IN (&#39;us&#39;, &#39;ca&#39;)&#34; (list), or &#34;WITH (MODULUS 4, REMAINDER 0)&#34; (hash).


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) |  |  |
| values | [string](#string) |  |  |
| subPartition | [PostgresOptions.Partition](#sqlanvil-PostgresOptions-Partition) |  | Sub-partitioning: make this child a partitioned table in its own right (PARTITION BY ...), with its own nested `partitions`. Omit for a leaf child that holds rows directly. |






<a name="sqlanvil-RepositorySnapshotDestinationConfig"></a>

### RepositorySnapshotDestinationConfig



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| repositorySnapshotUri | [string](#string) |  | Storage URI to upload the repository snapshot to. |






<a name="sqlanvil-SupabaseConnection"></a>

### SupabaseConnection
SupabaseConnection — connection params for warehouse.kind = &#34;supabase&#34;.
Supabase projects expose a Postgres connection via project_ref &#43;
service_role_key, or a direct connection string for bypassing PostgREST.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| projectRef | [string](#string) |  | From the Supabase dashboard (project URL host before .supabase.co). |
| serviceRoleKey | [string](#string) |  | Project service-role JWT. NEVER commit literally — use ${ENV_VAR} interpolation in workflow_settings.yaml. |
| defaultSchema | [string](#string) |  |  |
| connectionString | [string](#string) |  | Optional override — direct Postgres URL bypassing the PostgREST proxy. e.g. &#34;postgresql://postgres:${PASSWORD}@db.&lt;project_ref&gt;.supabase.co:5432/postgres&#34;. If set, takes precedence over project_ref &#43; service_role_key for the direct DB connection. service_role_key is still used for RLS bypass. |






<a name="sqlanvil-SupabaseOptions"></a>

### SupabaseOptions
SupabaseOptions — Supabase-specific platform features layered on top of
standard Postgres. Used as a peer of `postgres: {...}` for projects
targeting `warehouse: { kind: supabase }`.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| postgres | [PostgresOptions](#sqlanvil-PostgresOptions) |  | Standard Postgres options apply. Set these via `postgres:` directly or nest under `supabase.postgres:` — either is accepted. |
| publishToRealtime | [bool](#bool) |  | ALTER PUBLICATION supabase_realtime ADD TABLE &lt;this&gt;. Implicitly sets REPLICA IDENTITY appropriately. |
| enableRls | [bool](#bool) |  | ALTER TABLE &lt;this&gt; ENABLE ROW LEVEL SECURITY. Note: only enables RLS — policies are declared via the `rlsPolicy` action type (see Phase 5). |
| ownerRole | [string](#string) |  | OWNER TO &lt;role&gt;. Typically &#34;postgres&#34; or &#34;service_role&#34;. |
| vectors | [SupabaseOptions.VectorConfig](#sqlanvil-SupabaseOptions-VectorConfig) | repeated |  |






<a name="sqlanvil-SupabaseOptions-VectorConfig"></a>

### SupabaseOptions.VectorConfig
pgvector convenience config. Equivalent to declaring a
PostgresOptions.Index with method=HNSW or method=GIST &#43; ivfflat ops,
but more ergonomic for RAG pipelines.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| column | [string](#string) |  |  |
| dimensions | [uint32](#uint32) |  |  |
| indexType | [SupabaseOptions.VectorConfig.IndexType](#sqlanvil-SupabaseOptions-VectorConfig-IndexType) |  |  |
| params | [SupabaseOptions.VectorConfig.ParamsEntry](#sqlanvil-SupabaseOptions-VectorConfig-ParamsEntry) | repeated | ivfflat: { lists }, hnsw: { m, ef_construction }. |






<a name="sqlanvil-SupabaseOptions-VectorConfig-ParamsEntry"></a>

### SupabaseOptions.VectorConfig.ParamsEntry



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| key | [string](#string) |  |  |
| value | [string](#string) |  |  |






<a name="sqlanvil-WarehouseConfig"></a>

### WarehouseConfig
WarehouseConfig — discriminated union over connection variants. The
`kind:` YAML tag selects which `oneof` arm is unmarshalled.

Example YAML:
  warehouse:
    kind: postgres
    host: db.example.com
    port: 5432
    database: analytics
    user: sqlanvil_writer
    password: ${PG_PASSWORD}
    ssl_mode: require
    default_schema: public


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| bigquery | [BigQueryConnection](#sqlanvil-BigQueryConnection) |  |  |
| postgres | [PostgresConnection](#sqlanvil-PostgresConnection) |  |  |
| supabase | [SupabaseConnection](#sqlanvil-SupabaseConnection) |  |  |
| mysql | [MysqlConnection](#sqlanvil-MysqlConnection) |  |  |






<a name="sqlanvil-WorkflowSettings"></a>

### WorkflowSettings
Workflow Settings defines the contents of the `workflow_settings.yaml`
configuration file.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| sqlanvilCoreVersion | [string](#string) |  | The desired sqlanvil core version to compile against. |
| defaultProject | [string](#string) |  | Required. The default Google Cloud project (database). |
| defaultDataset | [string](#string) |  | Required. The default dataset (schema). |
| defaultLocation | [string](#string) |  | Required. The default BigQuery location to use. For more information on BigQuery locations, see https://cloud.google.com/bigquery/docs/locations. |
| defaultAssertionDataset | [string](#string) |  | Required. The default dataset (schema) for assertions. |
| vars | [WorkflowSettings.VarsEntry](#sqlanvil-WorkflowSettings-VarsEntry) | repeated | Optional. User-defined variables that are made available to project code during compilation. An object containing a list of &#34;key&#34;: value pairs. |
| projectSuffix | [string](#string) |  | Optional. The suffix to append to all Google Cloud project references. |
| datasetSuffix | [string](#string) |  | Optional. The suffix to append to all dataset references. |
| namePrefix | [string](#string) |  | Optional. The prefix to append to all action names. |
| defaultNotebookRuntimeOptions | [NotebookRuntimeOptionsConfig](#sqlanvil-NotebookRuntimeOptionsConfig) |  | Optional. Default runtime options for Notebook actions. |
| builtinAssertionNamePrefix | [string](#string) |  | Optional. The prefix to append to built-in assertion names. |
| defaultIcebergConfig | [DefaultIcebergConfig](#sqlanvil-DefaultIcebergConfig) |  | Optional. Default config options for Iceberg tables. |
| disableAssertions | [bool](#bool) |  | Optional. Disables all assertions including built-in assertions (uniqueKey, nonNull, rowConditions) and manual assertions (type: assertion). When true, assertions will still be compiled but marked as disabled. |
| defaultReservation | [string](#string) |  | Optional. The default BigQuery reservation to use for execution. If unset, default BigQuery behavior applies. sqlanvil CLI only (GCP sqlanvil support pending). |
| extension | [Extension](#sqlanvil-Extension) |  | Optional. An external package that provides an extension. |
| includeTestsInCompiledGraph | [bool](#bool) |  | Optional. If set to true, unit tests will be included in the compiled graph. |
| warehouse | [string](#string) |  | Optional. The database warehouse to use, e.g. &#34;bigquery&#34;, &#34;postgres&#34;, &#34;supabase&#34;. |
| connections | [WorkflowSettings.ConnectionsEntry](#sqlanvil-WorkflowSettings-ConnectionsEntry) | repeated | Optional. Named connections (warehouse &#43; read-only sources). |
| environments | [WorkflowSettings.EnvironmentsEntry](#sqlanvil-WorkflowSettings-EnvironmentsEntry) | repeated | Optional. Named environments (dev/staging/prod) selected with `--environment`. |






<a name="sqlanvil-WorkflowSettings-ConnectionsEntry"></a>

### WorkflowSettings.ConnectionsEntry



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| key | [string](#string) |  |  |
| value | [ConnectionConfig](#sqlanvil-ConnectionConfig) |  |  |






<a name="sqlanvil-WorkflowSettings-EnvironmentsEntry"></a>

### WorkflowSettings.EnvironmentsEntry



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| key | [string](#string) |  |  |
| value | [Environment](#sqlanvil-Environment) |  |  |






<a name="sqlanvil-WorkflowSettings-VarsEntry"></a>

### WorkflowSettings.VarsEntry



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| key | [string](#string) |  |  |
| value | [string](#string) |  |  |





 


<a name="sqlanvil-ActionConfig-IcebergTableConfig-FileFormat"></a>

### ActionConfig.IcebergTableConfig.FileFormat
Supported file formats for BigQuery tables.

| Name | Number | Description |
| ---- | ------ | ----------- |
| FILE_FORMAT_UNSPECIFIED | 0 | Default value. |
| PARQUET | 1 | Apache Parquet format. |



<a name="sqlanvil-ActionConfig-LoadMode"></a>

### ActionConfig.LoadMode


| Name | Number | Description |
| ---- | ------ | ----------- |
| REPLACE_TABLE | 0 | Replace existing table (default). |
| APPEND | 1 | Insert into destination table. |
| MAXIMUM | 2 | Insert only records where the specified column value exceeds the existing maximum value in the destination table. |
| UNIQUE | 3 | Insert only records where the specified column value is not already present in the destination column values. |
| MERGE | 4 | Merge records into the destination table, deduplicating using 1&#43; unique keys |



<a name="sqlanvil-ActionConfig-OnSchemaChange"></a>

### ActionConfig.OnSchemaChange


| Name | Number | Description |
| ---- | ------ | ----------- |
| IGNORE | 0 | Ignore any schema changes (default). |
| FAIL | 1 | Fails if the query would result in a new column(s) being added, deleted, or renamed. |
| EXTEND | 2 | Does not block any new column(s) from being added. |
| SYNCHRONIZE | 3 | Does not block any new column(s) from being added, deleted or renamed. |



<a name="sqlanvil-MysqlOptions-Partition-Kind"></a>

### MysqlOptions.Partition.Kind


| Name | Number | Description |
| ---- | ------ | ----------- |
| RANGE | 0 |  |
| LIST | 1 |  |
| HASH | 2 |  |
| KEY | 3 |  |



<a name="sqlanvil-PostgresOptions-Index-Method"></a>

### PostgresOptions.Index.Method


| Name | Number | Description |
| ---- | ------ | ----------- |
| BTREE | 0 |  |
| HASH | 1 |  |
| GIN | 2 |  |
| GIST | 3 |  |
| BRIN | 4 |  |



<a name="sqlanvil-PostgresOptions-Partition-Kind"></a>

### PostgresOptions.Partition.Kind


| Name | Number | Description |
| ---- | ------ | ----------- |
| RANGE | 0 |  |
| LIST | 1 |  |
| HASH | 2 |  |



<a name="sqlanvil-SupabaseOptions-VectorConfig-IndexType"></a>

### SupabaseOptions.VectorConfig.IndexType


| Name | Number | Description |
| ---- | ------ | ----------- |
| IVFFLAT | 0 |  |
| HNSW | 1 |  |


 

 

 



## Scalar Value Types

| .proto Type | Notes | C++ | Java | Python | Go | C# | PHP | Ruby |
| ----------- | ----- | --- | ---- | ------ | -- | -- | --- | ---- |
| <a name="double" /> double |  | double | double | float | float64 | double | float | Float |
| <a name="float" /> float |  | float | float | float | float32 | float | float | Float |
| <a name="int32" /> int32 | Uses variable-length encoding. Inefficient for encoding negative numbers – if your field is likely to have negative values, use sint32 instead. | int32 | int | int | int32 | int | integer | Bignum or Fixnum (as required) |
| <a name="int64" /> int64 | Uses variable-length encoding. Inefficient for encoding negative numbers – if your field is likely to have negative values, use sint64 instead. | int64 | long | int/long | int64 | long | integer/string | Bignum |
| <a name="uint32" /> uint32 | Uses variable-length encoding. | uint32 | int | int/long | uint32 | uint | integer | Bignum or Fixnum (as required) |
| <a name="uint64" /> uint64 | Uses variable-length encoding. | uint64 | long | int/long | uint64 | ulong | integer/string | Bignum or Fixnum (as required) |
| <a name="sint32" /> sint32 | Uses variable-length encoding. Signed int value. These more efficiently encode negative numbers than regular int32s. | int32 | int | int | int32 | int | integer | Bignum or Fixnum (as required) |
| <a name="sint64" /> sint64 | Uses variable-length encoding. Signed int value. These more efficiently encode negative numbers than regular int64s. | int64 | long | int/long | int64 | long | integer/string | Bignum |
| <a name="fixed32" /> fixed32 | Always four bytes. More efficient than uint32 if values are often greater than 2^28. | uint32 | int | int | uint32 | uint | integer | Bignum or Fixnum (as required) |
| <a name="fixed64" /> fixed64 | Always eight bytes. More efficient than uint64 if values are often greater than 2^56. | uint64 | long | int/long | uint64 | ulong | integer/string | Bignum |
| <a name="sfixed32" /> sfixed32 | Always four bytes. | int32 | int | int | int32 | int | integer | Bignum or Fixnum (as required) |
| <a name="sfixed64" /> sfixed64 | Always eight bytes. | int64 | long | int/long | int64 | long | integer/string | Bignum |
| <a name="bool" /> bool |  | bool | boolean | boolean | bool | bool | boolean | TrueClass/FalseClass |
| <a name="string" /> string | A string must always contain UTF-8 encoded or 7-bit ASCII text. | string | String | str/unicode | string | string | string | String (UTF-8) |
| <a name="bytes" /> bytes | May contain any arbitrary sequence of bytes. | string | ByteString | str | []byte | ByteString | string | String (ASCII-8BIT) |

