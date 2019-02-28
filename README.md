# install_oracle_pyodbc
How to install pyodbc and connect to Oracle 11g (centos7)

https://github.com/mkleehammer/pyodbc/wiki/Connecting-to-Oracle-from-RHEL-or-Centos

install 3rdparty libs
---------
(https://www.oracle.com/technetwork/topics/linuxx86-64soft-092277.html)

- oracle-instantclient11.2-basic-11.2.0.4.0-1.x86_64.rpm  
- oracle-instantclient11.2-devel-11.2.0.4.0-1.x86_64.rpm  
- oracle-instantclient11.2-odbc-11.2.0.4.0-1.x86_64.rpm

```
$ rpm -ivh oracle-instantclient11.2-basic-11.2.0.4.0-1.x86_64.rpm
$ rpm -ivh oracle-instantclient11.2-devel-11.2.0.4.0-1.x86_64.rpm  
$ rpm -ivh oracle-instantclient11.2-odbc-11.2.0.4.0-1.x86_64.rpm
```

(http://www.unixodbc.org/download.html)

- unixODBC-2.3.0.tar.gz

Note, the version of unixODBC is really important!! 
(2.3.1 or the upper version won't work properly..)

--> internally, oracle odbc driver refers to unixODBC library (libodbcinst.so.1)

$ ldd /usr/lib/oracle/11.2/client64/lib/libsqora.so.11.1

linux-vdso.so.1 =>  (0x00007fffa8f50000)<br>
.... <br>
libclntsh.so.11.1 => /usr/lib/oracle/11.2/client64/lib/libclntsh.so.11.1 (0x00007ff50d095000) <br>
<b>libodbcinst.so.1 => /usr/local/lib/libodbcinst.so.1 (0x00007ff50ce7e000)</b><br>
libc.so.6 => /lib64/libc.so.6 (0x00007ff50cab1000)<br>
/lib64/ld-linux-x86-64.so.2 (0x000055ec0f767000)<br>
libnnz11.so => /usr/lib/oracle/11.2/client64/lib/libnnz11.so (0x00007ff50c6e3000)<br>
libaio.so.1 => /lib64/libaio.so.1 (0x00007ff50c4e1000)<br>

--> the unixodbc library name should be "libodbcinst.so.1", and the last unixodbc version that generates the name is 2.3.0
<br>(from 2.3.1, the name will be changed to "libodbcinst.so.2")

```
$ ./configure&&make&&make install
```

- pyodbc-4.0.26.tar.gz

```
$ python setup.py install
```

configure
---------

-- firstly, check your odbc configuration

```
$ odbcinst -j
unixODBC 2.3.0
DRIVERS............: /usr/local/etc/odbcinst.ini
SYSTEM DATA SOURCES: /usr/local/etc/odbc.ini
FILE DATA SOURCES..: /usr/local/etc/ODBCDataSources
USER DATA SOURCES..: ...
SQLULEN Size.......: 8
SQLLEN Size........: 8
SQLSETPOSIROW Size.: 8
```

-- open and set the 'DRIVERS' file

```
[MyOracle]
Description=Oracle Unicode driver
Driver=/usr/lib/oracle/11.2/client64/lib/libsqora.so.11.1
UsageCount=1
FileUsage=1
```
-- add environment variables

export LD_LIBRARY_PATH=/usr/local/lib:/usr/lib/oracle/11.2/client64/lib

(/usr/local/lib: unixodbc libs, /usr/lib/oracle/11.2/client64/lib: oracle libs)

test
---------

$ python -c "import pyodbc; print(pyodbc.connect('DRIVER=MyOracle;DBQ={oracleip}:{port}/{cid};UID={uid};PWD={password}'))"

<pyodbc.Connection object at 0x7fc0ab2f9d50>
