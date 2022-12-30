# EXPORT

## 功能

该语句用于将指定表的数据导出到指定位置。

这是一个异步操作，任务提交成功后返回结果。执行后可使用 `SHOW EXPORT` 命令查看进度。

## 语法

```sql
EXPORT TABLE table_name
[PARTITION (partition_name[, ...])]
[(column_name[, ...])]
TO export_path
[opt_properties]
WITH BROKER;
```

## 参数说明

- `table_name`：导出数据所在的表，目前支持导出 `engine` 为 `olap` 或 `mysql` 的表。
- `partition_name`：要导出的分区，如不指定则默认导出表中所有分区的数据。
- `column_name`：要导出的列。列的导出顺序可以和源表结构 (schema) 不同，如不指定则默认导出表中所有列的数据。
- `export_path`：导出的路径。如果是目录，需要以斜杠结尾。否则最后一个斜杠的后面部分会作为导出文件的前缀。如不指定文件名前缀，文件名前缀默认为 **data_**。
- `opt_properties`：导出相关的属性配置。

    语法：

    ```sql
    [PROPERTIES ("key"="value", ...)]
    ```

    配置项：
  - `column_separator`: 指定导出的列分隔符，默认为 `\t`。
  - `line_delimiter`: 指定导出的行分隔符，默认为 `\n`。
  - `load_mem_limit`: 导出在单个 BE 节点的内存使用上限，默认为 2 GB，单位为字节。
  - `timeout`：导出作业的超时时间，单位：秒。默认值为 `86400`（1 天）。
  - `include_query_id`: 导出文件名中是否包含 `query_id`，默认为 `true`，表示包含。

- `WITH BROKER`：在 StarRocks v2.4 及以前版本，用于指定 Broker 的名称，格式为 `WITH BROKER "<broker_name>"`。自 StarRocks v2.5 起，只保留 `WITH BROKER` 关键字，不再需要提供 `broker_name`。

## 示例

### 将表中所有数据导出到HDFS

将 `testTbl` 表中的所有数据导出到 HDFS 上。

```sql
EXPORT TABLE testTbl 
TO "hdfs://hdfs_host:port/a/b/c/" 
WITH BROKER ("username"="xxx", "password"="yyy");
```

### 将表中部分分区数据导出到HDFS

将 `testTbl` 表中`p1`和`p2`分区数据导出到 HDFS 上。

```sql
EXPORT TABLE testTbl PARTITION (p1,p2) 
TO "hdfs://hdfs_host:port/a/b/c/" 
WITH BROKER ("username"="xxx", "password"="yyy");
```

### 将表中所有数据导出到 HDFS 并指定分隔符

1.将 `testTbl` 表中的所有数据导出到 HDFS 上，以 `,` 作为列分隔符。

```sql
EXPORT TABLE testTbl 
TO "hdfs://hdfs_host:port/a/b/c/" 
PROPERTIES ("column_separator"=",") 
WITH BROKER ("username"="xxx", "password"="yyy");
```

2.将 `testTbl` 表中的所有数据导出到 HDFS 上，以 Hive 默认分隔符 `\x01` 作为列分隔符。

```sql
EXPORT TABLE testTbl 
TO "hdfs://hdfs_host:port/a/b/c/" 
PROPERTIES ("column_separator"="\\x01") 
WITH BROKER;
```

### 指定导出文件名前缀

将 `testTbl` 表中的所有数据导出到 HDFS 上，指定导出文件前缀为 `testTbl_`。

```sql
EXPORT TABLE testTbl 
TO "hdfs://hdfs_host:port/a/b/c/testTbl_" 
WITH BROKER;
```

### 导出数据到 OSS 上

将 `testTbl` 表中的所有数据导出到 OSS。

```sql
EXPORT TABLE testTbl 
TO "oss://oss-package/export/"
WITH BROKER
(
"fs.oss.accessKeyId" = "xxx",
"fs.oss.accessKeySecret" = "yyy",
"fs.oss.endpoint" = "oss-cn-zhangjiakou-internal.aliyuncs.com"
);
```

### 导出数据到 COS 上

将 `testTbl` 表中的所有数据导出到 COS 上。

```sql
EXPORT TABLE testTbl 
TO "cosn://cos-package/export/"
WITH BROKER
(
"fs.cosn.userinfo.secretId" = "xxx",
"fs.cosn.userinfo.secretKey" = "yyy",
"fs.cosn.bucket.endpoint_suffix" = "cos.ap-beijing.myqcloud.com"
);
```

### 导出数据到 S3 上

将 `testTbl` 表中的所有数据导出到 S3 上。

```sql
EXPORT TABLE testTbl 
TO "s3a://s3-package/export/"
WITH BROKER
(
"fs.s3a.access.key" = "xxx",
"fs.s3a.secret.key" = "yyy",
"fs.s3a.endpoint" = "s3-ap-northeast-1.amazonaws.com"
);
```
