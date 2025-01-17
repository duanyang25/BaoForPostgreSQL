0. Only Postgresql 12 works under the current implementation. The VM version of loading dataset does not work. I installed an Ubuntu 22.04 system, set up Postgresql 12 locally and manually loaded the dataset. 
```shell
# https://stackoverflow.com/a/26735105
# loggin Postgresql for the first time
sudo gedit /etc/postgresql/12/main/pg_hba.conf
```
Then you can set the `postgres` user by `psql -U postgres` to log in and add the `imdb` user and create the `imdb` database.   
I also change the line of pg_hba.conf from `local   all             all                                md5` to `local   all             all                                trust` to allow the access to the database `imdb` and the user `imdb` without password. (Do not forget to create the `imdb` user and its `imdb` database after you log in postgresql as `postgres` user.)
```shell
# edit shared_buffers
sudo gedit /etc/postgresql/12/main/postgresql.conf
```
Download the dump file from Harvard Dataverse https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/2QYZBT
```shell
# https://stackoverflow.com/a/61016512
# load it
pg_restore -d imdb -v -Fc foo.backup
```
1. On Makefiles under the `pg_extension` folder:   
Since the implementation of "Bao" targets Postgresql 12, we need to change 
```shell
PG_CONFIG = pg_config
```
to
```shell
PG_CONFIG = /usr/lib/postgresql/12/bin/pg_config
```
in order to find the correct header file of Postgresql 12. (postgresql-server-dev-13 14 15 have different implementations of header files, which are not supported after testing)

2. Since we did not use the VM to load the dataset, the `postgresql.conf` on our local machine locates at 
```shell
/etc/postgresql/12/main/postgresql.conf
```
So we need to use 
```shell
echo "shared_preload_libraries = 'pg_bao'" >> /etc/postgresql/12/main/postgresql.conf
```
or manually append `shared_preload_libraries = 'pg_bao'` at the end of `/etc/postgresql/12/main/postgresql.conf` file.
to load the built extension from above operation.    
3. In order to install `torch==1.5.0+cpu` from `-f https://download.pytorch.org/whl/torch_stable.html`, the version of Python should be below 3.9 and above 3.7 due to some new operatiors in the Bao Server. Python 3.8.15 works in my local machine.     
4. In addition to the packages and libraries mentioned on the tutorial, we need to install `psycopg2` as well.
```shell
pip3 install psycopg2
```
(in the following analyses on the tutorial, you need to install jupyter lab or notebook, matplotlib, and pandas as well)    
5. When running the workload, we need to change the 8th line of run_queries.py under the root folder from 
```python3
PG_CONNECTION_STR = "dbname=imdb user=imdb host=localhost"
``` 
to 
```python3
PG_CONNECTION_STR = "dbname=imdb user=imdb
```
Or
```python3
PG_CONNECTION_STR = "dbname=imdb user=imdb host=/var/run/postgresql"
```
, this depends on the setting on your local machine since the default host is `/tmp/.s.PGSQL.5432` if you do not specify the `host`.   
With this change, we do not need to provide the password, take a look at https://stackoverflow.com/a/23871618    
6. For the Exploration Mode, I need to change the value of PostgreSQLConnectString of `bao_server/bao.cfg` file
from 
```
PostgreSQLConnectString = user=imdb
```
to 
```
PostgreSQLConnectString = dbname=imdb user=imdb host=/var/run/postgresql
```
since the socket locates on `/var/run/postgresql/.s.PGSQL.5432`
take a look at https://www.psycopg.org/docs/module.html#psycopg2.connect and https://stackoverflow.com/a/5500831    
7. For "Bao as an advisor", use 
```shell
psql -U imdb
```
instead to log in postgresql if you did not set password for the `imdb` user.   
PS: On my local machine, the Bao prediction is always NaN and the Bao recommendation is always (No hints) as the output of `Explain` in postgresql although the analyses of workloads are corect (similar to the graphs on the tutorial).
