[![Gitter chat](https://badges.gitter.im/gitterHQ/gitter.png)](https://gitter.im/big-data-europe/Lobby)

# Changes

 * Updated to https://github.com/big-data-europe/docker-hadoop/pull/108
 * fix tags on images

# Hadoop Docker

## Supported Hadoop Versions
See repository branches for supported hadoop versions

## Quick Start

To deploy an example HDFS cluster, run:
```
  make
  docker-compose up
```
To run hdfs command, run:
```
  docker run --network docker-hadoop_default --env-file hadoop.env bde2020/hadoop-base:master hdfs dfs -ls /
```

To run hadoop command inside container, run:
```
  docker run -it --network docker-hadoop_default -v `pwd`:/data --env-file hadoop.env bde2020/hadoop-base:master bash
```

Run example wordcount job:
```
  make wordcount
```

Or deploy in swarm:
```
docker network create --driver overlay hbase
docker stack deploy -c docker-compose-v3.yml hadoop
```

`docker-compose` creates a docker network that can be found by running `docker network list`, e.g. `docker-hadoop_default`.

Run `docker network inspect` on the network (e.g. `docker-hadoop_default`) to find the IP the Hadoop interfaces are published on. Access these interfaces with the following URLs:

* Namenode: http://localhost:9870/dfshealth.html#tab-overview
* History server: http://localhost:8188/applicationhistory
* Resource manager: http://localhost:8088/

All other Hadoop communication ports are not exposed and only accessible from inside the Docker network using service name and port.

## Configure Environment Variables

The configuration parameters can be specified in the hadoop.env file or as environmental variables for specific services (e.g. namenode, datanode etc.):
```
  CORE_CONF_fs_defaultFS=hdfs://namenode:8020
```

CORE_CONF corresponds to core-site.xml. fs_defaultFS=hdfs://namenode:8020 will be transformed into:
```
  <property><name>fs.defaultFS</name><value>hdfs://namenode:8020</value></property>
```
To define dash inside a configuration parameter, use triple underscore, such as YARN_CONF_yarn_log___aggregation___enable=true (yarn-site.xml):
```
  <property><name>yarn.log-aggregation-enable</name><value>true</value></property>
```

The available configurations are:
* /etc/hadoop/core-site.xml CORE_CONF
* /etc/hadoop/hdfs-site.xml HDFS_CONF
* /etc/hadoop/yarn-site.xml YARN_CONF
* /etc/hadoop/httpfs-site.xml HTTPFS_CONF
* /etc/hadoop/kms-site.xml KMS_CONF
* /etc/hadoop/mapred-site.xml  MAPRED_CONF

If you need to extend some other configuration file, refer to base/entrypoint.sh bash script.

Thanks to original maintainer "Ivan Ermilov <mailto:ivan.s.ermilov@gmail.com>"
