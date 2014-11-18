# Logstash Dockerfile

Logstash 1.3.3 (with Kibana 3)


Clone the repo

    git clone https://github.com/denibertovic/logstash-dockerfile

Create OpenSSL certificates for secure communication with logstash-forwarder.
The build will fail if no certs are present.

    cd logstash-dockerfile && mkdir certs && cd certs

    openssl req -x509 -batch -nodes -newkey rsa:2048 -keyout logstash-forwarder.key -out logstash-forwarder.crt

Build

    docker build -t logstash .

Or pull from the Hub:

    docker pull denibertovic/logstash

First we need to make sure Elasticsearch is running

    docker run --name elasticsearch -d -t denibertovic/elasticsearch

Run logstash:


    mkdir conf && mv logstash.conf.example conf/logstash.conf
    docker run --name logstash -p 5043:5043 -p 514:514 -v `pwd`/certs:/opt/certs \
        -v `pwd`/conf:/opt/conf --link elasticsearch:elasticsearch -i -t logstash

Once the service is running try and send some data to it with netcat:

    netcat localhost 514
        > test
        > test
        > CTRL+C
    # You should see the messages show up on logstash

Ports

    514  (syslog)
    5043 (lumberjack)
    9292 (logstash ui)

Volumes

    /opt/conf (contains logstash.conf)
    /opt/certs (contains logstash forwarder certificates)

