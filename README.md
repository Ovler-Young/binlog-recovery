# MySQL Binlog DML Recovery Tool

[English](README.md) | [中文](README.CN.md)

A Go language tool for recovering DELETE and UPDATE operations from MySQL binlog.

Forked from <https://github.com/bai1986/1204>, main modifications:

1. Updated regular expressions
2. Added special character handling
3. Added documentation
4. Added GitHub Actions and releases

## Features

This tool can extract deleted or updated data from MySQL binlog and generate corresponding recovery SQL statements:

- **DELETE Recovery**: Generate INSERT statements to recover deleted data
- **UPDATE Recovery**: Generate UPDATE statements to recover data to its previous state

## Build or Download from Release

To build:

```pwsh
git clone https://github.com/Ovler-Young/binlog-recovery.git
go build dml_recovery.go
```

## Usage Steps

### 1. Prepare Table Structure File

Create a table structure file in a format similar to CREATE TABLE statement, for example:

```mysql
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

### 2. Generate Binlog Text File

Use mysqlbinlog tool to generate readable binlog file:

#### Specify Time Range (Recommended)

```bash
mysqlbinlog   --verbose \
    --start-datetime="2024-01-23 10:00:00" \
    --stop-datetime="2024-01-23 11:00:00" \
    mysql-bin.00001 > binlog.log
```

#### Specify Position Range

```bash
mysqlbinlog --verbose \
    --start-position=start_position \
    --stop-position=end_position \
    mysql-bin.00001 > binlog.log
```

The `--verbose` flag is essential!!!

Note: mariadb-binlog is also supported.

### 3. Run Recovery Tool

Linux/MacOS

```bash
./binlog-recovery --meta=table_structure.txt --logfile=binlog.log --type=delete --out=recover_insert.sql
```

Windows

```bash
binlog-recovery.exe --meta=table_structure.txt --logfile=binlog.log --type=delete --out=recover_insert.sql
```

**Parameter Description:**

- `--meta`: Table structure file path
- `--logfile`: Binlog text file path
- `--type`: Recovery type (delete or update)
- `--out`: Output SQL file path

### 4. Execute Recovery SQL

```bash
# MySQL
mysql -u username -p database_name < recover_insert.sql

# MariaDB
mariadb -u username -p database_name < recover_insert.sql
```
