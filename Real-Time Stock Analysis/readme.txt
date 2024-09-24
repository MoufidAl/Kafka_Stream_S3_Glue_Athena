To connect to the EC2 instance
ssh -i "kafka-streaming-project1.pem" ec2-user@ec2-3-144-88-113.us-east-2.compute.amazonaws.com

to get kafka on our EC2 machine (linux)
wget https://downloads.apache.org/kafka/3.8.0/kafka-3.8.0-src.tgz

to uncompress this file onto the machine:
tar -xvf kafka-3.8.0-src.tgz

kafka runs on java so we need to download java onto our machine
sudo yum install java

to test the version
java --version

now to start zookeeper we have to do it in the folder that we installed kafka on our machine 

cd kafka-3.8.0-src

then we start it 
bin/zookeeper-server-start.sh config/zookeeper.properties

At the same time (not recommended for development) we run the kakfa server in another terminal 
1. We want to increase the memory of the EC2 machine for Kafka: 
    export KAFKA_HEAP_OPTS="Xmx256M -Xms128M"
 
2. to start the kafka server we need to go to the directory just like zookeeper
    cd kafka-3.8.0-src/
    then:
        bin/kafka-topics.sh config/server.properties
        

    However, the problem with the above is that we are trying to connect to the private IP address of the EC2 server from our local machine which will not work.
    so we change it to the public IP.
        RUN THE BELOW COMMAND: no quotation marks.
        'sudo nano config/server.properties' 
        
        change ADVERTISED_LISTENERS to public ip of the EC2 instance: IP found on EC2 home panel online.

3. make sure the security in the EC2 machine under groups is allowing traffic from your IP address. 

4. in a 3rd terminal , connect to the EC2 and in this we will create the topic and other things.

    cd kafka 
    A. Create the Topic/s
    bin/kafka-topics.sh --create --topic {name of topic} --bootstrap -server {Public IP:9092} --replication factor 1 --partitions 1

    B. Create the Producer
    bin/kafka-console-producer.sh --topic {name of the topic above} --bootstrap-server {Public IP:9092}

    C. Create the Consumer
    In a FOURTH terminal, connect to the EC2 machine and then into the kafka folder
        bin/kafka-console-consumer.sh --topic {same topic name} --bootstrap-server {IP:9092}

-----------------

PYTHON -- everything is explained in code

-----------

S3fs, CLI, and user account in AWS:

1. In AWS click on IAMROLE to add yourself as a user with admin rights. this will download
a file with access key and secret access key.
2. download CLI and open a new terminal
3. type: aws configure
    this will prompt you for the access key and secret key.
hit enter a few times and that will set up AWS on the local machine 

----------------------

Glue :
We will crawl the entire file from S3 so we can query on top of it in Athena

once in the page, click on crawlers. create new crawlers and choose data from the S3

and after selecting the s3 bucket name, place a '/' at the end.

follow the procedure and create a new role if necessary. 

create new database and name it appropriately 

------
now if I click on Athena I can select the table from the database we created and query.

