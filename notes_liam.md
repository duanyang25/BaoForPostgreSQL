1. On Makefiles under the `pg_extension` folder:   
Since the implementation of "Bao" targets Postgresql 12, we need to change 
```shell
PG_CONFIG = pg_config
```
to
```shell
PG_CONFIG = /usr/lib/postgresql/12/bin/pg_config
```
in order to find the correct header file of Postgresql 12. (postgresql-server-dev-13 14 15 have different implementations of header files)

2. Since we did not use the VM to load the dataset, the `postgresql.conf` on our local machine locates at 
```shell
/etc/postgresql/12/main/postgresql.conf
```
So we need to use 
```shell
echo "shared_preload_libraries = 'pg_bao'" >> /etc/postgresql/12/main/postgresql.conf
```
or manually append `"shared_preload_libraries = 'pg_bao'"` at the end of `/etc/postgresql/12/main/postgresql.conf` file.
to load the built extension from above operation.
3. In order to install `torch==1.5.0+cpu` from `-f https://download.pytorch.org/whl/torch_stable.html`, the version of Python should be below 3.9 and above 3.7 due to some new operatiors in the Bao Server. Python 3.8.15 works in my local machine.
