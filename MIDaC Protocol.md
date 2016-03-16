#Modular Information Display and Control Protocol (MIDaC)MIDaC Protocol is an open standard for data exchange between a client device and a robotics system over TCP/IP that is based on the JSON format for data serialization.
The MIDaC Protocol is part of the MIDaC toolkit which also includes proposals for MIDaC Protocol Client and Server implementations as well as a standard implementation known as MissionControl. The MIDaC toolkit is designed so that any part can be replaced with a third- party implementation as long as they comply with the requirements that are defined within the MIDaC toolkit.
The protocol includes two message types for data exchange (data messages and control messages) and two defined handshakes (one for connecting a client to the server and one for connecting a multi-platform server with a platform specific part for data readout and control).
##Client-Server-HandshakeAfter establishing a TCP/IP socket connection to the server a client initiates a handshake consisting of 4 parts:<ol><li>Client -> Server: ConnREQ (Connection Request containing information about the client device)</li>
<li>Server -> Client: ConnACK or ConnREJ (Connection Acknowledgement or Connection Rejection)</li><li>Server -> Client: ConnLAO (A message including a description of all data properties and controls that are available)</li><li>Client -> Server: ConnSTT (Acknowledgment for receiving ConnLAO)</li>
</ol>
These messages are, as well as any other message, JSON formatted. The ConnACK and ConnLAO messages are split because there is theoretical support for encryption in the MIDaC protocol and if used every message after the ConnACK has to be encrypted.
If successful the server starts to periodically transmit ‚Information Messages‘ containing the current values for every given data property. The client can send based on user input ‚Control Messages‘ which contain commands for described actuators that are part of the robotics system.##Server-RSAL-Handshake
The Robotics System Abstraction Layer (RSAL) is part of the MIDaC toolkit and represents an abstraction layer that makes it possible for a MIDaC server to be multi-platform. In it all the control and data-inquiry routines are defined and wrapped so that the routines itself are exchangeable for every system. The RSAL is connected to the server over TCP/IP sockets.
When the RSAL is connecting to the server a different handshake that consist out of 3 parts is used:
<ol><li>RSAL -> Server: B-Connect (A message containing an identifier that the server passed to the RSAL while calling it)</li><li> Server -> RSAL: ConnACK or ConnREJ (Same as with the Client-Server handshake but empty)</li><li>RSAL -> Server: ConnLAO (The ConnLAO message which is passed to every connecting client)</li>
</ol>

##Types###PropertiesA defined set of data types is used within MIDaC Information Messages:* Bool - Graph[Integer, Optional] * Integer - Maximum[Integer], Minimum[Integer], Graph[Integer, Optional] 
* Float - Maximum[Float], Minimum[Float], Graph[Integer, Optional]* String - Maximum length[Integer]These data types feature unique properties which are transmitted with the ConnLAO message that makes it easier to enhance them for presentation.
###Controls
There are two types of controls supported by MIDaC, each with unique properties transmitted with the ConnLAO message:* Sliders - Maximum[Integer], Minimum[Integer], AutoUpdate[String representation of bool]* Buttons - Descriptor[String]

###Accessing properties and controls
Within MIDaC messages properties and controls are accessed via unique names. These names can also be used to display them on client devices. The names are specified within the controller specific part and are up to the implementing party. The names are used as identifiers in the information and control messages.##Handshake messagesA listing of all the properties of the handshake messages is now given. When encryption is used all messages from and including the ConnLAO message are encrypted.__ConnREQ__: HardwareType (String)[Type of hardware, can be: Web, PC, Tablet or Smartphone]
__ConnACK__: SegmentSize (Integer)[Size of the segments used by the sockets]
__ConnREJ__: Error (String)[Error message] 
__ConnLAO__: Information (JSON Objects)[Containing all the properties that are transmitted to the clients and their description], Controller (JSON Objects)[Containing all the control types and their descriptions]__ConnSTT__: This message just includes an empty object.__B-Connect__: This message just includes an empty object.###Information messagesInformation messages are the messages that are used to perform the monitoring. They transmit properties like sensor values, status messages, etc.###Control messagesControl messages are the messages that are used, if supported, to perform commands on the controller. They contain a value for a property that is defined in the ConnLAO message. A processing routine for the command has to be implemented on the controller side. While for sliders the value is sent, for buttons it’s just a string ‚click‘.When a slider is set to AutoUpdate for every interaction the updated value has to be sent.###ConnLAO message layoutThe ConnLAO message consists of two parts, the definition of the data properties („Information“) and the definition of the controls („Controller“).Each property has a unique identifier, is of a certain type and can have attributes specific to its datatype (see „Properties“). Properties must be grouped by their data type (see example ConnLAO message)

Controls (sliders and buttons) can exist alone or in a group of exactly 1 button and 1 slider. Each control hast to have a globally unique name and each group has to have a different globally unique name. 
##Example Messages
 
####ConnREQ example
```JSON {	"ConnREQ" : {       "HardwareType" : "Smartphone"	} 
}
```
####ConnACK example
```JSON{	
	"ConnACK" : {       "SegmentSize" : 2048	} 
}
```####ConnREJ example
```JSON
{	"ConnREJ" : {		"Error" : "Some error because reasons"	}
}
```
####ConnLAO example
```JSON
{
    "ConnLAO": {
        "Information": {
            "Integer": {
                "Analog 1": {
                    "DataType": "Integer",
                    "MinBound": 0,
                    "MaxBound": 1023,
                    "Graph": 20
                },
                "Analog 2": {
                    "DataType": "Integer",
                    "MinBound": 0,
                    "MaxBound": 1023,
                    "Graph": 20
                }
            },
            "Bool": {
                "Digital 1": {
                    "DataType": "Bool",
                    "Graph": 20
                },
                "Digital 2": {
                    "DataType": "Bool",
                    "Graph": 20
                }
            }
        },
        "Controller": {
            "Servo 1": {
                "Slider S1": {
                    "ControlType": "Slider",
                    "MinBoud": 1,
                    "MaxBound": 2047
                }
            },
            "Servo 2": {
                "Slider S2": {
                    "ControlType": "Slider",
                    "MinBoud": 1,
                    "MaxBound": 2047
                }
            },
            "Motor 1": {
                "Motor 1 Slider": {
                    "ControlType": "Slider",
                    "MinBoud": 1,
                    "MaxBound": 1500
                },
                "Motor 1 Button": {
                    "ControlType": "Button"
                }
            },
            "Motor 2": {
                "Motor 2 Slider": {
                    "ControlType": "Slider",
                    "MinBoud": 1,
                    "MaxBound": 1500
                },
                "Motor 2 Button": {
                    "ControlType": "Button"
                }
            }
        }
    }
}
```
####ConnSTT example

```JSON
{	"ConnREJ" : {		"Error" : "Some error because reasons"	}
}
```
####Data message example```JSON{
	"ConnSTT" : {} 
}
```####Control message example
```JSON
{	"Control" : {		"SomeButton" : "click"      	"SomeSlider" : 76,	} 
}```####B-Connect message example
```JSON
{	"B-Connect" : {} 
}```