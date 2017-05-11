# Tindex 查询接口使用文档

Tindex的原生查询接口是HTTP REST风格查询方式，还有其它客户库查询方式比如：[sugo-plyql](developer/interfaces/sugo-plyql.md)  

使用HTTP REST风格查询（`Broker`, `Historical`, 或者 `Realtime`）节点数据。

查询参数为JSON格式，每个节点类型都会暴露相同的REST查询接口。

对于正常的Tindex操作，应该向`Broker`节点发出查询。
查询请求示例如下：

```shell
 curl -X POST '<queryable_host>:<port>/druid/v2/?pretty' -H 'Content-Type:application/json' -d @<query_json_file>
```

 `queryable_host: ` Broker节点ip:8082 (`http://192.168.0.200:8082`)  

 `query_json_file: ` 查询参数详见[Tindex-Query-Json](/developer/query/query.md)部分

- [Tindex-Query-Json](/developer/query/query.md) 属性详情如下：
  - [`dataSource`](/developer/query/datasource.md)
  - [`dimension`](/developer/query/dimension.md)
  - [`interval`](/developer/query/interval.md)
  - [`filter`](/developer/query/filter.md)
  - [`extraction-fn`](/developer/query/extraction-fn.md)
  - [`aggregation`](/developer/query/aggregation.md)
  - [`post-aggregation`](/developer/query/post-aggregation.md)
  - [`having`](/developer/query/having.md)