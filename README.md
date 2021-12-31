# gettingStartedIntegration

GettingStartedIntegration reads the messages from the files stored in [dataIn](/dataIn) directory and print them in the application console or write in files.

#### Requirements
Compatibility: Java 11

Download or clone the project

Import this project as a Gradle project in Eclipse 

or 

Open this project in Intellij
#### Running the application
Run the main as a Java project: https://github.com/charroux/gettingStartedIntegration/blob/master/src/main/java/test/Main.java

XML configuration files are into: https://github.com/charroux/gettingStartedIntegration/tree/master/src/main/resources

## [applicationContext.xml](/src/main/resources)

This file contains the poler configuration to read messages from the files with ‘.txt’ extension stored in dataIn directory inside project and print in the console.
	

        
#### channelEntree
This is entry channel, in which data will be transformed.

#### inbound-channel-adapter	
This channel adapter is used to read the files content stored in dataIn directory. It has following parameters.
###### 1) Channel
This is the input channel entry.
###### 2) Directory
   The directory where the data will be stored.
###### 3) Filename-pattern
   The file pattern, in which the data is formatted. In this case the file is in text format. The data will be read and transferred in text format.
###### 4) int-poler
   This is poler which polls the data after each second.

#### file-to-string-transformer
This transformer is used to read data from channelEntree and transform into responseChannel with charset UTF-8
format. After reading the messages files will not be deleted in this case.

#### ResponseChannel

This is response channel, transformer will read the data from channelEntree and transfer into responseChannel.
#### logging-channel-adapter

The channel adapter which is used to log every message in response channel in application console.

#### Description
The inbound-channel-adapter reads the data from all files ends with ‘.txt’ extension stored inside dataIn directory after each second and puts the response in channelEntree channel, file-to-string-transformer will read the
channelEntree and transfer each file data into string format and put it in responseChannel. Now the logging-channel- adapter prints the logs of responseChannel on console.

## [filtering.xml](/src/main/resources)
This file contains the poler configuration to read the files with ‘.txt’ extension stored in dataIn directory inside project and apply filter on the headers.
#### ChannelEntree

This is entry channel, in which data will be transformed.
	
#### inbound-channel-adapter
This channel adapter is used to read the files content stored in dataIn directory. It has following parameters.
###### 1) Channel
   This is the input channel entry.
###### 2) Directory
   The directory where the data will be stored. 
###### 3) Filename-pattern
   The file pattern, in which the data is formatted. In this case the file is in text format. The data will be read and transferred in text format.
###### 4) int-poler
   This is poler which polls the data after each second.

#### file-to-string-transformer
This transformer is used to read data from channelEntree and transform into responseChannel with charset UTF-8
format. After reading the messages files will not be deleted in this case.

#### ResponseChannel
This is response channel transformer will read the data from channelEntree and transfer into responseChannel.
#### logging-channel-adapter
The channel adapter which is used to log every message in response channel in application console.

#### header-filter input-channel
The filter applied on inputChannel and ResponseChannel.

#### Description
The inbound-channel-adapter reads the whole file ends with ‘.txt’ extension stored inside dataIn
directory at once after each second and puts the response in channelEntree channel. The file-to-string-transformer will convert the file data from channelEntree channel and put this string into the responseChannel, the files will not be deleted after reading from the folder because delete-files is false. The header-filter is applied on lastName and state. Now the logging-channel-adapter prints the logs of responseChannel on console.


## [serviceActivator.xml](/src/main/resources)

This file contains the configuration to read messages from files insde dataIn directory and store these messages in the files inside dataOut directory.

#### inbound-channel-adapter

This is an inbound channel, which contains polling configuration that sends the message from files in dataIn directory. It
has following parameters.
###### 1) Channel
   This is the input channel entry.
###### 2) Directory
   The directory of input data, where input files are stored. Data from these files are transported over the channel.
###### 3) Filename-pattern
   The file pattern, in which the data is formatted. In this case the file is in text format. The data will be read and transferred in text format.
###### 4) int-poler
   This is poler which polls the data after each second.
#### file-to-string-transformer

The transformer which transforms the file data to string. It puts the data from channelEntree to responseChannel.

#### service-activator
The endpoint type for connecting Account object to an input channel(responseChannel) so that it may play the role of a service. Now the Account bean will act as a service which produces an output (The create method in Account bean) in string format. So, it will be connected an output channel which is responseChannel.

#### outbound-channel-adapter

The channel adapter for receiving the messages and store them in text files inside dataOut directory.

#### Description
The inbound-channel-adapter reads files with extension .txt’ stored inside dataIn directory at once after
each second and puts the response in channelEntree channel. Now file-to-string-transformer will read the file data from channelEntree put it into the responseChannel during this process files will not be deleted from the directory. At this point service-activator will read message from responseChannel and execute the 'create’ method inside Account class and print it on the console, also the response of this method will be loaded on outChannel. At receiving end outbount- channel-adapter will read the messages from outChannel and store them inside dataOut directory.

## [routing.xml]((/src/main/resources))

