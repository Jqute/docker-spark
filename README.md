# docker-spark
Hello World with Spark and Docker. Spark with Docker example.

### Build Spark image

Run `docker build spark2 -t spark`

### Start Spark Master and Spark Worker

Run `docker-compose up -d --remove-orphans`

- `-d`  means running containers in the background
- `--remove-orphans` means removing containers for services not defined in the Compose file

### Start the Driver program using Spark Shell

Start a docker container using following command. Doing `docker ps` would display a container with name “driver”. Note the port **4041:4040**. This has been done because we have earlier used host 4040 port while setting up master. Note also network *spark-network*. This network specified in the docker-compose file to access Spark Master using the URL *spark://spark-master:7077*. 

```
docker run --network=spark-network -it -p 8088:8088 -p 8042:8042 -p 4041:4040 --name driver -h driver spark:latest bash
```

You'll be asked to create *spark-network* network first. Create a network using 

```
docker network create spark-network
```

As you execute `docker run` command, you get inside the docker container. Execute following command to start the spark shell (Driver) program

```
spark-shell --master spark://spark-master:7077
```

Now you can run Scala code. For example

```
for (n <- Seq("chris", "ed", "maurice")) println(n.capitalize)
```

### Running a HelloWorld Analysis on Cluster

Follow steps given below to run a text file analysis in the cluster setup above where the driver program is created as another container (let’s call it as client or driver node) apart from two containers, one running a master and other a worker node.

1. Create a file named as helloworld.txt within /home folder on both client (running driver program – spark shell in current case) and worker node. The file could be accessed with path. “/home/helloworld.txt”. If you do not do this, you would end up with exception such as FileNotFoundException or Invalid Input File Path Exception.
2. Start the spark shell program on client node using the command such as following:

```
spark-shell --master spark://spark-master:7077
```

This would start a spark application, register the app with master and have cluster manager (master) ask worker node to start an executor.

3. Execute following commands to run an analysis:

``` 
val textFile = sc.textFile("/home/helloworld.txt")
textFile.count() 
```

heck the WebUI for master (localhost:8080) and worker (localhost:8081) to check the job details. Also localhost:4041 to check the job stages.
