**CONTENTS**
- [Raspberry Pi Setup](#raspberry-pi-setup)
    - [Download this repository](#download-this-repository)
    - [Install git client](#install-git-client)
        - [Windows SSH](#windows-ssh)
        - [Linux SSH](#linux-ssh)
    - [Connect SSH](#connect-ssh)
    - [Connect VNC](#connect-vnc)
- [Modules](#modules)
    - [Mosquitto](#mosquitto)
        - [Quick test](#quick-test)
        - [Wildcard Topic Subscriptions](#wildcard-topic-subscriptions)
    - [Mosquitto from C](#mosquitto-from-c)
    - [Mosquitto from Qt](#mosquitto-from-qt)
        - [TASK](#task)
    - [Building our first QML GUI](#building-our-first-qml-gui)
    
# Raspberry Pi Setup
This repository contains all the binaries that you may need along with all the instructions to setup the raspberry pi.

## Download this repository
In the overview tab there is a button just bellow the repository name that allows you to download the full repository as a package.

## Install git client
You are free to use any git client of your choice if you are familiar with it. we will be using GitKraken.

### Windows SSH
- Run the installer in the Binary folder
- Open preferences > Authentication  and click in "Generate" button to generate a key.
- Copy the public key and add it to your profile in GitLab.

### Linux SSH
- Instal the .deb package.
- Use ssh-keygen command to generate a key.
- Add the key to GitKraken in preferences > Authentication.
- Add the public key to your GitLab profile.

## Connect SSH 
If you are familiar with ssh, you can use any client of your choice, we will use MobaXTerm for windows.

- Launch MobaXTerm portable from binaries.
- Get your RPI IP Address, we will tell to each group the right one.
- Create a new session (Top left) using the IP and one of the following users with password "frambuesa"
    - pi
    - pi2
    - pi3

At this point you should be able to get full control of your RPI.

## Connect VNC
This allows you to have an screen in the RPI.

- Install RealVNC from binaries.
- For user pi, the virtual desktop already exist, for other users you have to launch it with the following command

```
vncserver -geometry 1280x720
```
*You can specify a different resolution if you want. keep in mind that you will be up to 3 people in the same device.*

- keep note of the screen, in the case is :1

```
New desktop is raspberrypi:1 (192.168.0.159:1)
```
- Create the connection with RealVNC using the ip from the previous command.
- Introduce your user and password *pi, pi2 or p3 and "frambuesa"*

At this point you should be able to use the raspberry pi as a normal desktop computer.

# Modules
## Mosquitto

*Eclipse Mosquitto™ is an open source (EPL/EDL licensed) message broker that implements the MQTT protocol versions 3.1 and 3.1.1. MQTT provides a lightweight method of carrying out messaging using a publish/subscribe model. This makes it suitable for "Internet of Things" messaging such as with low power sensors or mobile devices such as phones, embedded computers or microcontrollers like the Arduino.*

It's included in the raspbian default ppa repository so using mossquitto is as easy as.
```
sudo apt install mosquitto mosquitto-clients mosquitto-dev
```
This will setup a local mosquitto server along with the clients to interact with it. The dev package is used for creating C++ programs that we will be using later.

### Quick test
To give a quick test you can open two different terminals and send messages from one to the other.

The communication is publisher/subscriber based and the messages are identified by a topic defines with the paramter -t.

In the first terminal we setup a subscriber with this command.
```
mosquitto_sub -t /test
```
and then we send a message from the other terminal with
```
mosquitto_pub -t /test -m hi
```
If everything is ok, you should see the message in the subscriber.

Now you should add the -h (host) parameter to point the common host to share your messages with others.

### Wildcard Topic Subscriptions
This is copy/paste from the official docs

In addition to allowing clients to subscribe to specific topics, mosquitto also allows the use of two wildcards in subscriptions. + is the wildcard used to match a single level of hierarchy. For example, for a topic of "a/b/c/d", the following example subscriptions will match:
```
    a/b/c/d
    +/b/c/d
    a/+/c/d
    a/+/+/d
    +/+/+/+
```

The following subscriptions will not match:

```
    a/b/c
    b/+/c/d
    +/+/+
```

The second wildcard is # and is used to match all subsequent levels of hierarchy. With a topic of "a/b/c/d", the following example subscriptions will match:

```
    a/b/c/d
    #
    a/#
    a/b/#
    a/b/c/#
    +/b/c/#
```

The $SYS hierarchy does not match a subscription of "#". If you want to observe the entire $SYS hierarchy, subscribe to $SYS/#.

Note that the wildcards must be only ever used on their own, so a subscription of "a/b+/c" is not valid use of a wildcard. The # wildcard must only ever be used as the final character of a subscription.

## Mosquitto from C
There is a C library but we are not using this, if you are interested in this topic there is a lot of information in the official page.

## Mosquitto from Qt
[QMQTT](https://github.com/emqtt/qmqtt) is an implementation of the mqtt protocol in Qt.

We have compiled and installed the libraries for the raspberry so you can use them in any project including it in the .pro file of your project.
```
QT += mqtt
```
Then you can access the clases with

```
#include "qmqtt.h"
```
The class QMQTT::Client contains all that we need. It has a lot of functions, but we only need the most basic of them.

Just like any other Qt object, QMQTT::Client is event driven and contains a lot of useful signals to subscribe. you can get the list directly from the qmqtt_client.h.

This is an example of a derived class constructor connecting the desired signals.
```
explicit Subscriber(const QHostAddress& host ,
                    const quint16 port ,
                    QObject* parent = NULL)
    : QMQTT::Client(host, port, parent)
    , _qout(stdout)
{
    connect(this, SIGNAL(connected()), this, SLOT(onConnected()));
    connect(this, SIGNAL(subscribed(QString,quint8)), this, SLOT(onSubscribed(QString)));
    connect(this, SIGNAL(received(QMQTT::Message)), this, SLOT(onReceived(QMQTT::Message)));
    this->setAutoReconnect(true);
    this->connectToHost();
}
```
Also, you have a function to send messages.
```
// Send a message with topic and string payload, id should be unique
void Subscriber::publishMsg(const quint16 id, const QString &topic, const QString &message)
{
    QMQTT::Message message_object(id, topic, message.toUtf8());
    this->publish(message_object);
}
```
If you check the class QMQTT::Message you will get an idea of the information contained in the messages.
### TASK
Build a simple QT/C++ application that receives and sends messages.

**Hints**

- Create first an application that send a message on run and catch the message from a terminal using the utility mosquitto_sub.
- Then, subscribe to a channel and send the messages using mosquitto_pub.
- finally, reply to received messages with other messages to a different channel.
- 
**Solution**

You have an example in Gitlab/qmqtt.

```The url may be incorrect, replace the address with the server ip```

http://192.168.0.164/PerformanceIOT/qmqtt/blob/master/examples/mqtt/client/example.cpp

## Building our first QML GUI
Inside QtCreator, there is a Help tab where you can get all the documentation. 

We will specially use the following pages:
- Qt QML > Guides and other information > The QML reference 
- Qt QML > Integrating QML with C++ > Integrating QML and C++
  


