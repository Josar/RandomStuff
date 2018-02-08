This is a program to receive data with a 433MHz transciever and publish it to a MQTT broker like mosquitto.

It is configured to send data to locallhost expecting the broker to run on the RPI.

It is based on the RFSniffer from [433Utils](https://github.com/ninjablocks/433Utils) and is best placed beside RFSniffer in RPI_utils

To compile it is necessary to change the Makefile as follows.
When copy pasting from here one problem remains as the markdown interpreter replaces the tabular with 8 spaces.
The lines which are intended, so always the second line after a `:`. Replace it with a tabular.

```make
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


To enable autostart at startup of the RPI copy the RFmqtt.service to
```
/lib/systemd/system/
```
And change the path of ExecStart to match your path to RFmqtt.

Make the file executable
```
sudo chmod 644 /lib/systemd/system/RFmqtt.service 
```
Reload the systemctl deamon, enable, start and view the status of the RFmqtt.service.
```
sudo systemctl daemon-reload
sudo systemctl enable RFmqtt.service
sudo systemctl start RFmqtt.service
sudo systemctl status RFmqtt.service
● RFmqtt.service - 433MHz Receiver on RPI GPIO sending to MQTT Broker
   Loaded: loaded (/lib/systemd/system/RFmqtt.service; enabled; vendor preset: enabled)
   Active: active (running) since xxx xxxx-xx-xx xx:xx:xx UTC; xxmin ago
 Main PID: 6748 (RFmqtt)
   CGroup: /system.slice/RFmqtt.service
           └─6748 /home/pi/SourceCode/433Utils/RPi_utils/RFmqtt
```

You can also define paramter in the RFmqtt.service at startup.
```
ExecStart=/home/pi/SourceCode/433Utils/RPi_utils/RFmqtt [-h Host] [-p Port] [-u username] [-x password] [-t topic] [-g WiringPI GPIO] [-w pulsewith]
```
