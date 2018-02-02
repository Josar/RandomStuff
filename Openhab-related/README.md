This is a program to receive data with a 433MHz transciever and publish it to a MQTT broker like mosquitto.

It is configured to send data to locallhost expecting the broker to run on the RPI.

It is based on the RFSniffer from [433Utils](https://github.com/ninjablocks/433Utils) and is best placed beside RFSniffer in RPI_utils

To compile it is neccessary to change the Makefile as follows.

```
# Defines the RPI variable which is needed by rc-switch/RCSwitch.h
CXXFLAGS=-DRPI

all: send codesend RFSniffer RFmqtt

send: ../rc-switch/RCSwitch.o send.o
        $(CXX) $(CXXFLAGS) $(LDFLAGS) $+ -o $@ -lwiringPi

codesend: ../rc-switch/RCSwitch.o codesend.o
        $(CXX) $(CXXFLAGS) $(LDFLAGS) $+ -o $@ -lwiringPi

RFSniffer: ../rc-switch/RCSwitch.o RFSniffer.o
        $(CXX) $(CXXFLAGS) $(LDFLAGS) $+ -o $@ -lwiringPi

RFmqtt: ../rc-switch/RCSwitch.o RFmqtt.o
        $(CXX) $(CXXFLAGS) $(LDFLAGS) $+ -o $@ -lwiringPi -lmosquitto

clean:
        $(RM) ../rc-switch/*.o *.o send codesend servo RFSniffer RFmqtt
```

Besides that some depencies have to be installed.

```
sudo apt-get install libmosquitto-dev
```

when running mosquitto as broker on the pi it has also to be installed.

```
sudo apt-get install mosquitto
```

after that just compile it ( this expects you followed all steps necccessary to compile the RPI_utils previously).
```
sodu make
```

It can be configured as follows
```
./RFmqtt [-h Host] [-p Port] [-u username] [-x password] [-t topic] [-g WiringPI GPIO] [-w pulsewith]
```
All paramter are optional, default values are.
```
Host: localhost
Port: 1883
Usernaem: admin
Password: password
MQTT Topic: 433MHz
GPIO Pin: 2
Pulsewidth: none
```

