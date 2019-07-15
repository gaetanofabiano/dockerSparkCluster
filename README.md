
# spark cluster on docker

A `linux:stretch` based [Spark](http://spark.apache.org) container. 
Use it in a standalone cluster with the accompanying `docker-compose.yml`.

This project is not aimed to be considered an enviorments for production products. 

IT is for study and demo purpose only

## docker-compose example

To create a simplistic standalone cluster with [docker-compose](http://docs.docker.com/compose):

    docker-compose up

The SparkUI will be running at `http://${YOUR_DOCKER_HOST}:8080` with one worker listed. 

## interact with the cluster

After run the 

    docker-compose up

Use the docker ps to retreive the ID of the master node conteiner <MASTER_CONTAINER_ID>:

    docker ps

For connecting via console to the master node use:

    docker exec -it <MASTER_CONTAINER_ID> bash

For example, if my id is 59d5d229a103

    docker exec -it 59d5d229a103 bash

To copy a file on the master node use

    docker cp <LOCAL_FILE_NAME> <MASTER_CONTAINER_ID>:<REMOTE_MASTER_FOLDER>

For example to copy a file named sparkJob.jar in the folder /usr/spark-2.4.1/bin where the container id is 59d5d229a103

    docker cp sparkJob.jar 59d5d229a103:/usr/spark-2.4.1/bin

To run `pyspark`, exec into a container:

    docker exec -it <MASTER_CONTAINER_ID> /bin/bash
    bin/pyspark

To run `SparkPi`, exec into a container:

    docker exec -it <MASTER_CONTAINER_ID> /bin/bash
    bin/run-example SparkPi 10

## Run compiled Java Spark Job application on the cluster

Ensure that you have build a complete jar file with all dependencies if there is external library usage.

Retreive the ID of the master node conteiner <MASTER_CONTAINER_ID> as described before.

For example, we obtain an id like this:

    59d5d229a103

Create a folder in the master node:

     docker exec -it 59d5d229a103 bash
     mkdir jobs

Copy the jar with dependencies in the folder

    docker cp my-jar-with-dependencies.jar 59d5d229a103:/usr/spark-2.4.1/jobs  
    
Or copy the file into the folder /data of the cluster

To run the java job, go into the master node

    docker exec -it 59d5d229a103 bash

Let assume that the main java class that describe the job is MySparkApp in the package it.mysparktest

    cd bin

    ./spark-submit --class it.mysparktest.MySparkApp my-jar-with-dependencies.jar --master spark://master:7077

Go to `http://${YOUR_DOCKER_HOST}:8080` and follow the execution


## access to the cluster

To access to the cluster use the http://localhost:8080 address


## Extra: others docker python example

To run `SparkPi`, run the image with Docker:

    docker run --rm -it -p 4040:4040 gettyimages/spark bin/run-example SparkPi 10

To start `spark-shell` with your AWS credentials:

    docker run --rm -it -e "AWS_ACCESS_KEY_ID=YOURKEY" -e "AWS_SECRET_ACCESS_KEY=YOURSECRET" -p 4040:4040 gettyimages/spark bin/spark-shell

To do a thing with Pyspark

    echo -e "import pyspark\n\nprint(pyspark.SparkContext().parallelize(range(0, 10)).count())" > count.py
    docker run --rm -it -p 4040:4040 -v $(pwd)/count.py:/count.py gettyimages/spark bin/spark-submit /count.py



