hoya-docker
===========
# Hortonworks Hoya on Docker

This repository contains a Docker file to start with Hortonworks Hoya and HBase (for the image we chooses HBase but any other application with a Hoya provider shoule be OK).

```
docker run -i -t sequenceiq/hoya-docker /etc/bootstrap.sh -bash
```

The bootsrap script will start Hadoop, YARN and Zookeeper - all these required for HBase/Hoya.

#### Creating a HBase cluster using Hoya with 1 Master and 1 RegionServer

```
hoya create hbase --role master 1 --role worker 1 --manager localhost:8032 --filesystem hdfs://localhost:9000 --image hdfs://localhost:9000/hbase.tar.gz --zkhosts localhost --appconf file:///usr/local/hbaseconf
```

####Flex the HBase cluster size dynamically

```
num_of_workers=4
hoya flex hbase --role worker $num_of_workers --manager localhost:8032 --filesystem hdfs://localhost:9000
```
####Freeze the HBase cluster

```
hoya freeze hbase --manager localhost:8032 --filesystem hdfs://localhost:9000
```

####Destroy the HBase cluster

```
hoya destroy hbase --manager localhost:8032 --filesystem hdfs://localhost:9000
```