This file contains the configuration to read JSON file from dataIn directory and store them in different files iside fileOut1 and fileOut2 directories as individual account.json files.
#### inbound-channel-adapter

This is an inbound channel, which contains polling configuration that polls the message source. It has following
parameters.
###### 1) Channel
   This is the input channel entry.
###### 2) Directory
   The directory of input data, where input files are stored.
###### 3) Filename-pattern
   The file pattern, in which the data is formatted. In this case the file is in JSON format. The data will be read and transferred in JSON format.
###### 4) int-poler
   This is poler which polls the data after each second.
#### json-to-object-transformer

The transformer which is used to transform input from channelEntree, it will automatically transfer the data, put it out in
the object(test.MyAccount) and pipe that into the mainChannel.

#### recipient-list-router

This is the router list which gets the messages from mainChannel and route this to serviceActivatorChannel and
channel2.

#### Bean

Configuration for AccountService bean.

#### service-activator

A service activator which connects the messages on serviceActivatorChannel and objectTransformer1. This is used to connect service layer which is serviceID(AccountService) with adapter layer.

#### object-to-json-transformer (id=t1)

The transformer which is used to transform input from objectTransfer1, it will automatically transfer the data to pipe
that into the filesOut1 channel.


#### json-to-object-transformer(id=t2)

The transformer which is used to transform input from channel2, it will automatically transfer the data to pipe that into
the filesOut2 channel.

#### outbound-channel-adapter(id=f1)


An outbound channel which is used to read files from filesOut1 channel and store them dataOut/test1 directory.

#### outbound-channel-adapter(id=f2)

An outbound channel which is used to read files from filesOut2 channel and store them dataOut/test2 directory.


#### Description 
The inbound-channel-adapter reads the file 'account.json' stored inside dataIn directory at once after
each second and puts the response in channelEntree channel. Now json-to-object-transformer will read the JSON from channelEntree convert it into the MyAccount object and put it into the mainChannel. Then recipient-list-router will read mainChannel and distribute the message into serviceActivatorChannel and channel2. At this point service-activator will read message from serviceActivatorChannel and execute the ‘myService’ method inside MyAccount class and print it on the console, also the response of this method will be loaded on objectTransformer1 channel. Now at the receiving end object-to-json-transformer(id=t1) will read the input from objectTransformer1 channel and put it in fileOut1 channel. Here, the outbound-channel-adapter(id=f1) will get data from fileOut1 channel, put it into the file inside dataOut directory called test1. The object-to-json-transformer(id=t2) will read the input from channel2 and put it in fileOut2 channel. Here, the outbound-channel-adapter(id=f2) will get data from fileOut2 channel, put it into the file inside dataOut directory called test2.

## [aggragate.xml](/src/main/resources)
This file contains the configuration to read messages from ‘account.json’ file.

#### inbound-channel-adapter
This is an inbound channel, which contains polling configuration that sends the messages to source from the file. It has
following parameters.
###### 1) Channel
   This is the input channel entry.
###### 2) Directory
   The directory where the files are stored.
###### 3) Filename-pattern
   The file pattern, in which the data is stored. In this case the file is in JSON format. The data will be read and transferred in JSON format.
###### 4) int-poler
   This is poller which polls the data after each second.
   
#### json-to-object-transformer
The transformer which is used to transform input from channelEntree, it will automatically transfer the data, put it out in the object(test.MyAccount) and pipe that into the mainChannel.

#### recipient-list-router

A router list which gets the messages from mainChannel and route this to serviceActivatorChannel and aggregatorChannel.

#### Bean

Configuration for MyService bean.


#### service-activator

A service activator which connects the messages on serviceActivatorChannel and aggregatorChannel. This is used to connect service layer which is serviceID(AccountService) with adapter layer.

#### Aggregator

The aggregator which combines messages the from aggregatorChannel and outputChannel.
#### logging-channel-adapter

The adapter which is used for logging the upstream of output channel at INFO level.

#### Description

The inbound-channel-adapter reads the file account.json from dataIn directory after each second and
puts the response in channelEntree channel, json-to-object-transformer is applied now to read the JSON object from channelEntree, convert it into ‘MyAccount’ object and put it in mainChannel. Now the recipient-list-router reads the mainChannel and routes the messages towards serviceActivatorChannel and aggregatorChannel. At this point the service-activator will read the input from serviceActivatorChannel and executes the ‘AccountService’ service which will return the MyAccount object and put it into aggregatorChannel. After all the above proccess the aggregator will play its role now, it will combines the object from aggregatorChannel and outputChannel with correlation strategy applied on name field of MyAccount object and its size. The logging-channel-adapter prints the logs of output channel on console.




javaScriptService.xml

	- gateway
	
	- javascript
	
	- logger
	
webServiceCalling.xml

Existing Web Service project : https://github.com/charroux/webServiceForSpringIntegration

	-gateway
	
	- web service call
	
	- logger
	
sqlDatabase.xml

	- gateway
	
	- data base (hsqldb, h2...)
	
	- logger
