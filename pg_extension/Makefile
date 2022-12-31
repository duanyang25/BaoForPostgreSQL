EXTENSION = pg_bao
MODULE_big = pg_bao
DATA = pg_bao--0.0.1.sql
OBJS = main.o 
# PG_CONFIG = pg_config
# For Postgres 12 Only (due to different implementations of header files of `postgresql-server-dev`)
PG_CONFIG = /usr/lib/postgresql/12/bin/pg_config
PGXS := $(shell $(PG_CONFIG) --pgxs)
#SHLIB_LINK = -lsqlite3
include $(PGXS)

# For VM
# echo "shared_preload_libraries = 'pg_bao'" >> /media/data/pg_data/data/postgresql.conf

# For local postgres
# echo "shared_preload_libraries = 'pg_bao'" >> /etc/postgresql/12/main/postgresql.conf
