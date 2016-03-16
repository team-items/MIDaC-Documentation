#MC2ML 
MC2ML is the MissionControl Controller Markup Language, an XML based language designed to make it as easy as possible to customize the data accessed and the actuators controlled by a MissionControl server as well as making it more simple to add support to new robotics controllers. An MC2ML file is converted into an C file and then automatically compiled using GCC with the options specified by you.

##Structure
MC2MLs root element is the controller tag. The structure of a MC2ML file should be like this:

```XML
<?xml version="1.0"?>
<Controller>
	<Libraries>...</Libraries> //Your libraries and compile flags
	<Support>...</Support> //Support functions and  global variables that you need for your data readout or control
	<Init>...</Init> //A function called right after program start
	<Monitor></Monitor> //The properties displayed by MissionControl, their attributes, settings and accessing functions
	<Control></Control> //The controls displayed by MissionControl, their attributes, settings and control functions
</Controller>
```
###Inculding Libraries
To include the libraries necessary for your controller and to set compile flags the _Libraries_ element is used:

```XML
<Libraries>
	<Flag>-lkovan</Flag>
	<Include>kovan/kovan.h</Include>
</Libraries>
```
###Adding additional global variables and supporting functions
You can add global variables and functions you need directly into the MC2ML file like this:

```XML
<Support>
	<Function>
		int onePlusOne(int one){
			return one+1;
		}
	</Function>
	<Variable>
		int two = 2;
	</Variable>
</Support>
```
###Initializers
There is often a function that has to be called to enable things like motor control, these functions can be put into the _Init_ tag:

```XML
<Init>
	enable_everything();
</Init>
```

###Data access
To tell the server which data to deliver to the clients it has to be defined wihtin the MC2ML's _Monitor_ element. The data fields are grouped in elements of the same data type, the elements of an specific type include the same information as specified in the MIDaC Protocol definition. 

```XML
<Monitor>
	<Integer>
		<IntegerValue>
			<Name>Some Int</Name> //name that is used to display it
			<MinBound>0</MinBound> //minimum value to expect
			<MaxBound>1023</MaxBound> //maximum value to expect
			<Graph>20</Graph> //how many elements the graph should show 
			<Getter>getterFunction(0)</Getter> // getter function must return an integer, variables from the support tag can be used as parameters
		</IntegerValue>
	</Integer>
	
	<Float>
		<FloatValue>
			<Name>SomeFloat</Name>
			<MinBound>0.0</MinBound>
			<MaxBound>1.0</MaxBound>
			<Graph>100</Graph>
			<Getter>0.5</Getter> //getter function must return an float 
		</FloatValue>
	</Float>	
	<Bool>	
		<BoolValue>
			<Name>Almost always True</Name>
			<Graph>20</Graph>
			<Getter>"True"</Getter> //getter function must return the strings "True" or "False"
		</BoolValue>
		<BoolValue>
			<Name>Always False</Name>
			<Graph>20</Graph>
			<Getter>"False"</Getter>
		</BoolValue>
	</Bool>
</Monitor>
```

###Controlling the controller
MissionControl allows you to control the controller, in order to achieve that the controls and the actions called have to be specified in the MC2ML file. Controls can be Sliders (representation of an integer value) or Buttons or a group consisting of exactly 1 Slider and 1 Button. For Sliders a global variable is created in which the last received value is stored automatically. It can be accessed via the sliders name (if there are spaces in the name, these have been automaticall replace with underscores.)

```XML
<Control>
	<Group>
		<Name>Group 1</Name>
		<Slider>
			<Name>Slider 1</Name>
			<MinBound>-1500</MinBound>
			<MaxBound>1500</MaxBound>
			<Action>
				//C code that is called whenever a new slider value is received
				printf("Slider one now at value %d\n", Slider_1);
			</Action>
		</Slider>
		<Button>
			<Name>Some Button</Name>
			<Action>
				//C code that is called on a button click
				printf("The Button has been clicked\n");
			</Action>
		</Button>
	</Group>

	<Button>
		<Name>Some other Button</Name>
		<Action>
			printf("The Button has been clicked\n");
		</Action>
	</Button>
	
	<Slider>
		<Name>Some Slider</Name>
		<MinBound>0</MinBound>
		<MaxBound>4700</MaxBound>
		<Action>
			if(Some_Slider > 1000){
				printf("Slider value higher than 1000\n");
			}
		</Action>
	</Slider>
</Control>
```
