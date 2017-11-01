# Lookup JDBC(join)查询

> 本文档主要说明如何通过Tindex的lookup-jdbc查询实现join关系型数据库字段的查询（理论上支持所有通过jdbc可以访问的数据库，目前测试支持`MySQL`，`Postgres`数据库）。

***注：一个lookup只能关联一个字段，如有多个字段需创建多个lookup***

 - [创建Lookup-JDBC](#create)

 - [GroupBy查询使用Looup-JDBC](#groupBy)

 - [过滤(FisrtN)查询使用Looup-JDBC](#fisrtN)

## <a id="create" href="create"></a> 1. 创建lookup，通过jdbc驱动从MySQL数据库中读取数据

发送post请求到:
`http://<CoordinatorIP>:8081/druid/coordinator/v1/lookups/__default/<LookupName>`

**`CoordinatorIP:`** Tindex集群内coordinator服务节点ip

**`LookupName:`** 定义Lookup的名称,如jdbc_1001（需保证唯一性）

post参数如下：
```json
{
    "type": "cachingLookup",
    "version": "abcdefg",
    "dataLoader": {
        "type": "jdbc",
        "connectorConfig": {
            "connectURI": "jdbc:mysql://192.168.0.110:3306/hive_druid?useSSL=false",
            "user": "root",
            "encrypted": false,
            "password": "123456"
        },
        "query": "SELECT userId, name FROM user WHERE userId IS NOT NULL AND userId <> '' AND name IS NOT NULL AND name <> ''",
        "groupId": "<LookupName>",
        "loadPeriod": "PT5M",
        "rowLimit": 100
    }
}
```

关键参数说明：  

**`version:`** lookup创建版本,修改配置信息后需要修改版本号才会生效。

**`dataLoader.connectorConfig.connectURI:`** 关系型数据库连接字符串（包括数据库）。

**`dataLoader.connectorConfig.user:`** 关系型数据库账号名称。

**`dataLoader.connectorConfig.password:`** 关系型数据库账号密码。

**`dataLoader.connectorConfig.encrypted:`** 密码是否加密。

  > 数据库连接密码默认不加密，如果需要加密，需要设置`encrypted=true`。目前默认使用DES加密算法。  

**`dataLoader.query:`** 关联查询的SQL，SQL中只能查出两个字段，第一个字段作为key，第二个字段作为value，其他字段丢弃。  

   > SELECT规则：第一列是跟Tindex关联的字段列，第二列为映射显示的字段列
   
   > SQL查询Key，Value必须要做非空处理

**`dataLoader.groupId:`**  定义Lookup的名称，最好和前面的lookup name一样，如jdbc_1001，并且具有唯一性。

**`dataLoader.loadPeriod:`** 周期性从数据库中加载数据频率粒度，可以按业务场景设置加载周期，比如：`PT1H`每小时, `P1D`每天等。

**`dataLoader.rowLimit:`**  加载关联数据行数限制，默认10000行，最大支持100w。

## 2. 使用Lookup-JDBC查询Tindex数据

### <a id="groupBy" href="groupBy"></a> GroupBy查询使用lookup-jdbc

在groupby查询中使用LookupJdbc，只需要配置name属性即可。除了name属性，也可以配置lookup属性，两者只能配一个。 查询JSON如下：

- 关联字段（UserID）为字符串类型时如下：

```json
{
	"queryType": "lucene_groupBy",
	"dataSource": "events",
	"intervals": "1000/3000",
	"granularity": "all",
	"context": {
		"timeout": 600000,
		"useOffheap": true,
		"maxResults": 1000000
	},
	"dimensions": [{
	  "type": "lookup",                       // 关联维度为字符串时为:lookup
		"dimension": "UserID",                  // Tindex里(关联)维度名
		"outputName": "UserId",
    "retainMissingValue": "false",
    "replaceMissingValueWith": "未匹配",     // 设置未关联数据的分组名
	  "name": "jdbc_1001"                           // 指定查询使用的lookup名称
	}],
	"aggregations": [{
		"name": "count()",
		"type": "lucene_count"
	}],
	"limitSpec": {
		"type": "default",
		"columns": [{
			"dimension": "UserId",
      "dimensionOrder":"alphanumeric"
		}],
    "limit":100
	}
}
```

- 关联字段（numId）为数值类型时如下：

```json
{
	"queryType": "lucene_groupBy",
	"dataSource": "events",
	"intervals": "1000/3000",
	"granularity": "all",
	"context": {
		"timeout": 600000,
		"useOffheap": true,
		"maxResults": 1000000
	},
	"dimensions": [{
		"type": "numericGroupBy",               // 关联维度为数值时为:numericGroupBy
		"dimension": "numId",                   // Tindex里(关联)维度名
		"outputName": "numId",
    "maxCardinality":100000,
    "retainMissingValue": "false",
    "replaceMissingValueWith": "未匹配",     // 设置未关联数据的分组名
		"name": "numName"                       // 指定查询使用的lookup名称
	}],
	"aggregations": [{
		"name": "count()",
		"type": "lucene_count"
	}],
	"limitSpec": {
		"type": "default",
		"columns": [{
			"dimension": "numId",
      "dimensionOrder": "alphanumeric"
		}],
    "limit":100
	}
}
```

### <a id="fisrtN" href="fisrtN"></a> filter过滤中使用lookup-jdbc

```json
{
  "queryType": "lucene_firstN",
  "dataSource": "events",
  "intervals": "2017-05-22T03:06:11Z/2017-05-23T03:06:12Z",
  "granularity": "all",
  "context": {
    "timeout": 60000,
    "groupByStrategy": "v2",
  },
  "filter": {
    "type": "in",
    "dimension": "UserID",                  // Tindex里(关联)维度名
    "values": [
      "user001"                             // 按关联维度值过滤查询
    ], 
    "extractionFn": {
      "type": "registeredLookup",
      "lookup": "<jdbc_lookupName>",         // 指定查询使用的lookup名称
      "retainMissingValue": false,
      "replaceMissingValueWith": "未匹配",    // 设置未关联数据的分组名
      "optimize": true
    }
  },
  "dimension": {
    "type": "lookup",
    "dimension": "UserID",
    "outputName": "mysql_t",
    "retainMissingValue": false,
    "replaceMissingValueWith": "未匹配",
    "name": "<jdbc_lookupName>"
  },
  "aggregations": [
    {
      "name": "count",
      "type": "lucene_count"
    }
  ],
  "formatted": true,
  "threshold": 10
}
```
