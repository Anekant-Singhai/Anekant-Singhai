we start by pinging the machine so that to see whether it is reachable or not , then , the first question is that how many tcp ports are open , most of the time people do only one scan or they forget there's nmap and in which there's this flag called -PS and -PA for tcp syn ping and tcp ack so that there may be filtered ports and they also be scanned

remember we can get from ping what services are running on the systems , sometimes scan doesn't gives the results

we start for scanning all ports: nmap -p- -sV <ip> , thus getting port services and their versions

from that we get 2 as an answer

mongodb is NOSQL database

to activate mongo shell use: mongo command

to show all the databases in the mongodb: use: show dbs

to show all the collections in a DB : show collections

### command used for dumping the content of all the documents within the collection named flag in a format that is easy to read

: db.flag.find().pretty()

syntax: db.<collection_name>.find().pretty()

we get the flag , for more info check the walkthrough -> siddh -> others -> htb -> mongod

1b6e6fb359e7c40241b6d431427ba6ea