# Self-Balancing Kafka Demo
docker compose demo of SBK


Overview
Self-balancing simplifies the management of Kafka clusters in the following ways:

When the load on the cluster is unevenly distributed, Self-balancing will automatically rebalance partitions to optimize performance
When a new broker is added to the cluster, Self-balancing will automatically fill it with partitions
When an operator wants to remove a broker, she can call a Self-balancing API to shut down the broker and drain the partitions from it
When a broker has been down for a certain amount of time, Self-balancing will automatically reassign the partitions to other brokers


Deployment
Self-balancing runs on the Confluent Server brokers and does not introduce any new dependencies.

# Demo

##About
This demo showcases the main features of Self-balancing Clusters, which will be available in GA with CP 6.0.

## Requirements
In order to run this demo, you will need *Docker* and at least 8GB of RAM.


## Docker Images
The following containers will be downloaded, initialized and started by docker compose. 
ZooKeeper
Confluent Platform Server (cp-server)
Confluent Control Center

## Test scenarios
Uneven load - We will create uneven load in the cluster and watch Self-balancing address this condition.
Add brokers - We will add new brokers to the cluster and watch Self-Balancing distribute leader follower replicas to the new brokers

## Demo scripts
The demo steps are in two shell scripts "01_runme_startup" and "02_runme_expansion"
Use "09_down" to reset the docker containers between demo runs.

## Message logging level
Message logging is OFF by default.
If the demo doesnt work as expected, and you want more message logging, edit log4j_injected/log4j.properties and change "OFF" to "INFO".

## Scenario 1: Uneven Load: 01_runme_startup
Start a system with one zookeeper, three Kafka brokers, a Confluent Control Center and a container to execute commands (called "Runme55").
```
The Runme55 container does the following:
a. sleep for 15 seconds while the brokers come online
b. create a topic "sbk" with fifty partitions, deliberately unbalanced with all leaders on one broker
c. sleep for 5 seconds
d. produce 3.6m messages into topic sbk

Open Confluent Control Center http://localhost:9021 and examine the partition assignments under under Cluster 1 | topics | sbk | partitions
Leader (Broker ID)=0 for all partitions, and Followers (Broker ID)=1 for all partitions.
Browse to Cluster 1 | Brokers | Self-balancing and observe that the status is Idle.
After 10-15 minutes, a rebalance will be activated and the leaders/followes for topic sbk will be rebalanced accross brokers 0/1/2.
```

## Secenario 2: Add Brokers: 02_runme_expansion
```
After the first rebalance completes, this script will add two brokers to the cluster, making a total of five brokers.
"Self-Balancing" will again activate, and rebalance the leaders and followers for topic sbk accross five brokers after ten minutes.
This can also be observed in Confluent Control Center
```

[![Screenshot-2020-10-06-at-15-29-20.png](https://i.postimg.cc/4NdXkpDf/Screenshot-2020-10-06-at-15-29-20.png)](https://postimg.cc/CdW3bnMX)
