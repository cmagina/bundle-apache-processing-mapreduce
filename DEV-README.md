# Overview

This bundle provides a complete deployment of the core components of the Apache
Hadoop platform along with its monitoring facilities.  This document describes
how the components of this deployment are connected and how to connect additional
services to the deployment to add
functionality.


# Building on this Bundle

This bundle supports two ways to extend its functionality via additional charms:
by relating to the subordinate `apache-hadoop-plugin` charm, or by using a
stand-alone charm connecting to HDFS and YARN via standard relations.


## Subordinate Charm Connection

By connecting to the [plugin charm](https://jujucharms.com/apache-hadoop-plugin)
via the `hadoop-plugin` subordinate relation, a charm will get access to a
pre-configured Apache Hadoop environment, including Java libraries and the
`hdfs` command-line utility.  The `/etc/environment` file on the unit will
have all of the path and configuration variables defined.

The details of the interface protocol used by this relation are documented in
the DEV-README.md of the [plugin charm](https://jujucharms.com/apache-hadoop-plugin).

For example, to deploy [apache-hive](https://jujucharms.com/apache-hive) with
this bundle, you would use:

    juju quickstart apache-core-batch-processing
    juju deploy cs:trusty/apache-hive
    juju add-relation apache-hive plugin

This workflow has been bundled and is available as
[apache-analytics-sql](https://jujucharms.com/apache-analytics-sql).


## Stand-alone Charm Connection

If a charm does not need the Java libraries, environment config, etc., and only
needs the address information for the NameNode and ResourceManager, then it
can connect to the [hdfs-master](https://jujucharms.com/apache-hadoop-hdfs-master)
and [yarn-master](https://jujucharms.com/apache-hadoop-yarn-master) services via the
`namenode` and `resourcemanager` relations, respectively.

The details of the interface protocols used by these relations are documented
in the DEV-README.md of the respective charms.

For example, to deploy [apache-hue](https://jujucharms.com/apache-hue) with
this bundle, you would use:

    juju quickstart apache-core-batch-processing
    juju deploy cs:trusty/apache-hue
    juju add-relation apache-hue plugin


# Component Services

This bundle deploys the following services:
(For detailed information, including relation specifications,
see each charm's DEV-README.md):

* [**namenode**](https://jujucharms.com/apache-hadoop-hdfs-master)
  The two units of this service provide the NameNode in an HA configuration.

* [**resourcemanager**](https://jujucharms.com/apache-hadoop-yarn-master)
  This service runs the ResourceManager component.

* [**slave**](https://jujucharms.com/apache-hadoop-compute-slave)
  The three units of this service run the DataNode, NodeManger, and JournalNode components.

* [**plugin**](https://jujucharms.com/apache-hadoop-plugin)
  This subordinate provides a consolidated connection point for Apache Hadoop, and provides
  the Hadoop libaries and config for connected clients.

* [**client**](https://jujucharms.com/hadoop-client)
  This is a minimal client, which serves as a gateway node.

* [**zookeeper**](https://jujucharms.com/apache-zookeeper)
  The three units of this service provide automatic fail-over for the NameNode.

* [**ganglia**](https://jujucharms.com/ganglia)
  This service provides the UI for monitoring.

* [**ganglia-node**](https://jujucharms.com/ganglia-node)
  This subordinate collects system-level monitoring data from each node.

* [**rsyslog-forwarder-ha**](https://jujucharms.com/rsyslog-forwarder-ha)
  This subordinate forwards logs from each node to flume-syslog.

* [**flume-syslog**](https://jujucharms.com/apache-flume-syslog)
  This service collects the logs in preparation for ingesting them into HDFS.

* [**flume-hdfs**](https://jujucharms.com/apache-flume-hdfs)
  This service takes the logs from flume-syslog and ingests them into HDFS.
