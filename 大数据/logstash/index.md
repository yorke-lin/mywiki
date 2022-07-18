# 安装启动
## 配置文件
**数据管道配置**
```sh
input {
	stdin {}
    jdbc {
        # 表类型区分，每个jdbc模块需对应一个type；
        type => "activist_site"
        # 是否清除last_run_metadata_path的记录，需要增量同步时此字段必须为false；
        clean_run => false
        # 同步频率(分 时 天 月 年)，默认每分钟同步一次；
        schedule => "* * * * *"
        # 数据库连接地址
        jdbc_connection_string => "jdbc:mysql://127.0.0.1:3306/ynxb_show?useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=convertToNull&useSSL=true&serverTimezone=GMT%2B8"
        # 数据库用户名
        jdbc_user => "root"
        # 数据库密码
        jdbc_password => "123"
        # MySQL驱动依赖包路径 
        jdbc_driver_library => "D:/SDK/elastic/logstash-7.12.1/config/jdbc/mysql-connector-java-8.0.26.jar"
        # MySQL驱动类
        jdbc_driver_class => "com.mysql.cj.jdbc.Driver"
        # 数据库重连尝试次数
        connection_retry_attempts => "3"
        # 判断数据库连接是否可用，默认false不开启
        jdbc_validate_connection => "true"
        # 数据库连接可用校验超时时间，默认3600S 
        jdbc_validation_timeout => "3600"
        # 开启分页查询（默认false不开启）；
        jdbc_paging_enabled => "true"
        # 单次分页查询条数默认100000
        jdbc_page_size => "10000"
        # 查询数据sql
        statement => "select * from activist_site s where s.update_time >= :sql_last_value order by s.update_time asc"
        # 是否将字段名转换为小写，默认true
        lowercase_column_names => false
        # sql日志级别
        sql_log_level => warn
        # 是否记录上次执行结果，true表示会将上次执行结果的tracking_column字段的值保存到last_run_metadata_path指定的文件中
        record_last_run => true
        # 需要记录查询结果某字段的值时，此字段为true
        use_column_value => true
        # 需要记录的字段，用于增量同步，需是数据库字段
        tracking_column => "update_time"
        # 记录字段类型只有数字类型(numeric)和时间戳(timestamp)
        tracking_column_type => timestamp
        # record_last_run上次数据存放位置
        last_run_metadata_path => "D:/SDK/elastic/logstash-7.12.1/config/jdbc/last_ynxb_admin_activist_site.txt"
    }
    # 其他表配置省略
}
    
filter {
    json {
        source => "message"
        remove_field => ["message"]
    }
}

output {
    # 对应jdbc块中的type
    if [type] == "activist_site" {
        elasticsearch {
            # es 地址
            hosts => ["127.0.0.1:9200"]
            # 索引名字，必须小写
            index => "activist_site"
            # 数据唯一索引
            document_id => "%{id}"
        }
    }
    # 其他表配置省略
}
```
## 启动
`安装路径/bin/logstash -f 配置文件路径`
