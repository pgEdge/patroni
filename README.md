[![Tests Status](https://github.com/zalando/patroni/actions/workflows/tests.yaml/badge.svg)](https://github.com/zalando/patroni/actions/workflows/tests.yaml?query=branch%3Amaster)
[![Coverage Status](https://coveralls.io/repos/zalando/patroni/badge.svg?branch=master)](https://coveralls.io/github/zalando/patroni?branch=master)


# pgedge-patroni: pgEdge's Spock Three AZ Clustering

Patroni is a template for high availability (HA) PostgreSQL solutions using Python. For maximum accessibility, Patroni supports a variety of distributed configuration stores like [ZooKeeper](https://zookeeper.apache.org/), [etcd](https://github.com/coreos/etcd), [Consul](https://github.com/hashicorp/consul) or [Kubernetes](https://kubernetes.io). Database engineers, DBAs, DevOps engineers, and SREs who are looking to quickly deploy HA PostgreSQL in datacenters - or anywhere else - will hopefully find it useful.

Currently pgedge-patroni supported PostgreSQL 15 and above.

![Image Description](pgedge-ultra-high-availability.png)

## How Patroni Works

Patroni originated as a fork of [Governor](https://github.com/compose/governor), the project from Compose. It includes plenty of new features.

For an example of a Docker-based deployment with Patroni, see [Spilo](https://github.com/zalando/spilo), currently in use at Zalando.

For additional background info, see:
- [Elephants on Automatic: HA Clustered PostgreSQL with Helm](https://www.youtube.com/watch?v=CftcVhFMGSY), talk by Josh Berkus and Oleksii Kliukin at KubeCon Berlin 2017
- [PostgreSQL HA with Kubernetes and Patroni](https://www.youtube.com/watch?v=iruaCgeG7qs), talk by Josh Berkus at KubeCon 2016 (video)
- [Feb. 2016 Zalando Tech blog post](https://tech.zalando.de/blog/zalandos-patroni-a-template-for-high-availability-postgresql/)

## Replication Choices

Patroni uses Postgres' streaming replication, which is asynchronous by default. Patroni's asynchronous replication configuration allows for ``maximum_lag_on_failover`` settings. 
This setting ensures failover will not occur if a follower is more than a certain number of bytes behind the leader. This setting should be increased or decreased based on business requirements. It's also possible to use synchronous replication for better durability guarantees.

See replication modes documentation [here](https://github.com/zalando/patroni/blob/master/docs/replication_modes.rst) for details.

## Applications Should Not Use Superusers

When connecting from an application, always use a non-superuser. Patroni requires access to the database to function properly. By using a superuser from an application, you can potentially use the entire connection pool, including the connections reserved for superusers, with the ``superuser_reserved_connections`` setting. If Patroni cannot access the Primary because the connection pool is full, behavior will be undesirable.
