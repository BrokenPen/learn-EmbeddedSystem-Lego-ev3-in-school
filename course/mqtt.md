# mqtt

MQTT stands for MQ Telemetry Transport. It is a publish/subscribe, extremely simple and lightweight messaging protocol, designed for constrained devices and low-bandwidth, high-latency or unreliable networks. [(mqtt org . What is MQTT)][mqtt]

<!--
### for the current state only installed= = not confige yet , ..
-->

## installation

- install open source mqtt broker : `mosquitto`
- install open source mqtt client : `mosquitto-clients` for testing
- install python mqtt library : `paho-mqtt`

## premise

assume you have run before

    sudo apt-get update -y & sudo apt full-upgarde -y

## install mqtt

      ssh robot@10.42.0.3 
      sudo adduser mosquitto
      # type mosquitto as password for mosquitto, keep remaining field empty by pressing enter for Y
      sudo apt-get install mosquitto mosquitto-clients -y
    

## confige mqtt

    # -c, overwrite existing files, I just dont use it
    # just make sure your pwfile is empty

Create mqtt client for connect mosquitto

    sudo mosquitto_passwd /etc/mosquitto/pwfile ev3
    # password : formosa
        
    sudo mosquitto_passwd  /etc/mosquitto/pwfile test
    # password : formosa
  
    sudo mkdir /var/lib/mosquitto/
    sudo chown mosquitto:mosquitto /var/lib/mosquitto/ -R

Configuration local

    cd /etc/mostquitto
    sudo nano mosquitto.conf 
    # First line
    # Place your local configuration in /etc/mosquitto/conf.d/

Way to confige mosquitto

    cd /etc/mostquitto/conf.d
    ls
    # README
    cat README
    # Any files placed in this directory that have a .conf ending will be loaded as config files by the broker. Use this to make your local config.
    man mosquitto.conf
    # Let use the example text that I found in digitalocean.com
    # https://www.digitalocean.com/community/questions/how-to-setup-a-mosquitto-mqtt-server-and-receive-data-from-owntracks
    sudo nano mosquitto.conf

Copy and Paste the follow content

    listener 1883 10.42.0.3
    persistence true
    persistence_location /var/lib/mosquitto/
    persistence_file mosquitto.db
    log_dest syslog
    log_dest stdout
    log_dest topic
    log_type error
    log_type warning
    log_type notice
    log_type information
    connection_messages true
    log_timestamp true
    allow_anonymous false
    password_file /etc/mosquitto/pwfile

Reset mosquitto service

    sudo /sbin/ldconfig
    sudo service mosquitto restart

---

## simple test mosquitto


you need two terminal to run subsriber and publisher side

    ssh robot@10.42.0.3
    sudo su root
    
simple test with authoration

### mqtt annmoymos connect 
    
terminal 1 :

    mosquitto_sub -h 10.42.0.3 -p 1883 -t hello/world -d

Result : 

    Client mosqsub/11891-BROKENPEN sending CONNECT
    Client mosqsub/11891-BROKENPEN received CONNACK
    Connection Refused: not authorised.
  
---

terminal 2 : 
    
    mosquitto_pub -h 10.42.0.3 -p 1883 -d -t hello/world -m "hello"

Result : 

    Client mosqpub/12102-BROKENPEN sending CONNECT
    Client mosqpub/12102-BROKENPEN received CONNACK
    Connection Refused: not authorised.
    Error: The connection was refused.

---

### mqtt auth connect

use username and password to connect

terminal 1 : 

    mosquitto_sub -h 10.42.0.3 -p 1883 -t hello/world -d -u ev3 -P formosa
     
Result :

    Client mosqsub/18187-raspberry sending CONNECT
    Client mosqsub/18187-raspberry received CONNACK
    Client mosqsub/18187-raspberry sending SUBSCRIBE (Mid: 1, Topic: hello/world, QoS: 0)
    Client mosqsub/18187-raspberry received SUBACK
    Subscribed (mid: 1): 0

---

terminal 2 : 

    mosquitto_pub -h 10.42.0.3 -p 1883 -d -t hello/world -m "hello" -u test -P formosa
    
Result : 

    Client mosqpub/12210-BROKENPEN sending CONNECT
    Client mosqpub/12210-BROKENPEN received CONNACK
    Client mosqpub/12210-BROKENPEN sending PUBLISH (d0, q0, r0, m1, 'hello/world', ... (5 bytes))
    Client mosqpub/12210-BROKENPEN sending DISCONNECT

---

### python mqtt library

    sudo apt-get install python-pip  -y # pip ulitily
    sudo pip install paho-mqtt



## reference link

[mqtt]: 
- FAQ - Frequently Asked Questions | MQTT. MQTT.ORG. [accessed 2017 May 6]. http://mqtt.org/faq
What is MQTT?

## Useful link

- paho-mqtt 1.2.3 : Python Package Index. paho-mqtt 1.2.3 : Python Package Index. [accessed 2017 May 6]. https://pypi.python.org/pypi/paho-mqtt/1.2.3