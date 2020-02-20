# Kafka Python API


You must have zookeeper and kafka installed on your machine.  If not use

`https://github.com/shekhar2010us/kafka_t/blob/master/labs/01_install_zk_kafka_single_broker.md`


## Install Python 3.6 on VM (20 Minutes)
    
    sudo apt install -y python-pip
    sudo apt install -y python3-pip

    pip install kafka
    pip3 install kafka
        
Check your version of Python
    
    which python
    python --version
    python3 --version


## Download Source Code

    cd ~

    git clone https://github.com/smhillin/kafka_essentials
    
    cd kafka_essentials

## Take a look at your text file

```
head logs/logs.txt
wc -l logs/logs.txt 
```
   
Here we have a log file with 163416 lines of log entry's.


##  Create a Producer

We are going to write a producer that reads log data from the log file and writes that data to a kafka broker.  This will simulate
real time logging.  


Luckily this has already been done for you!  Check it out

    vi code/log_consumer.py
   
 

## Create a consumer


We are going to write a producer that polls a kafka topic for log data.  When that topic has data we will take the unstructured log
data and process it into a structure that we can later perform some analysis on. 
    
    
    vi code/log_producer.py
    
    vi code/log_parser.py
    
    
## Run consumer and producer in two tabs

Open another terminal window and ssh into your vm.  Go to kafka_essentials directory.
Run both of these consumers.
	
	# in first terminal
    sudo python3 code/log_consumer.py
   
    # in second terminal 
    sudo python3 code/log_producer.py


Watch your consumer process the data as it is written to the topic!
If you want quicker, change sleep(3) to something smaller




    
# Troubleshooting

Problems with Python installation.  Run this and then rebuild.

- sudo apt-get install build-essential tk-dev libncurses5-dev libncursesw5-dev libreadline6-dev 
libdb5.3-dev libgdbm-dev libsqlite3-dev libssl-dev libbz2-dev libexpat1-dev liblzma-dev zlib1g-dev