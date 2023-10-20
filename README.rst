
staz: A Template for pgEdge's Spock Three AZ Clustering
-------------------------------------------------------

Staz is a template for high availability (HA) PostgreSQL solutions using Python3. It is far from being a one-size-fits-all or plug-and-play replication system. 
It will have its own caveats. Use wisely.  Currently supported PostgreSQL versions: 15 to 16.  Staz is a fork of Patroni.

===================================
Technical Requirements/Installation
===================================

Use python3 & psycopg3

::

    pip3 install psycopg[binary]>=3.0.0

Note that external tools to call in the replica creation or custom bootstrap scripts (i.e. WAL-E) should be installed independently of Patroni.

=======================
Running and Configuring
=======================

To get started, do the following from different terminals:
::

    > etcd --data-dir=data/etcd --enable-v2=true
    > ./patroni.py postgres0.yml
    > ./patroni.py postgres1.yml

You will then see a high-availability cluster start up. Test different settings in the YAML files to see how the cluster's behavior changes. Kill some of the components to see how the system behaves.

Add more ``postgres*.yml`` files to create an even larger cluster.

Patroni provides an `HAProxy <http://www.haproxy.org/>`__ configuration, which will give your application a single endpoint for connecting to the cluster's leader. To configure,
run:

::

    > haproxy -f haproxy.cfg

::

    > psql --host 127.0.0.1 --port 5000 postgres

==================
YAML Configuration
==================


===================
Replication Choices
===================

Patroni uses Postgres' streaming replication, which is asynchronous by default. Patroni's asynchronous replication configuration allows for ``maximum_lag_on_failover`` settings. This setting ensures failover will not occur if a follower is more than a certain number of bytes behind the leader. This setting should be increased or decreased based on business requirements. It's also possible to use synchronous replication for better durability guarantees. See `replication modes documentation <https://github.com/zalando/patroni/blob/master/docs/replication_modes.rst>`__ for details.

======================================
Applications Should Not Use Superusers
======================================

When connecting from an application, always use a non-superuser. Patroni requires access to the database to function properly. By using a superuser from an application, you can potentially use the entire connection pool, including the connections reserved for superusers, with the ``superuser_reserved_connections`` setting. If Patroni cannot access the Primary because the connection pool is full, behavior will be undesirable.
