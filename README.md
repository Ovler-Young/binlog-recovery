# MySQL Binlog DML Recovery Tool

一个用于从MySQL binlog恢复DELETE和UPDATE操作的Go语言工具。

Fork自 <https://github.com/bai1986/1204>，主要修改：更新正则表达式，增加特殊字符处理

## 功能说明

本工具可以从MySQL binlog中提取被删除或更新的数据，并生成相应的恢复SQL语句：

- **DELETE恢复**：生成INSERT语句来恢复被删除的数据
- **UPDATE恢复**：生成UPDATE语句来恢复被修改前的数据

## 编译程序

```pwsh
go build recovery.go
```

## 使用步骤

### 1. 准备表结构文件

创建表结构文件，格式类似CREATE TABLE语句格式，如：

```
`video_static`
`aid` bigint(20)
`bvid` varchar(50)
`pubdate` int(11)
`title` varchar(255)
`description` text
`tag` text
`pic` varchar(255)
`type_id` int(11)
`user_id` bigint(20)
`priority` int(11)
```

### 2. 生成binlog文本文件

使用mysqlbinlog工具生成可读的binlog文件：

#### 指定时间范围(推荐)

```bash
mysqlbinlog   --verbose \
    --start-datetime="2024-01-23 10:00:00" \
    --stop-datetime="2024-01-23 11:00:00" \
    mysql-bin.00001 > binlog.log
```

#### 指定位置范围

```bash
mysqlbinlog --verbose \
    --start-position=开始位置 \
    --stop-position=结束位置 \
    mysql-bin.00001 > binlog.log
```

其中 `--verbose` 不可缺少！！！

注意，mariadb-binlog也可行。

### 3. 运行恢复工具

```bash
# 恢复DELETE操作 (生成INSERT语句)
./recovery.exe --meta=table_structure.txt --logfile=binlog.log --type=delete --out=recover_insert.sql

# 恢复UPDATE操作 (生成另一个UPDATE语句)
./recovery.exe --meta=table_structure.txt --logfile=binlog.log --type=update --out=recover_update.sql
```

**参数说明：**

- `--meta`: 表结构文件路径
- `--logfile`: binlog文本文件路径
- `--type`: 恢复类型 (delete 或 update)
- `--out`: 输出SQL文件路径

### 4. 执行恢复SQL

```bash
# MySQL
mysql -u username -p database_name < recover_insert.sql

# MariaDB
mariadb -u username -p database_name < recover_insert.sql
```
