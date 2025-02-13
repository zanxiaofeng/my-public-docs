# 基本概念
## 数据库
- 
## 表
## 行
## 模式（Schema）
- 模式是数据库中的一个概念，可以将其理解为一个命名空间或目录，不同的模式下可以有相同名称的表、函数等对象而不会产生冲突。如果没有模式的概念，那么不同用户或者开发者在创建各自数据库对象命名时可能会发生冲突以及相互干扰等问题，有了模式，就可以避免这些问题。
- 一个数据库包含一个或者多个模式，模式中又包含了表、函数以及操作符等数据库对象。其中的层级关系是：数据库-模式-对象。需要注意的是，虽然能创建多个数据库实例，但不能同时访问不同数据库中的对象，当需要访问另一个数据库中的表或其他对象时，需要重新连接到这个数据库，而模式却没有此限制，一个用户在连接到一个数据库后，就可以同时访问这个数据库中多个模式的对象。
- public，系统默认为每个数据库创建的默认schema。
## 表空间
- 不同的数据库表空间有不同的定义。在 PostgreSQL 中，表空间允许在文件系统中定义数据库对象存储的位置，实质上就是指定一个目录。
- pg_default：系统默认创建的表空间，用来存储系统目录对象，用户表、用户表index、临时表、临时表index、内部临时表的默认空间。
- pg_global：系统默认创建的表空间，用来存储系统字典表。
# 锁

# pg_catalog
- pg_catalog.pg_database
- pg_catalog.pg_tables
- pg_catalog.pg_indexes
- pg_catalog.pg_constraint
- pg_catalog.pg_user_mapping
- pg_catalog.pg_views
- pg_catalog.pg_proc
- pg_catalog.pg_type
- pg_catalog.pg_locks
- pg_catalog.pg_stats
- pg_catalog.pg_roles
- pg_catalog.pg_cursors
- pg_catalog.pg_user
- pg_catalog.pg_group
- pg_catalog.pg_matviews
- pg_catalog.pg_prepared_statement
- pg_catalog.pg_prepared_xacts
- pg_catalog.pg_timezone_names
- pg_catalog.pg_timezone_abbrevs
- pg_catalog.pg_stat_database
- pg_catalog.pg_stat_activity
- pg_catalog.pg_stat_all_tables
- pg_catalog.pg_stat_sys_tables
- pg_catalog.pg_stat_user_tables
- pg_catalog.pg_stat_all_indexes
- pg_catalog.pg_stat_sys_indexes
- pg_catalog.pg_stat_user_indexes

# information_schema
- information_schema.tables
- information_schema.views
- information_schema.role_table_grants


# 执行计划

# 索引

