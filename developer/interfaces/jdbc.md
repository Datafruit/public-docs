
# JAVA JDBC驱动调用

先启动sugo-plyql的mysql 网关模式:
```
./bin/plyql -h 192.168.0.212 [--druid-time-attribute time --force-boolean isNew] --experimental-mysql-gateway 13307
```

- [MySQL驱动下载地址](https://search.maven.org/#search%7Cgav%7C1%7Cg:%22mysql%22%20AND%20a:%22mysql-connector-java%22)
- 经测试成功的版本：
  - mysql-connector-java-5.1.30.jar以上或者mysql-connector-java-6.0.x.jar 

JAVA JDBC调用如下：
```java
package sugo;

import java.sql.SQLException;
import java.sql.DriverManager;
import java.sql.Connection;
import java.sql.Statement;
import java.sql.ResultSet;

class DruidQuery
{
  public static void main(String[] args) throws SQLException
  {
    try {
      String conStr = "jdbc:mysql://192.168.0.212:13307/plyql1";
      Connection con = DriverManager.getConnection(conStr, "root", "");
      Statement stmt = con.createStatement();
      ResultSet rs = stmt.executeQuery(
        "SELECT DATE_FORMAT(`time`, '%Y-%m-%d 00:00:00') AS `Time`, `channel` AS `Channel`, `isNew` AS IsNew, SUM(count) AS Count, SUM(`added`)/100 AS 'Added' FROM wikipedia GROUP BY DATE_FORMAT(`time`, '%Y-%m-%d 00:00:00'), channel, isNew ORDER BY Count DESC LIMIT 5"
      );

      while (rs.next()) {
      	Timestamp time = rs.getTimestamp("Time");
        String channel = rs.getString("Channel");
        long count = rs.getLong("Count");
        float added = rs.getFloat("Added");
        System.out.println(String.format("Time[%s] Channel[%s] Count[%d] Added[%f]", time.toString(), channel, count, added));
      }
      System.out.println("done");
    } catch (SQLException s) {
      s.printStackTrace();
    }
  }
}
```

## mysql 客户端
```
mysql --host=127.0.0.1 --port=13307 -e "SELECT 1+1"
```
具体的函数支持或者调用请看[Sugo Plyql 使用文档](/developer/interfaces/sugo-plyql.md)
