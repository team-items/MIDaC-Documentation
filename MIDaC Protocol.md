#Modular Information Display and Control Protocol (MIDaC)



<li>Server -> Client: ConnACK or ConnREJ (Connection Acknowledgement or Connection Rejection)</li>
</ol>




<ol>
</ol>

##Types
* Float - Maximum[Float], Minimum[Float], Graph[Integer, Optional]



###Accessing properties and controls



__ConnLAO__: Information (JSON Objects)[Containing all the properties that are transmitted to the clients and their description], Controller (JSON Objects)[Containing all the control types and their descriptions]

Controls (sliders and buttons) can exist alone or in a group of exactly 1 button and 1 slider. Each control hast to have a globally unique name and each group has to have a different globally unique name. 
##Example Messages
 
####ConnREQ example
```JSON
}
```
####ConnACK example
```JSON
	"ConnACK" : {
}
```
```JSON
{
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
{
}
```
####Data message example
	"ConnSTT" : {} 
}
```
```JSON
{
}
```JSON
{
}