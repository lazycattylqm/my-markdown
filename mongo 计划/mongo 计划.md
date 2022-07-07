# 目录
~/mongo-test

*shard0 目录*  
./s0  
./s0/log  
./s0/data  


*shard1 目录*  
./s1  
./s1/log  
./s1/data  

*config 目录*  
./c0  
./c0/log  
./c0.data  

*router server 目录*  
./r0  
./r0/log  
./r0/data  

# 节点

## shard0

host: localhost  
port 27010

## shard1

host: localhost  
port: 27011

## config

host: localhost  
port: 27020

## router server

host: localhost  
port: 27030

# 名称

## shard0

name: s0

## shard1 

name: s1

## config

name: c0

## router server

name: r0

# collection

name：zone

```json
{
  "_id": "zone",
  "zone": "china/india",
}

```

# database

database: sharddb

# zone

china 

存储在s0

india

存储在s1

# index

field: zone  

sort: 1
