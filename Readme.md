Installation
-------
### Step 0. System requirements
    
    sudo apt-get install freetds-bin php5-sybase


### Step 1. Install MssqlBundle
Add the **realestate/mssql-bundle** into **composer.json**

    "require": {
        ....
        "realestateconz/mssql-bundle": "master-dev"
    },

And run
``` bash
$ php composer.phar install
```
### Step 2. Configure DBAL's connection to use MssqlBundle
In config.yml, remove the "driver" param and add "driver_class" instead:

```
doctrine:
    dbal:
        default_connection:     default
        connections:
            default:
                driver_class:   Realestate\MssqlBundle\Driver\PDODblib\Driver
                host:           %database_host%
                dbname:         %database_prefix%%database_name%
                user:           %database_user%
                password:       %database_password%
```

### Step 3. Enable the bundle
Finally, enable the bundle in the kernel:

``` php
<?php
// app/AppKernel.php

public function registerBundles()
{
    $bundles = array(
        // ...
        new Realestate\MssqlBundle\RealestateMssqlBundle(),
    );
}
```

Notes
-------
### Prerequsites
This driver requires version 8.0 (from http://www.ubuntitis.com/?p=64) as default 4.2 version does not have UTF support

In /etc/freetds/freetds.conf, change
tds version = 4.2
to
tds version = 8.0

### NVARCHAR & NTEXT data types
NVARCHAR & NTEXT are not supported.
In SQL 2000 SP4 or newer, SQL 2005 or SQL 2008, if you do a query that returns NTEXT type data, you may encounter the following exception:
_mssql.MssqlDatabaseError: SQL Server message 4004, severity 16, state 1, line 1:
Unicode data in a Unicode-only collation or ntext data cannot be sent to clients using DB-Library (such as ISQL) or ODBC version 3.7 or earlier.

It means that SQL Server is unable to send Unicode data to FTREETDS, because of shortcomings of FTREETDS. You have to CAST or CONVERT the data to equivalent NVARCHAR data type, which does not exhibit this behaviour.



