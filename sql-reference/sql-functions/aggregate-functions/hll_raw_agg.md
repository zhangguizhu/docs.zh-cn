# HLL_RAW_AGG

## 功能

此函数为聚合函数，用于聚合 hll 类型字段，并且返回的还是 hll 类型。

## 语法

```Haskell
hll_raw_agg(hll)
```

## 参数说明

`hll`: 通过其它列或者导入数据里面的数据生成的 hll 列。

## 返回值说明

返回值为 hll 类型。

## 示例

```Plain
mysql> select k1, hll_cardinality(hll_raw_agg(v1)) from tbl group by k1;
+------+----------------------------------+
| k1   | hll_cardinality(hll_raw_agg(`v1`)) |
+------+----------------------------------+
|    2 |                                4 |
|    1 |                                3 |
+------+----------------------------------+
```