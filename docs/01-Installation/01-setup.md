---
layout: default
title: Installation From Source
nav_order: 1
description: 'setup procedure for PostGraph'
---


## Pre-Installation

Install the following essential libraries according to each OS. Building AGE from source depends on the following Linux libraries


#### CentOS
```
yum install gcc glibc glib-common readline readline-devel zlib zlib-devel flex bison
```
#### Fedora/Red Hat
```
dnf install gcc glibc bison flex readline readline-devel zlib zlib-devel
```
#### Ubuntu
```
sudo apt-get install build-essential libreadline-dev zlib1g-dev flex bison
```

## Installation

PostGraph supports all the stable versions of postgresql 14

The build process will attempt to use the first path in the PATH environment variable when installing PostGraph. If the pg_config path is located there, run the following command in the source code directory of PostGraph to build and install the extension.
```
make install
```
If the path to your Postgres installation is not in the PATH variable, add the path in the arguments:
```
make PG_CONFIG=/path/to/postgres/bin/pg_config install
```
## Post Installation

After the installation, open a connection to a running instance of your database and run the CREATE EXTENSION command to have PostGraph installed on the server.
```sql
CREATE EXTENSION postgraph;
```

