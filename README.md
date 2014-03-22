hoya-docker
===========
# Hortonworks Hoya on Docker

This repository contains a Docker file to start with Hortonworks Hoya and HBase (for the image we chooses HBase but any other application with a Hoya provider shoule be OK).

```
docker run -i -t sequenceiq/hoya-docker /etc/bootstrap.sh -bash
```

The bootsrap script will start Hadoop, YARN and Zookeeper - all these required for HBase/Hoya.


#### Creating a HBase cluster using Hoya 

``` bash
create-hoya-cluster() {
  hoya create hbase --role master 1 --role worker 1
    --manager localhost:8032
    --filesystem hdfs://localhost:9000 --image hdfs://localhost:9000/hbase.tar.gz
    --appconf file:///tmp/hoya-master/hoya-core/src/main/resources/org/apache/hoya/providers/hbase/conf
    --zkhosts localhost
}
```
This will launch a 2 node HBase cluster (1 Master and 1 RegionServer). 

####Flex the HBase cluster size dynamically

Now lets increase the number of RegionServers.

``` bash
flex-hoya-cluster() {
  num_of_workers=$1
  hoya flex hbase --role worker $num_of_workers --manager localhost:8032 --filesystem hdfs://localhost:9000
}
```

This will start as many RegionServers as specified - in new YARN containers. 

####Freeze the HBase cluster

Also the size of the cluster can be decreased if the load on the system does not demand for a larger number of RegionServers. The cluster can also be freezed (Hoya takes care about persisting the state).

``` bash
freeze-hoya-cluster() {
  hoya freeze hbase --manager localhost:8032 --filesystem hdfs://localhost:9000
}
```

####Destroy the HBase cluster

Finally when you'd like to destroy the cluster and the state associated with the application you can use:

``` bash
destroy-hoya-cluster() {
  hoya destroy hbase --manager localhost:8032 --filesystem hdfs://localhost:9000
}
```
