## 1.EmEditor删除空行
> 正则替换：^[\s\t]*\n
## 2.Oracle SQLPlus上下左右删除乱码
    yum install rlwrap
    yum install readline-devel
    修改.bash_profile，使用同义词更换系统命令使用同义词更换系统命令
    vim /home/oracle/.bash_profile  
    #stty erase ^H
    #结尾增加  
    alias sqlplus='rlwrap sqlplus'  
    alias rman='rlwrap rman'  
## 3.Oracle数据导出
### （1）创建目录
    mkdir dataImp  
    chown -R oracle:oinstall dataImp
    create directory impdp_dir as '/u01/dataImp';
    grant read,write on directory impdp_dir to 【用户名】;
### （2）导出
    1)导出Schema
    expdp scott/tiger@orcl schemas=scott dumpfile=expdp.dmp directory=dump_dir;
    2)导出表
    expdp scott/tiger@orcl tables=emp,dept dumpfile=expdp.dmp directory=dump_dir;
    3)按查询条件导
    expdp scott/tiger@orcl directory=dump_dir dumpfile=expdp.dmp tables=empquery='where deptno=20';
    4)按表空间导
    expdp system/manager@orcl directory=dump_dir dumpfile=tablespace.dmptablespaces=temp,example;
    5)导整个数据库
    expdp system/manager@orcl directory=dump_dir dumpfile=full.dmp full=y;
### （3）导入
    1)导入用户（从用户scott导入到用户scott）
    impdp scott/tiger@orcl directory=dump_dir dumpfile=expdp.dmp schemas=scott;
    2)导入表（从scott用户中把表dept和emp导入到system用户中）
    impdp system/manager@orcl directory=dump_dir dumpfile=expdp.dmptables=scott.dept,scott.emp remap_schema=scott:system;
    3)导入表空间
    impdp system/manager@orcl directory=dump_dir dumpfile=tablespace.dmp tablespaces=example;
    4)导入数据库
    impdb system/manager@orcl directory=dump_dir dumpfile=full.dmp full=y;
    5)追加数据
    impdp system/manager@orcl directory=dump_dir dumpfile=expdp.dmp schemas=systemtable_exists_action
## 4.Oracle导出txt
### （1）main.sql
```sql
set linesize 200 
set term off verify off feedback off pagesize 999 
set markup html on entmap ON spool on preformat off
spool ./tables.txt
@./get_tables.sql
spool off
exit
```
### （2）get_tables.sql
```sql
select table_name from all_tables where table_name like '%CWBASE%';
```
### （3）执行
```sql
sqlplus / as sysdba @./main.sql
```
