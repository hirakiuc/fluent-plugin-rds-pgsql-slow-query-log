# Amazon RDS for PostgreSQL log input plugin for fluentd

## Overview
- Amazon Web Services RDS log input plugin for fluentd
- Forked from [fluentd-plugin-rds-pgsql-log](https://github.com/shinsaka/fluent-plugin-rds-pgsql-log)
- Add slow query duration parsing.
- Modify time to ISO 8601 format for ELK

## Installation

    $ fluentd-gem install fluent-plugin-rds-pgsql-slow-query-log

## AWS ELB Settings
- settings see: [PostgreSQL Database Log Files](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.PostgreSQL.html)

## When SSL certification error
log:
```
SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed
```
Do env setting follows:
```
SSL_CERT_FILE=/etc/ssl/certs/ca-bundle.crt (If you using amazon linux)
```

## Configuration

```config
<source>
  type rds_pgsql_slow_query_log
  # required
  region                 <region name>
  db_instance_identifier <instance identifier>
  # optional if you can IAM credentials
  access_key_id          <access_key>
  secret_access_key      <secret_access_key>
  # optional
  refresh_interval       <interval number by second(default: 30)>
  tag                    <tag name(default: rds-pgsql.log>
  pos_file               <log getting position file(default: rds-pgsql.log)>
</source>
```

### Example setting
```config
<source>
  type rds_pgsql_log
  region ap-northeast-1
  db_instance_identifier test-postgres
  access_key_id     XXXXXXXXXXXXXXXXXXXX
  secret_access_key xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  refresh_interval  30
  tag pgsql.log
  pos_file /tmp/pgsql-log-pos.dat
</source>

<match pgsql.log>
  type stdout
</match>
```

### json output example
```

{"time":"2015-10-04T05:55:10+00:00",
 "host":"192.168.30.175(53092)",
 "user":"testuser",
 "database":"db1",
 "pid":"9769",
 "message_level":"LOG",
 "message":"  statement: select 1;",
 "log_file_name":"error/postgresql.log.2015-05-30-02",
 "duration": 10.2,
 "sql_text": select 1;"
}
```

