---
title: database/sql, gorm
tags: [go]
---

go中连接操作数据库的基本库`database/sql`，以及字节开源的orm库`gorm`

## database/sql

> 善用文档: [sql package - database/sql ](https://pkg.go.dev/database/sql)

> 字节青训营文档：[设计模式之 database sql与 GORM实践.pdf - 飞书云文档 (feishu.cn)](https://bytedance.feishu.cn/file/boxcnct7Jc8Td2Oolfbm6t4HfJg)

### 设计思想

为不同数据库提供统一的连接接口和操作接口的方式，同时提供对连接池配置

![image-20220516171737879](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220516171737879.png)

> 定位类似java中的`jdbc`

1. 连接接口
   - 使用dsn连接
   - 使用`connector`结构体连接
2. 操作接口
   - 连接类型
     - 直连Conn
     - 预编译Stmt
     - 事务Tx
   - 返回数据操作
     - Exec / ExecContext -> Result
     - Query/QueryContex -> Rows(Columns)
     - QueryRow/QueryRowContex -> Row(Rows简化)

### 使用

1. import数据库driver

   - 需要import到`_`，来注册driver

2. 连接数据库

   1. 使用`dsn`

      - dsn: data source name，[Data source name - Wikipedia](https://en.wikipedia.org/wiki/Data_source_name)

      - go中dsn格式

        ```
        username:password@protocol(address)/dbname?param=value
        ```

      - 详见[go-sql-driver/mysql: Go MySQL Driver is a MySQL driver for Go's (golang) database/sql package (github.com)](https://github.com/go-sql-driver/mysql#dsn-data-source-name)

   2. 使用`connector`结构体（推荐）

3. 配置连接

4. 执行sql，得到结构体类型返回值

5. 从返回值将数据Scan到变量

实列

```go
// 使用dsn连接
dsn := "qing:123456@tcp(118.202.10.86:3306)/community"
db, err := sql.Open("mysql", dsn)

// 推荐连接方式，使用connector结构体
connector, err := mysql.NewConnector(&mysql.Config{
    User:   "qing",
    Passwd: "123456",
    Net:    "tcp",
    Addr:   "118.202.10.86",
    DBName: "community",
})
db := sql.OpenDB(connector)

if err != nil {
    println(err.Error())
}
defer db.Close()

//配置连接
db.SetConnMaxLifetime(0)
db.SetMaxIdleConns(50)
db.SetMaxOpenConns(50)

sql := "select id, username from user where id = ?"
rows, err := db.Query(sql, "1")
if err != nil {
    println(err.Error())
}
defer rows.close()

// 获取结果
for rows.Next() {
    var id int
    var name string
    err := rows.Scan(&id, &name)
    if err != nil {
        println(err.Error())
    } else {
        println(id, name)
    }
}

if rows.Err() != nil {
    / ...
}
```

### 原理

获取到db对象后，它是怎么查询sql的

```go
//操作过程伪代码，默认重试2次
for i:=0; i < maxBadConnRetries; i++ {
    // 通过连接池获取或通过driver新建连接
    dc, err := db.conn(ctx, strategy)
    	// 有空闲连接 -> reuse => max life time
    	// 新建连接 -> max open ...
    
    // 连接放回连接池
    defer dc.db.putConn(dc, err, true)
    	// 校验连接是否有错误
    
    // 执行sql
    if err == nil {
        err = dc.ci.Query(sql, args...)
    }
    
    isBadConn = errors.Is(err, driver.ErrBadConn)
    if !isBadConn {
        break
    }
}
```

driver如何注册

```go
// Driver 接口
type Driver interface {
	// Open returns a new connection to the database.
	Open(name string) (Conn, error)
}

//注册全局driver
//在全局变量drivers中添加一个driver
//重名会panic
func Register(name string, driver driver.Driver) {
	driversMu.Lock()
	defer driversMu.Unlock()
	if driver == nil {
		panic("sql: Register driver is nil")
	}
	if _, dup := drivers[name]; dup {
		panic("sql: Register called twice for driver " + name)
	}
	drivers[name] = driver
}

// mysql注册driver
func init() {
	sql.Register("mysql", &MySQLDriver{})
}
```

## GORM

> [GORM - The fantastic ORM library for Golang, aims to be developer friendly.](https://gorm.io/)

1. sql生成

   ![image-20220517173049393](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220517173049393.png)

   - Chain methods + Finisher Method
   - Chain: 参数生成子句(clauses)添加到statement中
   - Finisher: 将clauses取出编译成最终sql，执行
     - 会检查数据库类型，来生成对应的sql

2. 插件

   ![image-20220517173110724](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220517173110724.png)

3. ConnPool

   ![image-20220517173128725](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220517173128725.png)
   
   - 实现多数据库访问
   - 预编译sql


4. Dialector	![image-20220517173807404](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220517173807404.png)
