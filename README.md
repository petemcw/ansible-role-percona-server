# Percona Server

This role installs [Percona Server](http://www.percona.com/software/percona-server)
which is an enhanced, drop-in MySQL replacement. The role also installs the Percona
XtraBackup tool and the popular `mysqltuner` script.

The default variables are currently setup for moderate InnoDB MySQL usage on a
system with at least 2GB of memory.

## Requirements

This role requires [Ansible](http://www.ansibleworks.com/) version 1.4 or higher
and the Debian/Ubuntu platform.

## Role Variables

The variables that can be passed to this role and a brief description about
them are as follows (additional variables are available in the source):

```yaml
# Server variables
mysql_utf8: true
mysql_port: 3306
mysql_skip_networking: false
mysql_bind_address: '0.0.0.0'
mysql_max_connections: 100
mysql_back_log: 50
mysql_connect_timeout: 15
mysql_wait_timeout: 600

# General tuning variables
mysql_myisam_recover_options: 'BACKUP,FORCE'
mysql_bulk_insert_buffer_size: '16M'
mysql_key_buffer_size: '64M'
mysql_join_buffer_size: '2M'
mysql_max_allowed_packet: '16M'
mysql_max_heap_table_size: '16M'
mysql_myisam_max_sort_file_size: '8G'
mysql_myisam_sort_buffer_size: '16M'
mysql_open_files_limit: 65535
mysql_query_cache_limit: '2M'
mysql_query_cache_min_res_unit: 8192
mysql_query_cache_size: '64M'
mysql_query_cache_type: 1
mysql_read_buffer_size: '1M'
mysql_read_rnd_buffer_size: '2M'
mysql_sort_buffer_size: '1M'
mysql_table_definition_cache: 4096
mysql_table_open_cache: 4096
mysql_thread_cache_size: 8
mysql_thread_stack: '256K'
mysql_tmp_table_size: '16M'

# Logging variables
mysql_general_log: 0
mysql_general_log_file: '/var/log/mysql.log'
mysql_log_error: '/var/log/mysql/mysql-errors.log'
mysql_log_queries_not_using_indexes: 1
mysql_long_query_time: 10
mysql_slow_query_log: 1
mysql_slow_query_log_file: '/var/log/mysql/mysql-slow.log'

# Replication settings
mysql_binlog_format: 'MIXED'
mysql_expire_logs_days: 7
mysql_log_bin: '/var/log/mysql/mysql-bin'
mysql_max_binlog_size: '1G'

# InnoDB variables
mysql_innodb_additional_mem_pool_size: '8M'
mysql_innodb_buffer_pool_instances: 1
mysql_innodb_buffer_pool_size: '256M'
mysql_innodb_data_file_path: 'ibdata1:1G;ibdata2:512M:autoextend'
mysql_innodb_file_per_table: ~
mysql_innodb_flush_log_at_trx_commit: 1
mysql_innodb_flush_method: 'O_DIRECT'
mysql_innodb_lock_wait_timeout: 60
mysql_innodb_log_buffer_size: '16M'
mysql_innodb_log_file_size: '128M'
mysql_innodb_log_files_in_group: 2

```

### Extra Configuration

```yaml
# The HipChat API token used for new password notifications (optional)
mysql_hipchat_token: false

# The HipChat room used for new password notifications (optional)
mysql_hipchat_room: false

# The databases to create
mysql_databases:
  - name: 'staging_db'
  - name: 'production_db'

# The database users to create
mysql_users:
  - name: 'staging_user'
    pass: 'Secret!'
    priv: 'staging_db.*:ALL'
    host: 'localhost'
  - name: 'production_user'
    pass: 'Secret!'
    priv: 'production_db.*:ALL'
    host: 'localhost'
```

## Examples

1) Install Percona server with defaults

    ```yaml
    ---
    # This playbook installs Percona Server

    - name: Apply Percona Server to all db nodes
      hosts: db_servers
      roles:
        - percona-server
    ```

2) Install Percona server with custom variables

    ```yaml
    ---
    # This playbook installs Percona Server

    - name: Apply Percona Server to all db nodes
      hosts: db_servers
      roles:
        - { role: percona-server,
            mysql_innodb_buffer_pool_size: '2G'
          }
    ```

3) Install Percona server with defaults and create 2 databases and 2 users

    ```yaml
    ---
    # This playbook installs Percona Server

    - name: Apply Percona Server to all db nodes
      hosts: db_servers
      roles:
        - { role: percona-server,
            mysql_innodb_buffer_pool_size: '2G',
            mysql_databases: [
              {name: 'production_db'},
              {name: 'staging_db'}
            ],
            mysql_users: [
              {name: 'production_user', password: 'Secret1', priv: 'magento_production.*:ALL'},
              {name: 'staging_user', password: 'Secret1', priv: 'magento_staging.*:ALL'}
            ]
          }
    ```

## Dependencies

The following packages may be required for Debian derivatives:

* `aptitude`
* `python-apt`
* `python-mysqldb`

## License

MIT.
