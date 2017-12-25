# sugo-plyql路线图


以下是暂不支持的功能列表：  
  - groupBy 查询不支持数值维度
  - [TIME_BUCKET](/developer/interfaces/sugo-plyql.html#TIME_BUCKET)函数暂不支持除时间序列（__time）维度以外的其他date类型的groupBy查询。例如(暂不支持):
    - `SELECT  TIME_BUCKET(mydate, P1D, "Asia/Shanghai")
        FROM com_HyoaKhQMl_project_BkTc9Eb3g
        GROUP BY TIME_BUCKET(mydate, P1D, "Asia/Shanghai");`
  - 视图、存储过程、数据库insert、update、delete暂不支持
  - WHERE子句中的子查询。例如(暂不支持)：
    - `select * from A where A.id in (select id from B );`
  - JOIN查询语句

 # 问题与支持

  发送邮件到 cs@tingyun.com 或提出详细的issue，我们的进步，离不开各界的反馈。