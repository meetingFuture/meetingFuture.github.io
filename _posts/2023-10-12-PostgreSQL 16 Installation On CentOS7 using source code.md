---
title: PostgreSQL 16 Installation On CentOS7 using source code
tags:
  - Basic
  - PostgreSQL
  - Install
  - CentOS
categories:
  - Database
  - PostgreSQL
---
# Add User&Group

```bash
export PGPORT=5432
export PGDATA=/pgdata/$PGPORT/pg_root
export PGHOME=/usr/local/postgresql
export PGTBS=/pgdata/$PGPORT/pg_tbs

groupadd -g 2000 postgres
useradd  -g 2000 -u 2000 postgres
id postgres
passwd  postgres
mkdir -p $PGDATA/{data,backups,scripts,archive_wals}
mkdir -p $PGTBS
chown -R postgres:postgres /pgdata/$PGPORT
chmod 0700 /pgdata/$PGPORT
```

# Source Code Download

```bash
cd /usr/local/src

wget --no-check-certificate https://ftp.postgresql.org/pub/source/v16.0/postgresql-16.0.tar.bz2

tar xf postgresql-16.0.tar.bz2

cd postgresql-16.0/

yum install -y perl-ExtUtils-Embed readline-devel zlib-devel pam-devel \
libxml2-devel libxslt-devel openldap-devel python-devel gcc-c++ openssl-devel \
cmake perl-IPC-Run libicu-devel
```

# Source Code Make&Install

```bash
./configure --prefix=/usr/local/postgresql-16.0

./configure --help

  --with-pgport=PORTNUM   set default port number [5432]
  --with-blocksize=BLOCKSIZE
                          set table block size in kB [8]
  --with-segsize=SEGSIZE  set table segment size in GB [1]

gmake -j 4 world
gmake install-world

ln -s /usr/local/postgresql-16.0 /usr/local/postgresql

chown -R postgres:postgres /usr/local/postgresql-16.0/
```

# Set bash_profile

```bash
su - postgres
cat >>  ~/.bash_profile <<"EOF"
export PS1="[\u@\h \W]\$ "
export PGVERISON=16
export PGPORT=5432
export PGUSER=postgres
export PGDATA=/pgdata/$PGPORT/pg_root
export PGTBS=/pgdata/$PGPORT/pg_tbs
export LANG=en_US.utfB
export PGHOME=/usr/local/postgresql
export LD_LIBRARY_PATH=$PGHOME/lib:/lib64:/usr/lib64:/usr/local/lib64:/lib:/usr/lib:/usr/local/lib
export PATH=$PGHOME/bin:$PATH:.
export MANPATH=$PGHOME/share/man:$MANPATH
export PGHOST=$PGDATA
export PGUSER=postgres
export PGDATABASE=postgres
alias rm='rm -i'
alias ll='ls -lh'
EOF

source .bash_profile
```

# Initial Database

D, --pgdata=]DATADIR location for this database cluster
-E, --encoding=ENCODING set default encoding for new databases
-W, --pwprompt prompt for a password for the new superuser

```bash
initdb -D $PGDATA -E UTF8 --locale=en_US.utf8 -U postgres -W

The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.utf8".
The default text search configuration will be set to "english".

Data page checksums are disabled.

Enter new superuser password:
Enter it again:

fixing permissions on existing directory /pgdata/5432/pg_root ... ok
creating subdirectories ... ok
selecting dynamic shared memory implementation ... posix
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default time zone ... Asia/Shanghai
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok

initdb: warning: enabling "trust" authentication for local connections
initdb: hint: You can change this by editing pg_hba.conf or using the option -A, or --auth-local and --auth-host, the next time you run initdb.

Success. You can now start the database server using:

    pg_ctl -D /pgdata/5432/pg_root -l logfile start
```

# Modify postgresql.conf

```bash
cat >> $PGDATA/postgresql.conf <<"EOF"
listen_addresses = '*'
port=5432
unix_socket_directories='/pgdata/5432/pg_root'
logging_collector = on
log_directory = 'pg_log'
log_filename = 'postgresql-%a.log'
log_truncate_on_rotation = on
EOF
```

# Modify pg_hba.conf

```bash
cat >> $PGDATA/pg_hba.conf << EOF
# TYPE  DATABASE    USER    ADDRESS       METHOD
local     all       all                    trust
host      all       all   127.0.0.1/32     trust
host      all       all    0.0.0.0/0        md5
host   replication  all    0.0.0.0/0        md5
EOF
```

# Start&Stop

```bash
Start Instance:
pg_ctl start

Status:
pg_ctl -D $PGDATA status

Check 
pg_isready -p 5432

Stop
pg_ctl -D $PGDATA -mf stop
```

# Start Script

```bash
cat > /etc/systemd/system/PG16.service <<"EOF"
[Unit]
Description=PostgreSQL database server
Documentation=man:postgres(1)
After=network.target

[Service]
Type=forking
User=postgres
Group=postgres
OOMScoreAdjust=-1000
ExecStart=/usr/local/postgresql/bin/pg_ctl start -D /pgdata/5432/pg_root -s -o "-p 5432" -w -t 300
ExecStop=/usr/local/postgresql/bin/pg_ctl stop -D /pgdata/5432/pg_root -s -m fast
ExecReload=/usr/local/postgresql/bin/pg_ctl reload -D /pgdata/5432/pg_root -s
KillMode=mixed
KillSignal=SIGINT
TimeoutSec=0

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable PG16
systemctl start PG16
systemctl status PG16
```

# Reference

https://cloud.tencent.com/developer/article/2326353