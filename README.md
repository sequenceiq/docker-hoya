hoya-docker
===========
# Hortonworks Hoya on Docker

This repository contains a *docker file* to build a *docker image* with Hortonworks Hoya and HBase (for the image we chooses HBase but any other application with a Hoya provider shoule be OK).
The Hoya docker file depends on our previous Hadoop docker file/image, available at [SequenceIQ GitHub page](https://github.com/sequenceiq/hadoop-docker). 

Also the base Hadoop docker image is available as an official [Docker image](https://index.docker.io/u/sequenceiq/hadoop-docker).


Now lets start the Hoya docker imaga in his own isolated container (the container will span up a process with its own file system, its own networking, and its own isolated process tree, just like a VM).

```
docker run -i -t sequenceiq/hoya-docker /etc/bootstrap-hoya.sh -bash
```

The bootsrap script will start Hadoop, YARN and Zookeeper - all these required for HBase/Hoya.

#####Versions
Hadoop 2.3, Hoya 0.13, HBase 0.98, Zookeeper 3.3.6

#### Creating a HBase cluster using Hoya 

``` bash

  hoya create hbase --role master 1 --role worker 1
    --manager localhost:8032
    --filesystem hdfs://localhost:9000 --image hdfs://localhost:9000/hbase.tar.gz
    --appconf file:///tmp/hoya-master/hoya-core/src/main/resources/org/apache/hoya/providers/hbase/conf
    --zkhosts localhost
```
This will launch a 2 node HBase cluster (1 Master and 1 RegionServer). 

####Flex the HBase cluster size dynamically

Now lets increase the number of RegionServers.

``` bash
  num_of_workers=$1
  hoya flex hbase --role worker $num_of_workers --manager localhost:8032 --filesystem hdfs://localhost:9000
```

This will start as many RegionServers as specified - in new YARN containers. 

####Freeze the HBase cluster

Also the size of the cluster can be decreased if the load on the system does not demand for a larger number of RegionServers. The cluster can also be freezed (Hoya takes care about persisting the state).

``` bash
  hoya freeze hbase --manager localhost:8032 --filesystem hdfs://localhost:9000
```

####Destroy the HBase cluster

Finally when you'd like to destroy the cluster and the state associated with the application you can use:

``` bash
  hoya destroy hbase --manager localhost:8032 --filesystem hdfs://localhost:9000
```

Enjoy.
