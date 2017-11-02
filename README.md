**CONTENTS**
- [Raspberry Pi Setup](#raspberry-pi-setup)
    - [Download this repository](#download-this-repository)
    - [Install Graphical git client (Optional)](#install-graphical-git-client-optional)
        - [GitKraken Windows SSH](#gitkraken-windows-ssh)
        - [GitKraken Linux SSH](#gitkraken-linux-ssh)
    - [Configure Raspberrypi Git](#configure-raspberrypi-git)
        - [Setup SSH keys](#setup-ssh-keys)
    - [Connect SSH](#connect-ssh)
    - [Connect VNC](#connect-vnc)
- [Modules](#modules)
    - [Mosquitto](#mosquitto)
        - [Quick test](#quick-test)
        - [Wildcard Topic Subscriptions](#wildcard-topic-subscriptions)
    - [Mosquitto from C](#mosquitto-from-c)
    - [Qt creator](#qt-creator)
        - [Create a project](#create-a-project)
        - [Signal and Slot](#signal-and-slot)
    - [Mosquitto from Qt](#mosquitto-from-qt)
        - [PRACTICE](#practice)
        - [TASK: C++ Thermostat](#task-c-thermostat)
    - [QML basics](#qml-basics)
        - [Sections to read.](#sections-to-read)
        - [Basic components](#basic-components)
        - [TASK: QML interface](#task-qml-interface)
    - [Mosquitto + Qt + QML](#mosquitto-qt-qml)
        - [TASK QML MQTT client](#task-qml-mqtt-client)
    - [TASK: QML Thermostat](#task-qml-thermostat)
- [Challenges](#challenges)
- [Questions](#questions)
    
# Raspberry Pi Setup
This repository contains all the binaries that you may need along with all the instructions to setup the raspberry pi.

## Download this repository
In the overview tab there is a button just bellow the repository name that allows you to download the full repository as a package.

## Install Graphical git client (Optional)
This is usefull if you want to have the repositories in your own computer. 
You are free to use any git client of your choice if you are familiar with it. we will be using GitKraken.

### GitKraken Windows SSH
- Run the installer in the Binary folder
- Open preferences > Authentication  and click in "Generate" button to generate a key.
- Copy the public key and add it to your profile in GitLab.

### GitKraken Linux SSH
- Instal the .deb package.
- Use ssh-keygen command to generate a key.
- Add the key to GitKraken in preferences > Authentication.
- Add the public key to your GitLab profile.

## Configure Raspberrypi Git
If you want to share code using the GitLab Server, you will need to register and to setup a SSH key.

### Setup SSH keys
- cd to ~/.ssh/
- ssh-keygen -t rsa
- set name to "id_rsa"
- left passphrase blank
- Add the id_rsa.pub file content to your GitLab account settings > ssh keys.

With this information, now you should be able to use git push and git pull without password.

## Connect SSH 
If you are familiar with ssh, you can use any client of your choice, we will use MobaXTerm for windows and regular terminal for Linux.

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

## Qt creator
**Definition from Wikipedia**

*Qt (/kjuːt/ "cute") is a cross-platform application framework that is used for developing application software that can be run on various software and hardware platforms with little or no change in the underlying codebase, while still being a native application with native capabilities and speed.* 

We are using Qt mainly for the simplicity of the code, if you are already familiar with it, you may skip this section.

### Create a project
- Launch the Qt creator app from the app panel in app **"Programming > Qt Creator"**.
- Select the tab **"Welcome"** from the left panel and click in **New project**.
- Select the project type **"Application"** and choose **"Qt Console Application"** if you don't need an interface or **"Qt Quick controls 2 Application"** if you want a QML interface.
- Select the project folder and name, It's a good idea to keep all the Qt Projects inside a folder named  **"QtProjects"**, provably in the home directory or in Documents folder. *Avoid spaces in the file path*.
- Select the compilation kit, you only have the Desktop kit in the Raspberry pi.
- Click play on the down left part of the screen to test the project.

If you have a Console application, you won't see anything. To check if it's working you can add a "Hello world" print with the following code.
```
#include <QCoreApplication>
// For debug prints
#include <QDebug>

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);
    // Prints "Hello World!" and a line break
    qDebug() << "Hello World!" << endl;

    return a.exec();
}
```
### Signal and Slot 
We are going to use the Signal and Slot communication available in the Qt framework to handle the events. You can read about the functionality in the Qt Creator documentation.

[Qt Core > Core functionalities > Signals & Slots](qthelp://org.qt-project.qtcore.571/qtcore/signalsandslots.html)

In this project you only need to know how to create a handler function and connect the signal to it. This is done with the connect function.
```
// Example of the connection of the connected signal 

// header
public slots:
    void onConnected();

// source
void MyDerivedClass::onConnected(){
    //do something
}

// connection, provably on constructor
...
// connect(Sender object , Sender Signal, Receiver object, Receiver Slot)
connect(this, SIGNAL(connected()), this, SLOT(onConnected()));
...

```
Sending a signal it's just like calling the function but adding the word emit before it.
```
// Header
signals:
    void onMessageReceived();

// Usage
emit onMessageReceived();
```


## Mosquitto from Qt
[QMQTT](https://github.com/emqtt/qmqtt) is an implementation of the mqtt protocol in Qt.

We have compiled and installed the libraries for the raspberry so you can use them in any project including it in the .pro file of your project.
```
QT += mqtt
```
Then you can access the classes with

```
#include "qmqtt.h"
```
The class QMQTT::Client contains all that we need. It has a lot of functionality, but we only need the most basic of them.

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

### PRACTICE
Build a simple QT/C++ application that receives and sends messages.

**Hints**

- Create first an application that send a message on run and catch the message from a terminal using the utility mosquitto_sub.
- Then, subscribe to a channel and send the messages using mosquitto_pub.
- finally, reply to received messages with other messages to a different channel.

### TASK: C++ Thermostat 
Implement a class named Thermostat to handle the Thermostat behaviour with the following interface.

You can clone it from http://192.168.0.2:8081/PerformanceIOT/ThermostatLogic

**You are free to create method that you need, but you can't remove any method**

```
#ifndef THERMOSTAT_H
#define THERMOSTAT_H

#include <QObject>
#include "qmqtt.h"

class Thermostat : public QMQTT::Client
{

public:
    explicit Thermostat(
           const QString baseMQTTTopic = "device1/",
           const QHostAddress& host = QHostAddress::LocalHost,
           const quint16 port = 1883,
           QObject* parent = NULL);
    
    
    bool getHeaterStatus();
    float getTemp() const;
    float getHumidity() const;
    float getMaxTemp() const;
    float getMinTemp() const;

signals:
    void heaterStatusChanged();
    void tempChanged();
    void humidityChanged();
    void maxTempChanged();
    void minTempChanged();

public slots:
    void setMaxTemp();
    void setMinTemp();

private:
    // we need to append this to all the paths, this will help to separate messages from different sensors but keeping the same interface.
    QString baseMQTTTopic;

    // Suggestion: implement a function like this that returns the full path ready to use.
    QString getTopic(QString topic);

    // Topics
    const QString temperatureValueTopic = "sensor/temp";
    const QString humidityValueTopic = "sensor/humidity";
    const QString switchValueTopic = "sensor/switch";
    
    const QString ledStatusTopic = "indicator/led";
    const QString relayStatus = "indicator/relay";
    
    const QString maxTempValueTopic = "config/maxTemp";
    const QString minTempValueTopic = "config/minTemp";
    
};

#endif // THERMOSTAT_H
```

This application should act as an Interface between the LowLevel and HighLevel applications. 

**Code example**

You have an example in Gitlab/qmqtt.

```The url may be incorrect, replace the address with the server ip```

http://192.168.0.164/PerformanceIOT/qmqtt/blob/master/examples/mqtt/client/example.cpp

it contains an example of how to inherit the QMQTT::Client and add functionality. you can start from here if you find it too hard.

## QML basics

Inside QtCreator, there is a Help tab where you can get all the documentation. 

### Sections to read.
This secctions of the Qt Creator help may be useful.
- **Qt QML > Guides and other information > The QML reference**: General information about QML engine.
- **Qt Quick > Positioning**: information about how to position items in qml 
- **Qt Quick Controls > QML types**: general purpose controls. 
- **Qt QML > Integrating QML with C++ > Integrating QML and C++**: How to connect C++ and QML, we need this to connect mosquitto to QML.

You can begin creating a new qml project with the steps indicated in [Create a project](#create-a-project) section. 

### Basic components

This is a list of the basic elements that you may need to create.
- **Rectangle**: Creates a simple rectangle with color. useful for simple indicators.
- **Button**:You can create simple buttons that emit signals onClicked.
- **LabeL**: Contains text.
- **TextField**: User text input.  
- **Row/Column/Flow**: Are container items that automatically arrange the contained objects .
- **Timer**: fires events at a desired interval.

if you type the component in a QML file, Qt creator will recognize it and highlight it in purple. If you select it and press F1, the item help will popup.

### TASK: QML interface

- Create a simple interface with a **Button** that prints in the console and updates the text in a label with the message inside a **TextField**.

## Mosquitto + Qt + QML
We already have a Qt class that handles the messages from mosquitto, what we need now, is to export this class to QML engine in order to get the mosquitto messages in a QML class.

There is an special function that must be called in main.cpp before the creation of the QML engine to register our custom class.

```
    // qmlRegisterType<MyType>(QML impor statement, major version, minor version, QML item name)
    qmlRegisterType<QmlClient>("QMQTT",1,0,"MQTTClient");
```
This will register the class QmlClient in the QML engine import path QMQTT 1.0 with the name MQTTClient.

Now we only need to create the object to use it. This is how you can use the class that we are going to implement in the following section.

```
import QMQTT 1.0
...
    MQTTClient{
        id: qmqtt
        hostip: "192.168.0.164"
        subscribeTopic: base.groupId + "/#"
        onNewMessage: {
            console.log(topic +": " + message)
        }
    }
...
```


### TASK QML MQTT client

Create a simple application with GUI that allows you to send messages over MQTT.

Implement a class named QmlClient that implements the following interface.
This will allow us to control MQTT from QML engine

**You are free to create method that you need, but you can't remove any method**
```
#ifndef QMLCLIENT_H
#define QMLCLIENT_H

#include <QObject>
#include "qmqtt.h"


class QmlClient : public QObject
{
    Q_OBJECT
    Q_PROPERTY(QString hostip READ hostString WRITE setHostString NOTIFY hostStringChanged)
    Q_PROPERTY(int port READ port WRITE setPort NOTIFY portChanged)
    Q_PROPERTY(bool isConnected READ isConnected NOTIFY isConnectedChanged)
    // The topic to be subscribed. 
    Q_PROPERTY(QString subscribeTopic READ subscribeTopic WRITE setSubscribeTopic NOTIFY subscribeTopicChanged)

public:
    explicit QmlClient(QObject *parent = nullptr);

    QString hostString() const;
    void setHostString(const QString host);

    int port() const;
    void setPort(const int port);

    bool isConnected() const;
    void setIsConnected(const bool status);

    QString subscribeTopic() const;
    void setSubscribeTopic(const QString topic);


signals:
    void hostStringChanged();
    void portChanged();
    void isConnectedChanged();
    void subscribeTopicChanged();

    void newMessage(QString topic, QString message);

public slots:
    void publishMsg(const quint16 id, const QString &topic, const QString &message);
    void subscribed(QString topic);
    void received(QMQTT::Message message);


private:
    // Internal client, mainly we need to wrap the functions from this class.
    QMQTT::Client *_client;

};

#endif // QMLCLIENT_H

```  

Create different **TextField** to allow the user to enter the IP, port, Topic and Message and a button to send the message. You can start from the application that we did in the [QML basics](#qml-basics).

**HINT**: You will find useful the QML item **GridLayout** to arrange the items in a cool from way. 

## TASK: QML Thermostat 
Starting from the last application, you can subscribe to the high level messages from your previous app and create a visual interface to the Thermostat.

**Remember to run both apps at the same time.**

# Challenges

- Add extra functionalities
    - Humidity threshold.
    - Day/Night configuration.
    - Manual mode.
- Improve interface.
    - Add information about connection status, server ip...
    - Add Children-Lock system.

# Questions

Which technology is not being used in this project?
- C++
- JavaScript 
- Mosquitto
- C# (X)

If you want to subscribe to all the temperatures from the sensors in the room knowing that the pattern is /group/sensor/temp/. which of this wildcards is valid.

- /#/sensor/temp
- /+/sensor/temp (X)
- /+/temp
- /+/#






