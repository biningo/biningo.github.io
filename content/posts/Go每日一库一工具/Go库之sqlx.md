---
gtitle: Go库之sqlx
date: 2021-06-11
categories: [Go每日一库一工具]
tags: [Go]
draft: true
---

## sqlx和sql

`sqlx`是github上开源的外部库，基于`database/sql`封装，很多操作比内置sql库更加方便

> https://github.com/jmoiron/sqlx

由于sqlx是依赖sql库的，所以也需要加载数据库驱动，sqlx方便的是能更好的帮我们将查询出来的数据库和struct对应起来，这样就不需要我们一个个去`Scan`了，减少了重复的代码量

​    

## 创建数据库连接对象

我们可以通过`sql.DB`对象创建`sqlx.DB`

```go
func initMySql() *sql.DB {
	connector, _ := mysql.NewConnector(&mysql.Config{
		Addr:                 "localhost" + ":" + "3306",
		User:                 "root",
		Passwd:               "55555",
		DBName:               "lyer",
		Collation:            "utf8_general_ci",
		ParseTime:            true,
		Loc:                  time.Local,
		AllowNativePasswords: true,
	})
	db := sql.OpenDB(connector)
	if err := db.Ping(); err != nil {
		panic(err)
	}
	return db
}
```

```go
func initSqlXDB() *sqlx.DB {
	return sqlx.NewDb(initMySql(), "mysql") //第二个参数是驱动的名字
}
```

​    

## sql连接参数优化

`sql.DB`对象只是一个连接参数对象，并不是一个实际的TCP连接，实际的TCP连接只有在真正查询SQL语句的时候才会通过`sql.DB`对象的参数进行创建

### 1、SetMaxOpenConns

### 2、SetMaxIdleConns

### 3、SetConnMaxLifetime

​        

## 常用方法概览

```go
//DBx.Exec()
//DBx.NamedExec() * 传入数组可进行批量插入
//DBx.MustExec() *

//DBx.Query()
//DBx.QueryRow()
//DBx.NamedQuery() *
//DBx.Queryx() *
//DBx.QueryRowx() *

//DBx.Get() 单条数据
//DBx.Select() 多条数据

//DBx.Prepare()
//DBx.Preparex() *
//DBx.PrepareNamed() *

//DBx.Begin()
//DBx.BeginTx()
//DBx.Beginx() *
//DBx.BeginTxx() *
//DBx.MustBegin() *
//DBx.MustBeginTx() *
```

​    

## struct注解

此注解可以让sqlx方便和数据库映射字段

```go
type Tag struct {
	ID    int    `db:"id"`
	Title string `db:"title"`
}
```

​     

## 查询数据

### 1、单条查询

`QueryRowx`  和 `sql.QueryRow`

只是多了映射结构体、map等数据结构，这样就不需要我们主动调用`Scan`方法了

```go
rx := DBx.QueryRowx("select id,title from tag where title=?", "rust")
rx := DBx.NamedQuery("select id,title from tag where title=:name", map[string]interface{}{"name":"go"})
rx.Scan(&tag);
```

`Get` 则只能映射结构体

```go
var tgo Tag
DBx.Get(&tgo, "select id,title from tag where title=?", "go")
```

### 2、多条查询

`Queryx` 和 `sql.Query`

```go
rsx, _ := DBx.Queryx("select id,title from tag")
for rsx.Next() {
	//....
}
```

`NamedQuery` 支持name传参数，可以传入struct或map

```go
rsx, _ = DBx.NamedQuery("select id,title from tag where title<:title", map[string]interface{}{"title": "rust"})
for rsx.Next() {
	rsx.StructScan(&tgo)
    log.Println(tgo)
}
```

`Select` 只能传入数组，一次即可查询全部

```go
tags := []Tag{}
DBx.Select(&tags, "select id,title from tag")
```

> 多条查询需要注意的就是查询完毕之后需要调用`Rows`的`Close`方法关闭连接，否则查询次数多了连接都被占满了就无法进行查询了

​     

## 插入更新

插入一条

```go
DBx.NamedExec(
    "insert into tag(title) value(:title)",
    map[string]interface{}{
        "title": "go",
    })
```

也可以使用占位符的方式

```go
DBx.Exec("insert into tag(title) value(?)","go")
```

这里只说明如何进行批量插入，如果我们使用官方的sql库的话我们就需要自己通过循环来破解sql语句，但是sqlx提供了方便的API让我们不需要自己拼接

```go
func main(){
    tags := []Tag{
		{Title: "go"},
		{Title: "java"},
		{Title: "rust"},
		{Title: "linux"},
		{Title: "mysql"},
		{Title: "redis"},
		{Title: "nginx"},
	}
    //一下子插入全部
	_, err := DBx.NamedExec("insert into tag(title) values(?)", tags)
}
```

如果是in查询的话则需要处理一下

```go
func main(){
    //args是处理之后拼接起来的参数，bidss数组
    bids:=[]int{1,2,3,4}
    boilsSql, args, err := sqlx.In(
		"SELECT id,tag_id,user_id,create_time,content FROM boil_boil WHERE id in (?) ORDER BY create_time DESC", bids)
	if err = global.G_DB.Select(&boils, boilsSql, args); err != nil {
		return []model.Boil{}, err
	}
}
```

​    

## sql查询一些注意的点

- 注意根据业务进行设置最大链接线程、空闲线程数等
- 每次查询完毕之后注意关闭连接，否则会占用连接
- 使用`?`占位符来进行查询可以避免SQL注入，而不是使用`Sprintf`进行破解SQL
- 如果一个sql需要反复查询执行的可以使用SQL预编译可以提升查询的效率，还可以防止SQL注入

​    

## 参考

[[译]配置 sql.DB 获得更好的性能(2020年更新版)](https://colobu.com/2020/05/18/configuring-sql-DB-for-better-performance-2020/)

[sqlx库使用指南](https://www.liwenzhou.com/posts/Go/sqlx/#autoid-0-0-0)

[Illustrated guide to SQLX](http://jmoiron.github.io/sqlx/) 【官方文档】

