* [sql表名不区分大小写](#sql_ignorecase)
* [打开操作日志](#enable_operate_log)
* [TIMESTAMP用法](#timestamp_usage)
* [insert支持自动剔重](#insert_without_duplicate)
* [null转默认值](#null_to_default)

---------------------------------------------------------------------
#### <span id="sql_ignorecase">sql表名不区分大小写</span>
1. 在`/etc/my.cnf`中的[mysqld]后添加添加`lower_case_table_names=1` 
2. 重启MYSQL服务. `/etc/init.d/mysqld restart` 

默认情况下： 
windows不区分大小写；linux区分大小写 

`lower_case_table_names`取值说明： 

* 0: 区分大小写 
* 1: 不区分大小写 有可能改完还是不起作用，解决措施： 表删除重建

查看是否生效：
```
show variables like "%case%";
```

注意：
对于docker下的mysql，应修改`/etc/mysql/conf.d/docker.cnf`文件

#### <span id="enable_operate_log">打开操作日志</span>
以管理员用户登录
```
set global log_output = 'TABLE';  --将日志打印至表general_log中
SET GLOBAL general_log=ON;  --打开操作日志
SET GLOBAL general_log=OFF;  --关闭操作日志
select * from general_log;  --查看操作日志
```

#### <span id="timestamp_usage">TIMESTAMP用法</span>
* 在创建新记录和修改现有记录的时候都对这个数据列刷新
`TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP`
* 在创建新记录的时候把这个字段设置为当前时间，但以后修改时，不再刷新它：
`TIMESTAMP DEFAULT CURRENT_TIMESTAMP`

时间格式：
<table border>
<tr><td>%Y</td><td>年，4 位</td></tr>
<tr><td>%y</td><td>年，2 位</td></tr>
<tr><td>%m</td><td>月，数值(00-12)</td></tr>
<tr><td>%M</td><td>月名</td></tr>
<tr><td>%d</td><td>月的天，数值(00-31)</td></tr>
<tr><td>%e</td><td>月的天，数值(0-31)</td></tr>
<tr><td>%H</td><td>小时 (00-23)</td></tr>
<tr><td>%h</td><td>小时 (01-12)</td></tr>
<tr><td>%i</td><td>分钟，数值(00-59)</td></tr>
<tr><td>%s</td><td>秒(00-59)</td></tr>
<tr><td>%p</td><td>AM 或 PM</td></tr>
<tr><td>%T</td><td>时间, 24-小时 (hh:mm:ss)</td></tr>
<tr><td>%r</td><td>时间，12-小时（hh:mm:ss AM 或 PM）</td></tr>
</table>

TIMESTAMP与LONG转换:

* long 转 timestamp：`FROM_UNIXTIME`
示例：
`FROM_UNIXTIME(1249488000)`

* timestamp转long：`UNIX_TIMESTAMP`
示例：
`UNIX_TIMESTAMP(buytime)`

DATE做偏移：
DATE_ADD(date,INTERVAL expr type)
DATE_SUB(date,INTERVAL expr type)
type值:MICROSECOND/SECOND/MINUTE/HOUR/DAY/WEEK/MONTH/QUARTER/YEAR/SECOND_MICROSECOND/MINUTE_MICROSECOND/MINUTE_SECOND/HOUR_MICROSECOND/HOUR_SECOND/HOUR_MINUTE/DAY_MICROSECOND/DAY_SECOND/DAY_MINUTE/DAY_HOUR/YEAR_MONTH

### <span id="insert_without_duplicate">insert支持自动剔重</span>
* 使用`insert ignore`进行数据插入。
* 使用SQL
```
INSERT INTO table_listnames (name, address, tele)
SELECT * FROM (SELECT 'Rupert', 'Somewhere', '022') AS tmp
WHERE NOT EXISTS (
    SELECT name FROM table_listnames WHERE name = 'Rupert'
) LIMIT 1;
```
进行数据插入

#### <span id="null_to_default">null转默认值</span>
select ifnull(null, 2)
