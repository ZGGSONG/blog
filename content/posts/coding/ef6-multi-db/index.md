---
title: "Ef6 多数据库实现 CodeFirst | C#"
date: 2023-02-10T10:59:52+08:00
description: ""
featuredImage: ""
categories:
- coding
tags:
- csharp
- entifyframework
---

## 前言

遇到一个需求需要在 C#下同时连接多种数据库，记录学习 C# 连接 PostgreSql 和 SqlServer 并实现 CodeFirst

## 理论

### Enable-Migrations

由于在使用ef6进行数据库连接时都是通过`Enable-Migrations`来设置多个数据库上下文的迁移文件路径的，进行了如下命令输入：

```shell
Enable-Migrations -ContextTypeName ef_demo.PgSqlCodeFirstDbContext -MigrationsDirectory Magrations\pgsql
```
> 其中`\` 不能打错否则发生错误  

**ContextTypeName**：*DbContext*的位置，即 DbContext 的命名空间加上 DbContext 的名称  
**MigrationsDirectory**：需要迁移到的目标文件夹  

同样另一个数据上下文进行设置

```shell
Enable-Migrations -ContextTypeName ef_demo.SqlServerCodeFirstDbContext -MigrationsDirectory Magrations\sqlserver
```

### Add-Migrations

通过运行`Add-Migrations`命令

> 注意：通过`Add-Migrations`命令会自动选择唯一的*DbContext*, 但是如果有多个*DbContext*, 必须指定需要对哪个*DbContext*进行修改

```shell
Add-Migration -ConfigurationTypeName ef_demo.Migrations.pgsql.Configuration InitPgsqlDb
```

这里通过`-ConfigurationTypeName`指定*Configuration*  后面接着是*Configuration*的路径，然后接着是我们取得名字*InitPgsqlDb*  
EF 会找到指定的 Configuration, 并添加 XXXX_InitPgsqlDb

### Update-Database

最后通过`Update-Database`进行更新，需要指定需要更新哪个*Configuration*

```shell
Update-Database -ConfigurationTypeName ef_demo.Migrations.pgsql.Configuration
```

## 命令详情

```shell
Enable-Migrations
 -ContextTypeName EmployeeDBContext   #context 类型
 -MigrationsDirectory CreateMigrations       #自定义数据迁移文件夹
 -ProjectName 1-3EntityFrameWorkDemo   #数据迁移类将被添加到哪个项目中
 -StartUpProjectName 1-3EntityFrameWorkDemo   #数据库连接字符串配置文件 所在的项目
 -ContextProjectName 1-3EntityFrameWorkDemo   #context 类所在项目
 -ConnectionStringName EmployeeDBContext      #数据库连接字符串 名称
 -Force   #多次运行，是否重写迁移文件
 -Verbose  #详细信息
```

```shell
Add-Migration
 -Name MyFirstMigration  #名称
 -Force                  #强制执行，也就是说多次运行，会覆盖之前文件
 -ProjectName 1-3EntityFrameWorkDemo  #指定数据迁移类所在项目名称
 -StartUpProjectName 1-3EntityFrameWorkDemo  #数据库连接字符串所在项目名称
 -ConfigurationTypeName EntityFrameWorkDemo.CreateMigrations.Configuration #指定数据迁移配置类
 -ConnectionStringName EmployeeDBContext  #指定数据库连接字符串
 -Verbose  #详细信息
```

```shell
Update-Database
-ProjectName 1-3EntityFrameWorkDemo  指定 migration configuration 所在项目
-StartUpProjectName 1-3EntityFrameWorkDemo #指定数据库连接字符串所在项目
-ConfigurationTypeName EntityFrameWorkDemo.CreateMigrations.Configuration  #指定 Configuration 类
-ConnectionStringName EmployeeDBContext  #数据库连接字符串
-Force  #数据丢失被允许
-Verbose #详细信息
```

## 实战

### 开发环境

- Windows 10
- Visual Studio 2019
- C# 7.0
- .Net FrameWork 4.5.2

### 需求

1. 需要连接 SqlServer
2. 需要连接 PostgreSql

### 库

- EntityFramework
- EntityFramework6.Npgsql

### 示例代码

分别建立对应的 model，这边采用 CodeFirst 的方式，如果需要 DBFirst 请查询 [官方教程](https://www.entityframeworktutorial.net/) 随后创建 Context 继承 DBContext

```C#
public class CodeFirstDbContext : DbContext
{
	public CodeFirstDbContext()
		: base(new SqlConnection(
			new SqlConnectionStringBuilder()
			{
				DataSource = "localhost",
				InitialCatalog = "test",
				UserID = "sa",
				Password = "123456",
			}.ConnectionString), true)
	{

	}
	private static SqlConnection SqlConnection()
	{
		var builder = new SqlConnectionStringBuilder();
		builder.DataSource = "localhost";
		builder.InitialCatalog = "test";
		builder.UserID = "sa";
		builder.Password = "123456";
		return new SqlConnection(builder.ConnectionString);
	}

	public virtual DbSet<EntityModel.ControlDataEntity> ControlData { get; set; }
	public virtual DbSet<EntityModel.V_MonitorOnlineEntity> V_MonitorOnline { get; set; }
}
```

```C#
public class CodeFirstPostgreSqlDbContext : DbContext
{
	public CodeFirstPostgreSqlDbContext()
	: base("Server=172.17.209.6;Database=test3; User Id=root;Password=123456;")
	{

	}

	public virtual DbSet<EntityPostgreSQLModel.OEEAlarmInfosEntity> OEEAlarmInfos { get; set; }
	public virtual DbSet<EntityPostgreSQLModel.ReportAlarmStatisticsEntity> ReportAlarmStatistics { get; set; }
}
```

> 注意： 上述连接多个数据库时，配置中默认使用 npgsql 连接，所以在连接 sqlserver 的时候需要手动通过 ConnectionStringBuilder 构造指定连接类型

使用如下

```C#
#if false
	Task.Run(() =>
	{
		using (var db = new CodeFirstDbContext())
		{
			try
			{
				db.ControlData.Add(new Model.EntityModel.ControlDataEntity
				{
					CH = "123",
					InstrumentID = 112,
					AlarmValueLow = 456,
					Unit = "I don't known",
					Field1 = "789",
				});
				db.SaveChanges();

				db.ControlData.ToList().ForEach(x => Console.WriteLine(x.CH + "\t" + x.AlarmValueLow));
			}
			catch (Exception ex)
			{
				System.Diagnostics.Debug.Print(ex.Message);
			}
		}
	});
#endif

#if false
	Task.Run(() =>
	{
		using (var db = new CodeFirstPostgreSqlDbContext())
		{
			try
			{
				db.OEEAlarmInfos.Add(new Model.EntityPostgreSQLModel.OEEAlarmInfosEntity { Code = "test3" });
				db.SaveChanges();

				db.OEEAlarmInfos.ToList().ForEach(x => Console.WriteLine(x.Code));
			}
			catch (Exception ex)
			{
				System.Diagnostics.Debug.Print(ex.Message);
			}
		}

	});
#endif
```

## 参考

[「# 通过 Migration 在 EF6 中用多个 DbContext」](https://www.cnblogs.com/Jack-Blog/p/4699596.html)