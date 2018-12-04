# 1204
binlog dml recovery
需要手工粘贴表结构到文件中
需要手工通过mysqlbinlog --start-position= --stop-position mysql-bin.00001 > tmp.log
./dml_recovery --meta=表结构文件 --logfile= tmp.log --type=(delete|update) --out=rr.sql
