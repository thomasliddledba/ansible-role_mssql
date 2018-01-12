# Role Name - ansible-role_mssql
This role will install SQL Server 2017 `latest` on RHEL

# Requirements
1. RHEL/CentOS 7
2. <a href="https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup-tools">sqlcmd (SQL Server Command Line Tool)</a> Installed on Test Host <sup>1<sup>
3. Choose Product Edition (see `Editions` Section)
4. Vagrant 1.9+
5. Ansible 2.2+
6. Read and understand License/Privacy Terms:
> The license terms and privacy statement for this product can be found in </br>
> * https://go.microsoft.com/fwlink/?LinkId=855864&clcid=0x409</br>

# Role Variables
| Variable | Description | Default |
| --- | --- | --- |
| sa_password | Setting the initial SA Password.<sup>2</sup> | Prompted or set in vars/main.yml |
| accept_eula | Indicates you accept the EULA for SQL Server.  Accepts `Yes` or `No`.  Is case-senstive. | No |
| edition | The edition of SQL Server you want to install.  Chose from the instances aboven <sup>3</sup>| Prompted or set in vars/main.yml |
| memorylimitMB | Max Memory Limit in MegaBytes (MB) | 4096 |
| tcpport | SQL Server Port on the VM's IP | 1433 |
| traceflags | SQL Server Trace Flags (i.e 1234 5678 [separated by spaces for each flag]) | Set in vars/main.yml |
| capturecoredump | SQL Server Core Dump setting [ true or false] | True |
| coredumptype | SQL Server Core Dump type [Only takes effect if 'capturecoredump' is true] | mini |
| backupcompression | Enable/Disable Backup Compression [ 1=true or 0=False] | 1 |
| backupchecksumdefault | Use the backup checksum default setting to enable or disable backup checksum during backup and restore. | 0 |
| maxservermemory | Max Server Memory (MB) for SQL Server.  MSSQL requires at miniumum 3025. | 4096 |
| minservermemory | Min Server Memory (MB) for SQL Server.  MSSQL starts with miniumim needed. | 1024 |
| maxdop | Max Degree of Parrellism | 0 |
| defaulttrace | Default Trace for SQL Server [default=0 (false)] | 0 |
| datadir | Create the target directory for new database data files. | /mssql-data/data <sup>5</sup>|
| adhocdistributedqueries | Ad hoc distributed queries use the OPENROWSET and OPENDATASOURCE functions to connect to remote data sources that use OLE DB. | 1 |
| logdir | Create the target directory for new database log files. | /mssql-log/log <sup>5</sup>|
| auditdir | Create a target directory for new Local Audit logs | /mssql-audit/audit <sup>5</sup>|


## Core Dump Type Information
 | Type 	| Description |
 | --- | --- |
 | mini | Mini is the smallest dump file type. It uses the Linux system information to determine threads and modules in the process. The dump contains only the host environment thread stacks and modules. It does not contain indirect memory references or globals. |
 | miniplus | MiniPlus is similar to mini, but it includes additional memory. It understands the internals of SQLPAL and the host environment. |
 | filtered | Filtered uses a subtraction-based design where all memory in the process is included unless specifically excluded. The design understands the internals of SQLPAL and the host environment, excluding certain regions from the dump. |
 | full 	| Full is a complete process dump that includes all regions located in /proc/$pid/maps. This is not controlled by coredump.captureminiandfull setting. |

# Example Playbook
## Sample
The example below is a sample playbook to install MSSQL Server for Linux on RHEL

    - hosts: mssql_linux_servers
      roles:
         - ansible-role_mssql
    -  vars_prompt:
         - name: sa_password
           prompt: "Enter the SA Password for this instance"
           private: yes
         - name: accept_eula
           value: Yes|No
> Note: the `accept_eula` and `sa_password` are case-senstive

## Testing with Vagrant for local development
### Requirements
1. Vagrant 1.9+
2. Ansible 1.2+
3. Git Client

### How to run the test playbook
1.  Open a terminal session on your local machine
2.  Type in the following
```bash
$> git clone https://github.com/thomasliddledba/ansible-role_mssql.git
```
3.  Type in the following
```bash
$> cd ansible-role_mssql
```
4.  Type in the following to bring up a vm with SQL Server for Linux Installed
```bash
$> vagrant up
```
5.  Test with sqlcmd from your local machine.  Here you will verify version of database, create a new database, and ensure it is created in the correct directories.
```sql
$> sqlcmd -S 127.0.0.1,11443 -U sa -P <sa_password variable>
1> select @@version
2> go
1> create database [testdb]
2> go
1> select physical_name from sys.master_files where database_id=DB_ID('testdb') and type in (0,1);
2> go
```

```console
physical_name                                                                                                                              
-------------------------------------------------------------------------------------------------------------------------------------------
/mssql-data/data/testdb.mdf                                                                                                              
/mssql-log/log/testdb_log.ldf 
```

# Author Information
Thomas Liddle
 - www.thomasliddledba.com
 - Facebook: http://fb.tldba.co
 - Twitter: https://twitter.com/thomasliddledba
 - YouTube: http://yt.tldba.co

# Other Information
## Editions
1) Evaluation (free, no production use rights, 180-day limit)
2) Developer (free, no production use rights)
3) Express (free)
4) Web (PAID)
5) Standard (PAID)
6) Enterprise (PAID)
7) Enterprise Core (PAID)
8) I bought a license through a retail sales channel and have a product key to enter. *** NOT SUPPORTED ****  <sup>4<sup>

## Footnotes
<sup>1</sup> URL for sqlcmd<br>
<sup>2</sup> In Interactive mode you will be prompted for a SA password if the var_prompt is in the playbook.  In non-Interactive mode the variable sa_password must be passed as an extra variable. <br>
<sup>3</sup> Refer to the section "Editions" to find all the editions supported for this playbook<br>
<sup>4</sup> Entering a product key in this version is currently not supported at this time.<br>
<sup>5</sup> You can use external NAS/SAN to mount disks to these diretories before the installation.  The installation will recongize they already exist and permission them accordingly.