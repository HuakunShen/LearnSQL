# Data Migration

`mysqldump` command can be used to export data.

Suppose you are using a docker container, run the following

```bash
# Backup
docker exec CONTAINER /usr/bin/mysqldump -u root --password=root DATABASE > backup.sql

# Restore
cat backup.sql | docker exec -i CONTAINER /usr/bin/mysql -u root --password=root DATABASE

# Another way to restore, without docker
mysql -u [user] -p [database_name] < [filename].sql
```

## Reference

- https://gist.github.com/spalladino/6d981f7b33f6e0afe6bb