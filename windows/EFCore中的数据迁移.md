# EFCore中的数据迁移
EF Core的迁移 (Migration), 它以代码的形式记录对数据模型的更改，并能够将这些更改安全地同步到数据库，同时保留现有数据.

使用迁移的场景分两种: 
1. 创建数据库: 当项目从开发环境迁移到生产环境时, 用于创建数据库
2. 升级数据库: 随着系统的迭代, 数据模型也会发生变化, 这时需要同步数据库的结构. 迁移可以将数据模型和数据库结构同步到最新版本. 

## 迁移工具
使用EFCore的迁移功能需要安装EF Core .NET 命令行接口 (CLI) 工具
``` bash
dotnet tool install --global dotnet-ef
```

## 创建迁移
在项目的根目录下运行以下命令来创建迁移
``` bash
dotnet ef migrations add InitialCreate
```
InitialCreate 是我们给迁移起的名字.   
默认情况下, 该命令会在项目根目录下创建一个 Migrations 文件夹, 并生成相应的迁移类.  

要想命令能正常运行, 项目需要满足下面任意一条:
- 项目是否存在 IDesignTimeDbContextFactory<TContext> 工厂类。
- 是否可以从项目的 DI 容器中解析 DbContextOptions<TContext>。
- 继承DbContext的子类是否存在无参构造函数，且在 OnConfiguring 中配置了数据库提供程序和连接字符串。   

否者会报如下错误:   
`"Unable to resolve service for type 'DbContextOptions<TDbContext>' while attempting to activate 'TDbContext'."`

### 迁移类
这些自动生成的迁移类都在 项目名.Migrations 命名空间下. 他们会被编译到项目中.

当项目中使用 DbContext.Database.Migrate() 方法时，EFCore 通过反射的方式调用这些迁移类，将数据库升级到最新版本。当然也可以通过命令的方式创建. 

## 应用迁移
有两种方式可以把创建的迁移应用到数据库中.
1. 使用命令: 在控制台中运行以下命令来应用迁移
``` bash
dotnet ef database update
```
2. 使用代码: 在应用程序的启动代码中调用 DbContext.Database.Migrate() 方法来应用迁移

## 迁移回滚
如果需要撤销更改，可以使用 `dotnet ef database update <TargetMigrationName>` 命令将数据库回退到之前的某个特定迁移版本。若想回退到空状态（即删除所有数据库对象），可以使用 dotnet ef database update 0


## 命令总结
| 命令 |   用途 | 示例 |
| --- | --- | --- |
| dotnet ef migrations add | 根据模型变更创建新的迁移文件 | dotnet ef migrations add ProductTable |
| dotnet ef database update | 将所有未应用的迁移更新到数据库 | dotnet ef database update |   
| dotnet ef database update name | 将数据库回滚（或更新）到指定迁移版本 | dotnet ef database update InitialCreate |
| dotnet ef migrations remove | 删除最近一次创建的迁移（如果未应用） | dotnet ef migrations remove |
