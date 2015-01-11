# Setup MariaDB

MariaDB, a MySQL drop-in replacement. MariaDB is a community-developed fork of the MySQL relational database management system.

## Installing MariaDB

```
sudo yum install mariadb-server mariadb
sudo systemctl start mariadb
sudo mysql_secure_installation
```
The prompt will ask you for your current root password. Since you just installed MySQL, you most likely won’t have one, so leave it blank by pressing enter. 
Then the prompt will ask you if you want to set a root password. Go ahead and enter Y, and follow the instuctions:

```
sudo systemctl enable mariadb.service
```

## Backup and Restore SQL Database

### Backup:
```
mysqldump -u root -p[root_password] [database_name] > dumpfilename.sql
```

### Restore:
```
mysql -u root -p[root_password] [database_name] < dumpfilename.sql
```

