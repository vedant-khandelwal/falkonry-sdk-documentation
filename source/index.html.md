---
title: Falkonry Integrator API Reference

language_tabs:
  - csharp
  - java
  - python
  - shell

toc_footers:
  - <a href='https://falkonry.com/contact'>Provision a Falkonry Professional account</a>
  - <a href='https://help.falkonry.com'>Falkonry Help Documentation</a>

includes:
  - errors

search: true
---

# Falkonry Integrator ADKs

```shell
Run Falkonry CLI
Type "falkonry" in command prompt/terminal it will land you in the falkonry shell.

user$ falkonry
Welcome to Falkonry Shell !!!
falkonry>>
```

Welcome to the Falkonry Integrator API! You can use our API to create, ingest and extract information from various datastreams, assessments, and facts (ground truths). The API also allows the user to generate output for historical data, set/ get metadata for entities as well as toggle datastreams on/ off.

We have language bindings in Shell, Java, C# and Python! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.


# Authentication

```shell
Login:

For login use host and authorization token.

falkonry>> login --host=https://example.falkonry.ai --token=api-token
logged in to falkonry
falkonry>>
If wrong url or token is passed appropriate error will be displayed.

Check Login details:

For login details type "login_details"

falkonry>> login_details
Host : https://example.falkonry.ai
Token : api-token
falkonry>>

Logout:
For logging out of the current falkonry session

falkonry>> logout
logged out from falkonry
falkonry>>

For exiting from falkonry shell type "quit", "exit" or press "Ctr+C"

falkonry>> quit
user$
```

Falkonry uses API tokens to allow access to the API. Once you are provisioned an account with Falkonry Services, you can create a token, by clicking on Account, followed by Integration. Add some text where it says "Name your authorization token" and clicking on "Add Token". This should create a corresponding token. Copy this token for making any API calls.


<aside class="notice">
Make sure not to publish your API tokens
</aside>

# Datastreams


This creates a datastream.

A Datastream is the basic building block for pattern recognition used in Falkonry. A Datastream is associated with a set of Signals (that it consumes as input), a set of Entities, and a set of Assessments that convey the output of the Datastream.

A Datastream can consume a History Window of signal data and Facts that lie within the window to facilitate the learning of Models.

At any time one model can be designated as “Active”. If the Datastream is turned on with an active model, signal data streamed into the Datastreams will produce a live output stream of assessment results.

<aside class="success">
Remember — a datastream is your basic building block! 
</aside>


## Setup Sliding datastream for narrow style data from a single entity

> To setup a Sliding datastream for narrow style data from a single entity, use this code:

```csharp
  using FalkonryClient;
  using FalkonryClient.Helper.Models;
  
  string token = "api-token";   
  Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
    
	var time = new Time();
	time.Zone = "GMT";
	time.Identifier = "time";
	time.Format = "iso_8601";

	var datasource = new Datasource();
	datasource.Type = "PI";
	var ds = new DatastreamRequest();
	var Field = new Field();
	var Signal = new Signal();
	Signal.ValueIdentifier = "value";
	Signal.SignalIdentifier = "signal";
	Field.Signal = Signal;
	Field.Time = time;
	ds.Field = Field;
	ds.DataSource = datasource;
	var rnd = new System.Random();
	var randomNumber = System.Convert.ToString(rnd.Next(1, 10000));
	ds.Name = "TestDS" + randomNumber;
	ds.Field.Time = time;
	ds.DataSource = datasource;
	var datastream = _falkonry.CreateDatastream(ds);
```

```java
  import com.falkonry.client.Falkonry;
  import com.falkonry.helper.models.Datasource;
  import com.falkonry.helper.models.Datastream;
  import com.falkonry.helper.models.Field;
  import com.falkonry.helper.models.TimeObject;
  import com.falkonry.helper.models.Signal;

  //instantiate Falkonry
  Falkonry falkonry = new Falkonry("http://example.falkonry.ai", "api-token");
  
  Datastream ds = new Datastream();
  ds.setName("Test-DS-" + Math.random());
  TimeObject time = new TimeObject();
  time.setIdentifier("time");
  time.setFormat("iso_8601");
  time.setZone("GMT");
  Signal signal = new Signal();

  signal.setValueIdentifier("value");
  signal.setSignalIdentifier("signal");
  Field field = new Field();
  field.setSignal(signal);
  field.setTime(time);
  ds.setField(field);
  Datasource dataSource = new Datasource();
  dataSource.setType("STANDALONE");
  ds.setDatasource(dataSource);
  Datastream datastream = falkonry.createDatastream(ds);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://example.falkonry.ai', 'auth-token')
datastream = Schemas.Datastream()
datasource = Schemas.Datasource()
field = Schemas.Field()
time = Schemas.Time()
signal = Schemas.Signal()

datastream.set_name('Motor Health' + str(random.random()))  # set name of the Datastream
time.set_zone("GMT")                                        # set timezone of the datastream
time.set_identifier("time")                                 # set time identifier of the datastream
time.set_format("iso_8601")                                 # set time format of the datastream
field.set_time(time)            
signal.set_signalIdentifier("signal")                       # set signal identifier
signal.set_valueIdentifier("value")                         # set value identifier
field.set_signal(signal)                                    # set signal in field
datasource.set_type("STANDALONE")                           # set datastource type in datastream
datastream.set_datasource(datasource)
datastream.set_field(field)
        
#create Datastream
createdDatastream = falkonry.create_datastream(datastream)
```

```shell
To create any Datastream, you need to provide a config file for the datastream in JSON format.

Sample JSONFile:
{
  "name": "Narrow Single Entity Test DS",
  "dataSource": {
    "type": "STANDALONE"
  },
  "field": {
    "time": {
      "zone": "Asia/Calcutta",
      "identifier": "time",
      "format": "YYYY-MM-DD HH:mm:ss"
    },
  "signal": {
    "signalIdentifier": "signal",
    "valueIdentifier": "value"
    }
  }
}

Usage:
falkonry>> datastream_create --path "/Users/user/NarrowSingleEntity.json"
Datastream successfully created : anbsivd1h7h1sd
falkonry>>

Set default Datastream:
You need to select default datastream for using features like adding data to datastream, adding entity meta to datastream and all assessment related features.

falkonry>> datastream_default_set --id "anbsivd1h7h1sd"
Default datastream set: anbsivd1h7h1sd
falkonry>>

Get default Datastream:
For fetching default datastream use the following command

falkonry>> datastream_default_get
Default datastream set : anbsivd1h7h1sd Name : New Ds -1
falkonry>>
```

Wide and narrow (sometimes un-stacked and stacked) are terms used to describe two different presentations for tabular data.
Here, we create a datastream and set it up with narrow style data for a single entity or element.

**Sample format for narrow/historian style**

time                    | signal   | value
------------------------|----------|--------
2016-03-01 01:01:01     | signal1  | 3.4
2016-03-01 01:01:02     | signal2  | 9.3

<aside class="warning">
Structuring data as key-value pairs — as is done in narrow-form datasets — facilitates conceptual clarity. 
</aside>

> Input data could be of the following format:

```json
 {"time" :"2016-03-01 01:01:01", "signal" : "signal1", "value" : 3.4}
 {"time" :"2016-03-01 01:01:02", "signal" : "signal2", "value" : 9.3}
 ```


## Setup Batch datastream for narrow/historian style data from a single entity

> To setup a Batch datastream for narrow/historian style data from a single entity, use this code:

```csharp
  using FalkonryClient;
  using FalkonryClient.Helper.Models;
  
  string token="api-token";   
  Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
    
	var time = new Time();
	time.Zone = "GMT";
	time.Identifier = "time";
	time.Format = "iso_8601";

	var datasource = new Datasource();
	datasource.Type = "PI";
	var ds = new DatastreamRequest();
	var Field = new Field();
	var Signal = new Signal();
	Signal.ValueIdentifier = "value";
    Signal.SignalIdentifier = "signal";
	Field.Signal = Signal;
	Field.Time = time;
    Field.BatchIdentifier = "batch";
	ds.Field = Field;
	ds.DataSource = datasource;
	var rnd = new System.Random();
	var randomNumber = System.Convert.ToString(rnd.Next(1, 10000));
	ds.Name = "TestDS" + randomNumber;
	ds.Field.Time = time;
	ds.DataSource = datasource;
	var datastream = _falkonry.CreateDatastream(ds);
```

```java
  import com.falkonry.client.Falkonry;
  import com.falkonry.helper.models.Datasource;
  import com.falkonry.helper.models.Datastream;
  import com.falkonry.helper.models.Field;
  import com.falkonry.helper.models.TimeObject;
  import com.falkonry.helper.models.Signal;

  //instantiate Falkonry
  Falkonry falkonry = new Falkonry("http://example.falkonry.ai", "auth-token");
  
  Datastream ds = new Datastream();
	ds.setName("Test-DS-" + Math.random());
	TimeObject time = new TimeObject();
	time.setIdentifier("time");
	time.setFormat("iso_8601");
	time.setZone("GMT");
	Signal signal = new Signal();

	signal.setValueIdentifier("value");
	signal.setSignalIdentifier("signal");
	Field field = new Field();
	field.setSignal(signal);
	field.setBatchIdentifier("batch");
	field.setTime(time);
	ds.setField(field);
	Datasource dataSource = new Datasource();
	dataSource.setType("STANDALONE");
	ds.setDatasource(dataSource);
	Datastream datastream = falkonry.createDatastream(ds);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry("http://example.falkonry.ai", "auth-token")
datastream = Schemas.Datastream()
datasource = Schemas.Datasource()
field = Schemas.Field()
time = Schemas.Time()
signal = Schemas.Signal()

datastream.set_name('Motor Health' + str(random.random()))  # set name of the Datastream

time.set_zone("GMT")                                        # set timezone of the datastream
time.set_identifier("time")                                 # set time identifier of the datastream
time.set_format("iso_8601")                                 # set time format of the datastream
signal.set_signalIdentifier("signal");                      # set signal identifier
signal.set_valueIdentifier("value");                        # set value identifier
field.set_time(time)
field.set_signal(signal)                                    # set signal in field
field.set_batchIdentifier("batch")                          # set batchIdentifier in field
datasource.set_type("STANDALONE")                           # set datastource type in datastream
datastream.set_datasource(datasource)
datastream.set_field(field)
        
#create Datastream
createdDatastream = falkonry.create_datastream(datastream)
```

```shell
To create any Datastream, you need to provide a config file for the datastream in JSON format.

Sample JSONFile:
{
  "name": "Narrow Single Entity Test DS",
  "dataSource": {
    "type": "STANDALONE"
  },
  "field": {
    "time": {
      "zone": "Asia/Calcutta",
      "identifier": "time",
      "format": "YYYY-MM-DD HH:mm:ss"
    },
    "signal": {
      "signalIdentifier": "signal",
      "valueIdentifier": "value"
    },
    "batchIdentifier": "batchId" // set batch identifier here.
  }
}

Usage:
falkonry>> datastream_create --path "/Users/user/NarrowSingleEntity.json"
Datastream successfully created : anbsivd1h7h1sd
falkonry>>

Set default Datastream:
You need to select default datastream for using features like adding data to datastream, adding entity meta to datastream and all assessment related features.

falkonry>> datastream_default_set --id "anbsivd1h7h1sd"
Default datastream set: anbsivd1h7h1sd
falkonry>>

Get default Datastream:
For fetching default datastream use the following command

falkonry>> datastream_default_get
Default datastream set : anbsivd1h7h1sd Name : New Ds -1
falkonry>>
```

Wide and narrow (sometimes un-stacked and stacked) are terms used to describe two different presentations for tabular data.
Here, we create a datastream and set it up with narrow style data for a single entity or element.

**Sample format for narrow/historian style**

time                    | signal   | value | batch
------------------------|----------|-------|-------
2016-03-01 01:01:01     | signal1  | 3.4   | batch1
2016-03-01 01:01:02     | signal2  | 9.3   | batch1

<aside class="warning">
Structuring data as key-value pairs — as is done in narrow-form datasets — facilitates conceptual clarity. 
</aside>

> Input data could be of the following format:

```json
 {"batchId" :"batch1", "entity": "machine1", "signal" : "signal1", "value" : 3.4}
 {"batchId" :"batch2", "entity": "machine1", "signal" : "signal2", "value" : 9.3}
 ```


## Setup Sliding datastream for narrow/historian style data from multiple entities

> To setup a Sliding datastream using narrow style data for multiple entities, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="api-token";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
  
var time = new Time();
time.Zone = "GMT";
time.Identifier = "time";
time.Format = "iso_8601";

var datasource = new Datasource();
datasource.Type = "PI";
var ds = new DatastreamRequest();
var Field = new Field();
var Signal = new Signal();
Signal.ValueIdentifier = "value";
Signal.SignalIdentifier = "signal";
Field.Signal = Signal;
Field.Time = time;
Field.EntityIdentifier = "entity";
ds.Field = Field;
ds.DataSource = datasource;
var rnd = new System.Random();
var randomNumber = System.Convert.ToString(rnd.Next(1, 10000));
ds.Name = "TestDS" + randomNumber;
ds.Field.Time = time;
ds.DataSource = datasource;
var datastream = _falkonry.CreateDatastream(ds);
```

```java
import com.falkonry.client.Falkonry;
import com.falkonry.helper.models.Datasource;
import com.falkonry.helper.models.Datastream;
import com.falkonry.helper.models.Field;
import com.falkonry.helper.models.TimeObject;
import com.falkonry.helper.models.Signal;

//instantiate Falkonry
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", "auth-token");

Datastream ds = new Datastream();
ds.setName("Test-DS-" + Math.random());
TimeObject time = new TimeObject();
time.setIdentifier("time");
time.setFormat("iso_8601");
time.setZone("GMT");
Signal signal = new Signal();

signal.setValueIdentifier("value");
signal.setSignalIdentifier("signal");
Field field = new Field();
field.setSignal(signal);
field.setEntityIdentifier("entity");
field.setTime(time);
ds.setField(field);
Datasource dataSource = new Datasource();
dataSource.setType("STANDALONE");
ds.setDatasource(dataSource);

Datastream datastream = falkonry.createDatastream(ds);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://example.falkonry.ai', 'auth-token')
datastream = Schemas.Datastream()
datasource = Schemas.Datasource()
field = Schemas.Field()
time = Schemas.Time()
signal = Schemas.Signal()

datastream.set_name('Motor Health' + str(random.random()))  # set name of the Datastream
time.set_zone("GMT")                                        # set timezone of the datastream
time.set_identifier("time")                                 # set time identifier of the datastream
time.set_format("iso_8601")                                 # set time format of the datastream
field.set_time(time)            
signal.set_signalIdentifier("signal")                       # set signal identifier
signal.set_valueIdentifier("value")                         # set value identifier
field.set_signal(signal)                                    # set signal in field
field.set_entityIdentifier("entity")                        # set entity identifier
datasource.set_type("STANDALONE")                           # set datastource type in datastream
datastream.set_datasource(datasource)
datastream.set_field(field)
        
#create Datastream
createdDatastream = falkonry.create_datastream(datastream)
```

```shell
Sample JSONFile:
{
  "name": "Narrow Multiple Entity Test DS",
  "dataSource": {
    "type": "STANDALONE"
  },
  "field": {
    "time": {
      "zone": "Asia/Calcutta",
      "identifier": "time",
      "format": "YYYY-MM-DD HH:mm:ss"
    },
    "entityIdentifier": "entity",
    "signal": {
      "valueIdentifier": "value",
      "signalIdentifier": "signal"
    }
  }
}

Usage :
falkonry>> datastream_create --path "NarrowMultipleEntity.json"
Datastream successfully created : anbsivd1h7h1sd
falkonry>>
```

Wide and narrow (sometimes un-stacked and stacked) are terms used to describe two different presentations for tabular data.
Here, we create a datastream and set it up with narrow style data for multiple entities or elements.


**Sample format for narrow/historian style**

time                    | entity   | signal  | value
------------------------|----------|---------|------
2016-03-01 01:01:01     | machine1 | signal1 | 3.4   
2016-03-01 01:01:02     | machine2 | signal1 | 9.3

<aside class="warning">
Structuring data as key-value pairs — as is done in narrow-form datasets — facilitates conceptual clarity. 
</aside>


## Setup Sliding datastream for wide style data from a single entity

> To setup a Sliding datastream using wide style data for a single entity, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="api-token";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

var time = new Time();
time.Zone = "GMT";
time.Identifier = "time";
time.Format = "iso_8601";

var datasource = new Datasource();
datasource.Type = "PI";
var ds = new DatastreamRequest();
var Field = new Field();
var Signal = new Signal();
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
  
var time = new Time();
time.Zone = "GMT";
time.Identifier = "time";
time.Format = "iso_8601";

var datasource = new Datasource();
datasource.Type = "PI";
var ds = new DatastreamRequest();
// Input List
var inputList = new List<Input>();
var currents = new Input();
currents.Name = "current";
currents.ValueType = new ValueType();
currents.EventType = new EventType();
currents.ValueType.Type = "Numeric";
currents.EventType.Type = "Samples";
inputList.Add(currents);

var vibration = new Input();
vibration.Name = "vibration";
vibration.ValueType = new ValueType();
vibration.EventType = new EventType();
vibration.ValueType.Type = "Numeric";
vibration.EventType.Type = "Samples";
inputList.Add(vibration);

var state = new Input();
state.Name = "state";
state.ValueType = new ValueType();
state.EventType = new EventType();
state.ValueType.Type = "Categorical";
state.EventType.Type = "Samples";
inputList.Add(state);

ds.InputList = inputList;
var Field = new Field();
var Signal = new Signal();
Field.Signal = Signal;
Field.Time = time;
ds.Field = Field;
ds.DataSource = datasource;
var rnd = new System.Random();
var randomNumber = System.Convert.ToString(rnd.Next(1, 10000));
ds.Name = "TestDS" + randomNumber;
ds.Field.Time = time;
ds.DataSource = datasource;
var datastream = _falkonry.CreateDatastream(ds);
```

```java
import com.falkonry.client.Falkonry;
import com.falkonry.helper.models.Datasource;
import com.falkonry.helper.models.Datastream;
import com.falkonry.helper.models.Field;
import com.falkonry.helper.models.TimeObject;
import com.falkonry.helper.models.Signal;

//instantiate Falkonry
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", "auth-token");

Datastream ds = new Datastream();
ds.setName("Test-DS-" + Math.random());
TimeObject time = new TimeObject();
time.setIdentifier("time");
time.setFormat("millis");
time.setZone("GMT");

Field field = new Field();
field.setTime(time);
ds.setField(field);
Datasource dataSource = new Datasource();
dataSource.setType("PI");
dataSource.sethost("https://test.piserver.com/piwebapi");
dataSource.setElementTemplateName("SampleElementTempalte");
ds.setDatasource(dataSource);

// Input List
List<Input> inputList = new ArrayList<Input>();
Input currents = new Input();
ValueType valueType = new ValueType();
EventType eventType = new EventType();
currents.setName("current");
valueType.setType("Numeric");
eventType.setType("Samples");
currents.setValueType(valueType);
currents.setEventType(eventType);
inputList.add(currents);

Input vibration = new Input();
vibration.setName("vibration");
valueType.setType("Numeric");
eventType.setType("Samples");
vibration.setValueType(valueType);
vibration.setEventType(eventType);
inputList.add(vibration);

Input state = new Input();
state.setName("state");
valueType.setType("Categorical");
eventType.setType("Samples");
state.setValueType(valueType);
state.setEventType(eventType);
inputList.add(state);

ds.setInputList(inputList);

Datastream datastream = falkonry.createDatastream(ds);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://example.falkonry.ai', 'auth-token')
datastream = Schemas.Datastream()
datasource = Schemas.Datasource()
field = Schemas.Field()
time = Schemas.Time()
signal = Schemas.Signal()
input1 = Schemas.Input()
input2 = Schemas.Input()
input3 = Schemas.Input()

datastream.set_name('Motor Health' + str(random.random()))  # set name of the Datastream

input1.set_name("Signal1")                                  # set name of input signal
input1.set_value_type("Numeric")                            # set value type of input signal (Numeric for number, Categorical for string type)
input1.set_event_type("Samples")                            # set event type of input signal
input2.set_name("Signal2")                                  # set name of input signal
input2.set_value_type("Numeric")                            # set value type of input signal (Numeric for number, Categorical for string type)
input2.set_event_type("Samples")                            # set event type of input signal
input3.set_name("Signal3")                                  # set name of input signal
input3.set_value_type("Numeric")                            # set value type of input signal (Numeric for number, Categorical for string type)
input3.set_event_type("Samples")                            # set event type of input signal
inputs = []
inputs.append(input1)
inputs.append(input2)
inputs.append(input3)

time.set_zone("GMT")                                        # set timezone of the datastream
time.set_identifier("time")                                 # set time identifier of the datastream
time.set_format("iso_8601")                                 # set time format of the datastream
field.set_time(time)            
field.set_signal(signal)                                    # set signal in field
datasource.set_type("STANDALONE")                           # set datastource type in datastream
datastream.set_datasource(datasource)
datastream.set_field(field)
datastream.set_inputs(inputs)
        
#create Datastream
createdDatastream = falkonry.create_datastream(datastream)
```

```shell
Sample JSONFile:
{
  "name": "Wide Single Entity Test DS",
  "dataSource": {
    "type": "STANDALONE"
  },
  "field": {
    "time": {
      "zone": "Asia/Calcutta",
      "identifier": "time",
      "format": "millis"
    },
    "entityIdentifier": null
  },
  "inputList": [
    {
      "name": "signal1",
      "valueType": {
        "type": "Numeric"
      },
      "eventType": {
        "type": "Samples"
      }
    },
    {
      "name": "Signal2",
      "valueType": {
        "type": "Numeric"
      },
      "eventType": {
        "type": "Samples"
      }
    },
    {
      "name": "Signal3",
      "valueType": {
        "type": "Numeric"
      },
      "eventType": {
        "type": "Samples"
      }
    },
    {
      "name": "Signal4",
      "valueType": {
        "type": "Numeric"
      },
      "eventType": {
        "type": "Samples"
      }
    }
  ]
}

Usage :
falkonry>> datastream_create --path "WideSingleEntity.json"
Datastream successfully created : anb109d1h7h1po
falkonry>>
```

> Input data could be of the following format:

```json
{"time":1467729675422, "signal1":41.11, "signal2":82.34, "signal3":74.63, "signal4":4.8}
{"time":1467729668919, "signal1":78.11, "signal2":2.33, "signal3":4.6, "signal4":9.8}
```

Wide and narrow (sometimes un-stacked and stacked) are terms used to describe two different presentations for tabular data.
Here, we create a datastream and set it up with wide style data for a single entity or element.

**Sample format for wide style**

time                    | signal1 | signal2 | signal3 | signal4
------------------------|---------|---------|---------|--------
2016-03-01 01:01:01     | 1.2     | 3.4     | 4.5     | 5.6
2016-03-01 01:01:02     | 4.5     | 9.3     | 5.5     | 6.2


<aside class="warning">
If you have many value variables, it is difficult to summarize wide-form datasets at a glance
</aside>


## Setup Sliding datastream for wide style data from multiple entities


> To setup a Sliding datastream using wide style data for multiple entities, use this code:

```csharp
  using FalkonryClient;
  using FalkonryClient.Helper.Models;
  
  string token="api-token";   
  Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
    
	var time = new Time();
	time.Zone = "GMT";
	time.Identifier = "time";
	time.Format = "iso_8601";

	var datasource = new Datasource();
	datasource.Type = "PI";
	var ds = new DatastreamRequest();
	var Field = new Field();
	var Signal = new Signal();
	
	// Input List
  var inputList = new List<Input>();
  var currents = new Input();
  currents.Name = "current";
  currents.ValueType = new ValueType();
  currents.EventType = new EventType();
  currents.ValueType.Type = "Numeric";
  currents.EventType.Type = "Samples";
  inputList.Add(currents);

  var vibration = new Input();
  vibration.Name = "vibration";
  vibration.ValueType = new ValueType();
  vibration.EventType = new EventType();
  vibration.ValueType.Type = "Numeric";
  vibration.EventType.Type = "Samples";
  inputList.Add(vibration);

  var state = new Input();
  state.Name = "state";
  state.ValueType = new ValueType();
  state.EventType = new EventType();
  state.ValueType.Type = "Categorical";
  state.EventType.Type = "Samples";
  inputList.Add(state);

  ds.InputList = inputList;
	var Field = new Field();
	Field.EntityIdentifier = "entity";
	var Signal = new Signal();
	Field.Signal = Signal;
	Field.Time = time;
	ds.Field = Field;
	ds.DataSource = datasource;
	var rnd = new System.Random();
	var randomNumber = System.Convert.ToString(rnd.Next(1, 10000));
	ds.Name = "TestDS" + randomNumber;
	ds.Field.Time = time;
	ds.DataSource = datasource;
	var datastream = _falkonry.CreateDatastream(ds);
```

```java
    import com.falkonry.client.Falkonry;
    import com.falkonry.helper.models.Datasource;
    import com.falkonry.helper.models.Datastream;
    import com.falkonry.helper.models.Field;
    import com.falkonry.helper.models.TimeObject;
    import com.falkonry.helper.models.Signal;

    //instantiate Falkonry
    Falkonry falkonry = new Falkonry("http://example.falkonry.ai", "api-token");

    Datastream ds = new Datastream();
    ds.setName("Test-DS-" + Math.random());

    TimeObject time = new TimeObject();
    time.setIdentifier("time");
    time.setFormat("millis");
    time.setZone("GMT");

    Signal signal = new Signal();
    
    signal.setValueIdentifier("value");
    
    

    Datasource dataSource = new Datasource();
    dataSource.setType("STANDALONE");

    Field field = new Field();
    field.setSignal(signal);
    field.setTime(time);
    field.setEntityIdentifier("entity");

    ds.setDatasource(dataSource);
    ds.setField(field);

    Datastream datastream = falkonry.createDatastream(ds);


    //Add data to datastream
    String data = "time,entity,signal1,signal2,signal3,signal4" + "\n"
        + "1467729675422,entity1,41.11,62.34,77.63,4.8" + "\n"
        + "1467729675445,entity1,43.91,82.64,73.63,3.8";
    Map<String, String> options = new HashMap<String, String>();
    options.put("timeIdentifier", "time");
    options.put("timeFormat", "millis");
    options.put("fileFormat", "csv");
    falkonry.addInput(datastream.getId(), data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://example.falkonry.ai', 'api-token')
datastream = Schemas.Datastream()
datasource = Schemas.Datasource()
field = Schemas.Field()
time = Schemas.Time()
signal = Schemas.Signal()
input1 = Schemas.Input()
input2 = Schemas.Input()
input3 = Schemas.Input()

datastream.set_name('Motor Health' + str(random.random()))  # set name of the Datastream

input1.set_name("Signal1")                                  # set name of input signal
input1.set_value_type("Numeric")                            # set value type of input signal (Numeric for number, Categorical for string type)
input1.set_event_type("Samples")                            # set event type of input signal
input2.set_name("Signal2")                                  # set name of input signal
input2.set_value_type("Numeric")                            # set value type of input signal (Numeric for number, Categorical for string type)
input2.set_event_type("Samples")                            # set event type of input signal
input3.set_name("Signal3")                                  # set name of input signal
input3.set_value_type("Numeric")                            # set value type of input signal (Numeric for number, Categorical for string type)
input3.set_event_type("Samples")                            # set event type of input signal
inputs = []
inputs.append(input1)
inputs.append(input2)
inputs.append(input3)

time.set_zone("GMT")                                        # set timezone of the datastream
time.set_identifier("time")                                 # set time identifier of the datastream
time.set_format("iso_8601")                                 # set time format of the datastream
field.set_time(time)            
field.set_signal(signal)                                    # set signal in field
field.set_entityIdentifier("entity")                        # set entity identifier
datasource.set_type("STANDALONE")                           # set datastource type in datastream
datastream.set_datasource(datasource)
datastream.set_field(field)
datastream.set_inputs(inputs)
        
#create Datastream
createdDatastream = falkonry.create_datastream(datastream)
```

```shell
Sample JSONFile:
{
   "name":"Wide Multiple Entity Test DS",
   "dataSource":{
      "type":"STANDALONE"
   },
   "field":{
      "time":{
         "zone":"Asia/Calcutta",
         "identifier":"time",
         "format":"millis"
      },
      "entityIdentifier":"entity"
   },
   "inputList":[
      {
         "name":"signal1",
         "valueType":{
            "type":"Numeric"
         },
         "eventType":{
            "type":"Samples"
         }
      },
      {
         "name":"Signal2",
         "valueType":{
            "type":"Numeric"
         },
         "eventType":{
            "type":"Samples"
         }
      },
      {
         "name":"Signal3",
         "valueType":{
            "type":"Numeric"
         },
         "eventType":{
            "type":"Samples"
         }
      },
      {
         "name":"Signal4",
         "valueType":{
            "type":"Numeric"
         },
         "eventType":{
            "type":"Samples"
         }
      }
   ]
}

Usage :

falkonry>> datastream_create --path "WideMultipleEntity.json"
Datastream successfully created : anb109d1h7h1po
falkonry>>
```
> Input data could be of the following format:

```json
  {"time":1467729675422, "entity": "machine1", "signal1":1.2, "signal2":3.4, "signal3":4.5, "signal4":5.6}
  {"time":1467729668919, "entity": "machine2", "signal1":4.5, "signal2":9.3, "signal3":5.5, "signal4":6.2}
```

**Sample format for wide style**

time                    | entity   | signal1 | signal2 | signal3 | signal4
------------------------|----------|---------|---------|---------|---------
2016-03-01 01:01:01     | machine1 | 1.2     | 3.4     | 4.5     | 5.6
2016-03-01 01:01:02     | machine2 | 4.5     | 9.3     | 5.5     | 6.2

<aside class="warning">
If you have many value variables, it is difficult to summarize wide-form datasets at a glance
</aside>


## Create Datastream with microseconds precision

> To create datastream with microseconds precision, use the following code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

var time = new Time();
time.Zone = "GMT";
time.Identifier = "time";
time.Format = "iso_8601";

var datasource = new Datasource();
datasource.Type = "PI";
var ds = new DatastreamRequest();
var Field = new Field();
var Signal = new Signal();
Signal.ValueIdentifier = "value";
Signal.SignalIdentifier = "signal";
Field.Signal = Signal;
Field.Time = time;
ds.Field = Field;
ds.DataSource = datasource;
var rnd = new System.Random();
var randomNumber = System.Convert.ToString(rnd.Next(1, 10000));
ds.Name = "TestDS" + randomNumber;
ds.TimePrecision = "micro"; # this is use to store your data in different date time format. If input data precision is in micorseconds then set "micro" else "millis". If not sent then it will be "millis"
ds.Field.Time = time;
ds.DataSource = datasource;
var datastream = _falkonry.CreateDatastream(ds);

```

```java
import com.falkonry.client.Falkonry;
import com.falkonry.helper.models.Datasource;
import com.falkonry.helper.models.Datastream;
import com.falkonry.helper.models.Field;
import com.falkonry.helper.models.TimeObject;
import com.falkonry.helper.models.Signal;

//instantiate Falkonry
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", "auth-token");
Datastream ds = new Datastream();
ds.setName("Test-DS-" + Math.random());
ds.setTimePrecision("micro"); // "timePrecision" is use to store your data in different date time format. You can store your data in milliseconds("millis") or microseconds("micro"). Default will be "millis"

TimeObject time = new TimeObject();
time.setIdentifier("time");
time.setFormat("iso_8601");
time.setZone("GMT");

Signal signal = new Signal();
signal.setValueIdentifier("value");

Datasource dataSource = new Datasource();
dataSource.setType("STANDALONE");

Field field = new Field();
field.setSignal(signal);
field.setTime(time);

ds.setDatasource(dataSource);
ds.setField(field);

Datastream datastream = falkonry.createDatastream(ds);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('http://example.falkonry.ai', 'auth-token')
datastream = Schemas.Datastream()
datasource = Schemas.Datasource()
field = Schemas.Field()
time = Schemas.Time()
signal = Schemas.Signal()

datastream.set_name('Motor Health' + str(random.random()))  # set name of the Datastream
datastream.set_time_precision("micro")                      # set input data precision. default is "millis"
time.set_zone("GMT")                                        # set timezone of the datastream
time.set_identifier("time")                                 # set time identifier of the datastream
time.set_format("YYYY-MM-DD HH:mm:ss")                      # set time format of the datastream
field.set_time(time)            
signal.set_valueIdentifier("value")                         # set value identifier
signal.set_signalIdentifier("signal")                       # set signal identifier
field.set_signal(signal)                                    # set signal in field
datasource.set_type("STANDALONE")                           # set datastource type in datastream
datastream.set_datasource(datasource)
datastream.set_field(field)
        
#create Datastream
createdDatastream = falkonry.create_datastream(datastream)

```

```shell
Sample JSON File:
{
  "name": "Test DS",
  "dataSource": {
  "type": "STANDALONE"
},
  "field": {
    "time": {
      "zone": "Asia/Calcutta",
      "identifier": "time",
      "format": "YYYY-MM-DD HH:mm:ss"
    },
    "signal": {
      "signalIdentifier": "signal",
      "valueIdentifier": "value"
    }
  },
  "timePrecision": "micro"
}

Usage:

falkonry>> datastream_create --path "Micro.json"
Datastream successfully created : wp7lgnw6mr7nnt
falkonry>>
```
> Data could be of the following format:

```json
{"time" :"2016-03-01 01:01:01", "signal" : "signal1", "value" : 3.4}
{"time" :"2016-03-01 01:01:02", "signal" : "signal2", "value" : 9.3}
```

```csv
or
time,signal,value
2016-03-01 01:01:01,signal1,3.4
2016-03-01 01:01:02,signal2,9.3
```

This is used to store your data in different date time format. If input data precision is in microseconds then set "micro" else "millis". The default precision value is "millis"

## Retrieving all datastreams

> To retrieve a list of existing datastreams, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="api-token";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
List<Datastream> datastreams = _falkonry.GetDatastreams();
```

```java
import com.falkonry.client.Falkonry;

Falkonry falkonry = new Falkonry("https://example.falkonry.ai", "api-token");
datastream = falkonry.getDatastreams();
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://example.falkonry.ai', 'api-token')
        
#return list of Datastreams
datastreams = falkonry.get_datastreams()
```

```shell
falkonry>> datastream_get_list
Listing Datastreams...
=====================================================
Datastream Name Id Created By Live Status
=====================================================
Wide Multiple Entity Test DS oii0djojxc2lxt Tza1q4g0kw5epo OFF
Narrow Single Entity 5v8drr2eqtp7cy Y36zulie5as5bu OFF
Sample Datastream z0cenywoi3jlxj Tza1q4g0kw5epo OFF
=====================================================
falkonry>>
```


## Retrieve datastream by id

> To retrieve datastream by id, use this code:


```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="api-token";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
Datastream datastream = _falkonry.GetDatastream('datastreamId')

```

```java
import com.falkonry.client.Falkonry;

Falkonry falkonry = new Falkonry("https://example.falkonry.ai", "api-token");
datastream = falkonry.getDatastream("datastream_id"); //datastream's id
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://example.falkonry.ai', 'api-token')
datastreamId = 'id of the datastream'
        
#return sigle datastream
datastreams = falkonry.get_datastream(datastreamId)
```

```shell
falkonry>> datastream_get_by_id --id "tkhv69dwjmdydp"
Fetching Datastreams
=====================================================
Id : tkhv69dwjmdydp
Name : Wide Multiple Entity Test DS
Created By : e6q8ienqs9celz
Create Time : 2018-01-11 13:38:14.051000
Update Time : 2018-01-11 13:38:14.051000
Events # : 0
Events Start Time : N/A
Events End Time : N/A
Time Format : millis
Time Zone : Asia/Calcutta
Live Monitoring: OFF
Signals: signal1, Signal2, Signal3, Signal4
=====================================================
falkonry>>
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": "string",
    "name": "string",
    "tenant": "string",
    "sourceId": "string",
    "createdBy": "string",
    "updatedBy": "string",
    "createTime": 0,
    "updateTime": 0,
    "inputList": [
      {
        "key": "string",
        "name": "string",
        "valueType": {},
        "eventType": {},
        "inputType": {}
      }
    ],
    "dataSource": {
      "type": "STANDALONE"
    },
    "field": {
      "entityIdentifier": "string",
      "entityName": "string",
      "signal": {
        "signalIdentifier": "string",
        "valueIdentifier": "string"
      },
      "time": {
        "format": "string",
        "identifier": "string",
        "zone": "string"
      }
    }
  }
]
```

Use `retrieve all datastreams` to get a list of datastreams with their ids.

Use any of these ids to retrieve the datastream of your choice

<aside class="success">
Falkonry needs historical time series data to build pattern recognition models.
</aside>

## Add historical input data to batch datastream

### Add historical narrow input data (csv/json format) to single/multiple entity batch datastream

> To add historical narrow input data (json format) to single entity batch datastream, use the following code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

var data = "{\"time\" :\"2016-03-01 01:01:01\", \"signal\":\"current\",\"value\" : 12.4,\"batch\" : \"batch1\"}";
var options = new SortedDictionary<string, string>();
options.Add("streaming", "false");
options.Add("hasMoreData", "false");
options.Add("timeIdentifier", "time");
options.Add("timeZone", "GMT");
options.Add("timeFormat", "YYYY-MM-DD HH:mm:ss");
options.Add("signalIdentifier", "signal");
options.Add("valueIdentifier", "value");
options.Add("batchIdentifier", "batch");
var inputstatus = _falkonry.AddInput(datastream.Id, data, options);
```

```java
import com.falkonry.client.Falkonry;
import com.falkonry.helper.models.Datasource;
import com.falkonry.helper.models.Datastream;
import com.falkonry.helper.models.Field;
import com.falkonry.helper.models.TimeObject;
import com.falkonry.helper.models.Signal;

//instantiate Falkonry
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", "auth-token");
//Add data to datastream
String data = "{\"time\" :\"2016-03-01T01:01:01.000Z\", \"batch\" : \"batch-1\", \"signal\" : \"current\", \"value\" : 12.5}";

Map<String, String> options = new HashMap<String, String>();
options.put("timeIdentifier", "time");
options.put("timeFormat", "iso_8601");
options.put("timeZone", "GMT");
options.put("signalIdentifier", "signal");
options.put("valueIdentifier", "value");
options.put("batchIdentifier", "batch");
options.put("fileFormat", "json");
options.put("streaming", "false");
options.put("hasMoreData", "false");
falkonry.addInput('datastream-Id', data, options);
```

```python

from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('http://example.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
String data = '{"time": 1467729675010,"batchId": "batch_1","signal": "signal1","value": 9.95}\n'
    +'{"time": 1467729675020,"batchId": "batch_1","signal": "signal1","value": 4.45}\n'
    +'{"time": 1467729675030,"batchId": "batch_2","signal": "signal1","value": 1.45}\n'
    +'{"time": 1467729675040,"batchId": "batch_2","signal": "signal1","value": 8.45}\n'
    +'{"time": 1467729675050,"batchId": "batch_2","signal": "signal1","value": 2.45}'
        
options = {
    'streaming': False,
    'hasMoreData': False,
    'timeFormat': 'millis',
    'timeZone': 'GMT',
    'timeIdentifier': 'time',
    'signalIdentifier': 'signal',
    'valueIdentifier': 'value',
    'batchIdentifier': 'batchId'
}
inputResponse = falkonry.add_input_data(datastreamId, 'json', options, data)
```

```shell
Usage:
falkonry>> datastream_add_historical_data --path "/Users/user/InputNarrowBatchSingleEntity.json" --timeIdentifier "time" --timeFormat "millis" --timeZone "GMT" --signalIdentifier "signal" --batchIdentifier "batchId" --valueIdentifier "value"
Default datastream set : wlybjb4tq776n9 Name : Narrow Single Entity Batch Test DS
{'status': 'PENDING', 'datastream': 'wlybjb4tq776n9', '__$createTime': 1516013313895, '__$id': 'kpgly6d2tg9v27b6', 'user': 'e6q8ienqs9celz', 'action': 'ADD_DATA_DATASTREAM', '__$tenant': 'iqn80x6e2ku9id', 'dataSource': 'y8ibr9co7hqkkd'}
falkonry>>
```

> Input data could be of the following format:

```json
{"time": 1467729675010,"batchId": "batch_1","signal": "signal1","value": 9.95}
{"time": 1467729675030,"batchId": "batch_2","signal": "signal1","value": 1.45}
```

json data from a historian or time series data store can be imported into a Falkonry datastream

> To add historical narrow input data (csv format) to multi entity batch Datastream, use the following code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

var data = "time,signal,value,Unit,Batch\n" + "2016-05-05 12:00:00,current,12.4,uni1,batch1\n2016-03-01 01:01:01,vibration,20.4,unit2,batch2";
var options = new SortedDictionary<string, string>();
            
options.Add("streaming", "false");
options.Add("hasMoreData", "false");
options.Add("timeIdentifier", "time");
options.Add("timeZone", "GMT");
options.Add("timeFormat", "YYYY-MM-DD HH:mm:ss");
options.Add("signalIdentifier", "signal");
options.Add("valueIdentifier", "value");
options.Add("entityIdentifier", "Unit");
options.Add("batchIdentifier", "Batch");
            
var inputstatus = _falkonry.AddInput(datastream.Id, data, options);
               
```

```java
import com.falkonry.client.Falkonry;
import com.falkonry.helper.models.Datasource;
import com.falkonry.helper.models.Datastream;
import com.falkonry.helper.models.Field;
import com.falkonry.helper.models.TimeObject;
import com.falkonry.helper.models.Signal;

//instantiate Falkonry
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", "auth-token");
//Add data to datastream
String data = "time,signal,value,unit,batch\n" + 
			"2012-01-03T18:16:00.000Z,L1DynVert,9.95,unit1,batch1\n" + 
			"2012-01-03T18:16:00.000Z,L1VertAvg,12.95,unit2,batch2";

Map<String, String> options = new HashMap<String, String>();
options.put("fileFormat", "csv");
options.put("streaming", "false");
options.put("hasMoreData", "false");
falkonry.addInput('datastream-Id', data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('http://example.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
String data = 'time,batchId,unit,signal,value\n'
    +'1467729675010,batch_1,unit1,signal1,9.95'
        
options = {
    'streaming': False,
    'hasMoreData': False
}
inputResponse = falkonry.add_input_data(datastreamId, 'csv', options, data)
```

```shell
Sample data:

time,batchId,unit,signal,value
1467729675010,batch_1,unit1,signal1,9.95
1467729675020,batch_1,unit1,signal1,4.45

Usage:
falkonry>> datastream_add_historical_data --path "/Users/user/InputNarrowBatchMultiEntity.csv" --timeIdentifier "time" --timeFormat "millis" --timeZone "GMT" --entityIdentifier "unit" --signalIdentifier "signal" --batchIdentifier "batchId" --valueIdentifier "value"
Default datastream set : hn6cq2lpcwg49c Name : Narrow Multiple Entity Batch Test DS
{'status': 'PENDING', 'datastream': 'hn6cq2lpcwg49c', '__$createTime': 1516013971506, '__$id': '4hqpj9hw2vmcjqwh', 'user': 'e6q8ienqs9celz', 'action': 'ADD_DATA_DATASTREAM', '__$tenant': 'iqn80x6e2ku9id', 'dataSource': 'Rp8euhiyg3ctt4'}
falkonry>> 
```
> The data could be of the following format

```csv

```

### Add historical wide input data (csv/ json format) to single/multiple entity batch datastream

> To add historical wide input data (csv format) to single entity batch datastream, use the following code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

var data = "time,Batch,current,vibration,state\n 2016-05-05T12:00:00.000Z,batch1,12.4,3.4,On";
var options = new SortedDictionary<string, string>();
options.Add("timeIdentifier", "time");
options.Add("timeFormat", "iso_8601");
options.Add("timeZone", "GMT");
options.Add("streaming", "false");
options.Add("hasMoreData", "false");
options.Add("batchIdentifier", "Batch");
var inputstatus = _falkonry.AddInput(datastream.Id, data, options);
               
```

```java
import com.falkonry.client.Falkonry;
import com.falkonry.helper.models.Datasource;
import com.falkonry.helper.models.Datastream;
import com.falkonry.helper.models.Field;
import com.falkonry.helper.models.TimeObject;
import com.falkonry.helper.models.Signal;

//instantiate Falkonry
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", "auth-token");
//Add data to datastream
  
String data = "time,L1DynVert,L1VertAvg,L1VertPk,batch\n" + 
  "2012-01-03T18:16:00.000Z,4.6,9.95,89.95,batch1\n" + 
  "2012-01-03T18:16:00.000Z,5.2,12.95,5.85,batch2";

Map<String, String> options = new HashMap<String, String>();
options.put("timeIdentifier", "time");
options.put("timeFormat", "iso_8601");
options.put("timeZone", "GMT");
options.put("fileFormat", "csv");
options.put("streaming", "false");
options.put("hasMoreData", "false");
options.put("entityIdentifier", "unit");
options.put("batchIdentifier", "batch");

falkonry.addInput('datastream-Id', data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('http://example.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
String data = '{"time": 1467729675010,"batchId": "batch_1","signal1": 9.95,"signal2": 19.95,"signal3": 39.95}\n'
    +'{"time": 1467729675020,"batchId": "batch_1","signal1": 4.45,"signal2": 14.45,"signal3": 34.45}\n'
    +'{"time": 1467729675030,"batchId": "batch_2","signal1": 1.45,"signal2": 10.45,"signal3": 30.45}'
        
options = {
    'streaming': False,
    'hasMoreData': False,
    'timeFormat': 'millis',
    'timeZone': 'GMT',
    'timeIdentifier': 'time',
    'batchIdentifier': 'batchId'
}
inputResponse = falkonry.add_input_data(datastreamId, 'json', options, data)
```

```shell
falkonry>> datastream_add_historical_data --path "/Users/user/InputWideBatchSingleEntity.json" --timeIdentifier "time" --timeFormat "millis" --timeZone "GMT" --batchIdentifier "batchId"
Default datastream set : cm492hm4j74wrn Name : Wide Single Entity Batch Test DS
{'status': 'PENDING', 'datastream': 'cm492hm4j74wrn', '__$createTime': 1516015544894, '__$id': 'wmgllwgjwwjtp7w4', 'user': 'e6q8ienqs9celz', 'action': 'ADD_DATA_DATASTREAM', '__$tenant': 'iqn80x6e2ku9id', 'dataSource': 'Teqm2nwjpbhbs3'}
falkonry>> 
```
> The data could be of the following format

```json
{"time": 1467729675010,"batchId": "batch_1","signal1": 9.95,"signal2": 19.95,"signal3": 39.95}
{"time": 1467729675030,"batchId": "batch_2","signal1": 1.45,"signal2": 10.45,"signal3": 30.45}
```

> To add historical wide input data (json format) to multi entity batch datastream, use the following code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

var data = "{\"time\" :\"2016-03-01 01:01:01\", \"current\" : 12.4, \"vibration\" : 3.4, \"state\" : \"On\", \"car\" : \"car1\", \"batch\" : \"batch1\"}";
var options = new SortedDictionary<string, string>();
options.Add("streaming", "false");
options.Add("hasMoreData", "false");
options.Add("timeIdentifier", "time");
options.Add("timeZone", "GMT");
options.Add("timeFormat", "YYYY-MM-DD HH:mm:ss");
options.Add("entityIdentifier", "car");
options.Add("batchIdentifier", "batch");
var inputstatus = _falkonry.AddInput(datastream.Id, data, options);
```

```java
import com.falkonry.client.Falkonry;
import com.falkonry.helper.models.Datasource;
import com.falkonry.helper.models.Datastream;
import com.falkonry.helper.models.Field;
import com.falkonry.helper.models.TimeObject;
import com.falkonry.helper.models.Signal;

//instantiate Falkonry
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", "auth-token");

Datastream ds = new Datastream();
ds.setName("Test-DS-" + Math.random());

TimeObject time = new TimeObject();
time.setIdentifier("time");
time.setFormat("millis");
time.setZone("GMT");

Signal signal = new Signal();

signal.setValueIdentifier("value");

Datasource dataSource = new Datasource();
dataSource.setType("STANDALONE");

Field field = new Field();
field.setSignal(signal);
field.setTime(time);
field.setEntityIdentifier("unit");
field.setBatchIdentifier("batch");

ds.setDatasource(dataSource);
ds.setField(field);

Datastream datastream = falkonry.createDatastream(ds);


//Add data to datastream
String data = "{\"time\" :\"2016-03-01 01:01:01\", \"current\" : 12.4, \"vibration\" : 3.4, \"state\" : \"On\", \"batch\" : \"batch1\", \"unit\" : \"unit1\"}";

Map<String, String> options = new HashMap<String, String>();
options.put("fileFormat", "json");
options.put("streaming", "false");
options.put("hasMoreData", "false");
falkonry.addInput(datastream.getId(), data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('http://example.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
String data = 'time,batchId,unit,signal1,signal2,signal3\n'+
    '1467729675010,batch_1,unit1,9.95,19.95,39.95\n'+
    '1467729675020,batch_1,unit1,4.45,14.45,34.45'
        
options = {
    'streaming': False,
    'hasMoreData': False
}
inputResponse = falkonry.add_input_data(datastreamId, 'csv', options, data)
```

```shell
falkonry>> datastream_add_historical_data --path "/Users/user/InputWideBatchMultiEntity.csv" --timeIdentifier "time" --timeFormat "millis" --timeZone "GMT" --entityIdentifier "unit" --batchIdentifier "batchId"
Default datastream set : 7wgwm68b9p24n4 Name : Wide Multiple Entity Batch Test DS
{'status': 'PENDING', 'datastream': '7wgwm68b9p24n4', '__$createTime': 1516016064677, '__$id': 'mlc2pt7y87jhwlw2', 'user': 'e6q8ienqs9celz', 'action': 'ADD_DATA_DATASTREAM', '__$tenant': 'iqn80x6e2ku9id', 'dataSource': 'Lita7m408qq9j9'}
falkonry>>
```
> The data could be of the following format

```csv
time,batchId,unit,signal1,signal2,signal3
1467729675010,batch_1,unit1,9.95,19.95,39.95
1467729675020,batch_1,unit1,4.45,14.45,34.45
```

## Add historical input data to sliding datastream

### Add historical narrow input data (csv/json format) to single/multiple sliding datastream

> To add historical narrow input data (csv format) to single entity sliding datastream

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

var data = "time,signal,value\n" + "2016-05-05 12:00:00,current,12.4\n2016-03-01 01:01:01,vibration,20.4";
var options = new SortedDictionary<string, string>();
            
options.Add("streaming", "false");
options.Add("hasMoreData", "false");
            
var inputstatus = _falkonry.AddInput(datastream.Id, data, options);
```

```java
import com.falkonry.client.Falkonry;
import com.falkonry.helper.models.Datasource;
import com.falkonry.helper.models.Datastream;
import com.falkonry.helper.models.Field;
import com.falkonry.helper.models.TimeObject;
import com.falkonry.helper.models.Signal;

//instantiate Falkonry
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", "auth-token");
//Add data to datastream
String data = "time,signal,value\n" + 
    "2012-01-03T18:16:00.000Z,L1DynVert,9.95\n" + 
    "2012-01-03T18:16:00.000Z,L1VertAvg,12.95";

Map<String, String> options = new HashMap<String, String>();
options.put("fileFormat", "csv");
options.put("streaming", "false");
options.put("hasMoreData", "false");
falkonry.addInput('datastream-Id', data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('http://example.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
String data = "time,signal,value " + "\n"
        + "2016-03-01 01:01:01,current,3.4" + "\n"
        + "2016-03-01 01:01:01,vibration,1.4";
        
# set hasMoreData to True if data is sent in batches. When the last batch is getting sent then set  'hasMoreData' to False. For single batch upload it shpuld always be set to False
options = {'streaming': False,
           'hasMoreData': False,
           'timeFormat': "YYYY-MM-DD HH:mm:ss",
           'timeZone': "GMT",
           'timeIdentifier': "time"}
inputResponse = falkonry.add_input_data(datastreamId, 'csv', options, data)
```

```shell
falkonry>> datastream_add_historical_data --path "/Users/user/InputNarrow.csv" --timeIdentifier "time" --timeFormat "iso_8601" --timeZone "GMT" --signalIdentifier "signal" --valueIdentifier "value"
Default datastream set : oii0djojxc2lxt Name : New Ds -1
{'status': 'PENDING', 'datastream': 'oii0djojxc2lxt', '__$createTime': 1500538975912, '__$id': 'q68cho8foyml3gv4', 'user': 'Tza1q4g0kw5epo', 'action': 'ADD_DATA_DATASTREAM', '__$tenant': 'el7rvvqx2xr6v5', 'dataSource': 'Lp1nfea7z5lrtk'}
falkonry>>
```
> Data could be of following format:
```json
{"time" :"2016-03-01 01:01:01", "signal" : "current", "value" : 12.4}
{"time" :"2016-03-01 01:01:01", "signal" : "vibration", "value" : 3.4}
{"time" :"2016-03-01 01:01:01", "signal" : "state", "value" : on}
{"time" :"2016-03-01 01:01:01", "signal" : "current", "value" : 31.4}
{"time" :"2016-03-01 01:01:01", "signal" : "vibration", "value" : 2.4}
{"time" :"2016-03-01 01:01:01", "signal" : "state", "value" : off}
```

> To add historical narrow input data (json format) to multi entity sliding datastream

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

var data = "{\"time\" :\"2016-03-01 01:01:01\", \"signal\":\"current\",\"value\" : 12.4,\"car\" : \"car1\"}";
var options = new SortedDictionary<string, string>();
options.Add("streaming", "false");
options.Add("hasMoreData", "false");
options.Add("timeIdentifier", "time");
options.Add("timeZone", "GMT");
options.Add("timeFormat", "YYYY-MM-DD HH:mm:ss");
options.Add("entityIdentifier", "car");
options.Add("signalIdentifier", "signal");
options.Add("valueIdentifier", "value");
var inputstatus = _falkonry.AddInput(datastream.Id, data, options);
```

```java
import com.falkonry.client.Falkonry;
import com.falkonry.helper.models.Datasource;
import com.falkonry.helper.models.Datastream;
import com.falkonry.helper.models.Field;
import com.falkonry.helper.models.TimeObject;
import com.falkonry.helper.models.Signal;

//instantiate Falkonry
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", "auth-token");
//Add data to datastream
String data = "{\"time\" :\"2016-03-01T01:01:01.000Z\",\"unit\":\"Unit1\", \"signal\" : \"current\", \"value\" : 12.5}\n" +
			"{\"time\" :\"2016-03-01T01:01:01.000Z\",\"unit\":\"Unit2\", \"signal\" : \"vibration\", \"value\" : 3.4}";

Map<String, String> options = new HashMap<String, String>();
options.put("timeIdentifier", "time");
options.put("timeFormat", "iso_8601");
options.put("timeZone", "GMT");
options.put("signalIdentifier", "signal");
options.put("entityIdentifier", "unit");
options.put("valueIdentifier", "value");
options.put("fileFormat", "json");
options.put("streaming", "false");
options.put("hasMoreData", "false");
falkonry.addInput('datastream-Id', data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('http://example.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
String data = "{\"time\" :\"2016-03-01 01:01:01\", \"signal\" : \"current\", \"value\" : 12.4, \"car\" : \"car1\"} + "\n"
        + "{\"time\" :\"2016-03-01 01:01:01\", \"signal\" : \"vibration\", \"value\" : 2.4, \"car\" : \"car1\"} + "\n"
        + "{\"time\" :\"2016-03-01 01:01:01\", \"signal\" : \"state\", \"value\" : on, \"car\" : \"car1\"}";

        
# set hasMoreData to True if data is sent in batches. When the last batch is getting sent then set  'hasMoreData' to False. For single batch upload it shpuld always be set to False
options = {'streaming': False,
   'hasMoreData': False,
   'timeFormat': "YYYY-MM-DD HH:mm:ss",
   'timeZone': "GMT",
   'timeIdentifier': "time",
   'signalIdentifier': 'signal',
   'valueIdentifier': 'value',
   'entityIdentifier': 'car'}
inputResponse = falkonry.add_input_data(datastreamId, 'json', options, data)
```

```shell
falkonry>> datastream_add_historical_data --path "/Users/user/Input.json" --timeIdentifier "time" --entityIdentifier "car" --timeFormat "iso_8601" --timeZone "GMT" --signalIdentifier "signal" --valueIdentifier "value"

Default datastream set : oii0djojxc2lxt Name : New Ds -1
{'status': 'PENDING', 'datastream': 'oii0djojxc2lxt', '__$createTime': 1500538975912, '__$id': 'q68cho8foyml3gv4', 'user': 'Tza1q4g0kw5epo', 'action': 'ADD_DATA_DATASTREAM', '__$tenant': 'el7rvvqx2xr6v5', 'dataSource': 'Lp1nfea7z5lrtk'}
falkonry>>
```

> Data could be of following format:

```json
{"time" :"2016-03-01 01:01:01", "signal" : "current", "value" : 12.4, "car" : "car1"}
{"time" :"2016-03-01 01:01:01", "signal" : "vibration", "value" : 3.4, "car" : "car1"}
```

### Add historical wide input data (csv/json format) to single/multiple entity sliding datastream

> To add wide data (json format) to single entity sliding datastream

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

var data = "{\"time\" :\"2016-03-01 01:01:01\", \"current\" : 12.4, \"vibration\" : 3.4, \"state\" : \"On\"}";
var options = new SortedDictionary<string, string>();
options.Add("streaming", "false");
options.Add("hasMoreData", "false");
var inputstatus = _falkonry.AddInput(datastream.Id, data, options);
```

```java
import com.falkonry.client.Falkonry;
import com.falkonry.helper.models.Datasource;
import com.falkonry.helper.models.Datastream;
import com.falkonry.helper.models.Field;
import com.falkonry.helper.models.TimeObject;
import com.falkonry.helper.models.Signal;

//instantiate Falkonry
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", "auth-token");

Datastream ds = new Datastream();
ds.setName("Test-DS-" + Math.random());

TimeObject time = new TimeObject();
time.setIdentifier("time");
time.setFormat("millis");
time.setZone("GMT");

Signal signal = new Signal();
signal.setValueIdentifier("value");

Datasource dataSource = new Datasource();
dataSource.setType("STANDALONE");

Field field = new Field();
field.setSignal(signal);
field.setTime(time);
field.setEntityIdentifier("entities");

ds.setDatasource(dataSource);
ds.setField(field);

Datastream datastream = falkonry.createDatastream(ds);

//Add data to datastream
String data = "{\"time\" :\"2016-03-01 01:01:01\", \"current\" : 12.4, \"vibration\" : 3.4, \"state\" : \"On\"}";

Map<String, String> options = new HashMap<String, String>();
options.put("fileFormat", "json");
options.put("streaming", "false");
options.put("hasMoreData", "false");
falkonry.addInput(datastream.getId(), data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('http://example.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
stream   = io.open('./data.json')
        
# set hasMoreData to True if data is sent in batches. When the last batch is getting sent then set  'hasMoreData' to False. For single batch upload it shpuld always be set to False

options = {'streaming': False,
   'hasMoreData': False,
   'timeFormat': "YYYY-MM-DD HH:mm:ss",
   'timeZone': "GMT",
   'timeIdentifier': "time"
}
inputResponse = falkonry.add_input_data(datastreamId, 'json', options, stream)
```

```shell
falkonry>> datastream_add_historical_data --path "/Users/user/InputWideSingleEntity.csv" --timeIdentifier "time" --timeFormat "iso_8601" --timeZone "GMT" --signalIdentifier "signal" --valueIdentifier "value"

Default datastream set : oii0djojxc2lxt Name : New Ds -1
{'status': 'PENDING', 'datastream': 'oii0djojxc2lxt', '__$createTime': 1500538975912, '__$id': 'q68cho8foyml3gv4', 'user': 'Tza1q4g0kw5epo', 'action': 'ADD_DATA_DATASTREAM', '__$tenant': 'el7rvvqx2xr6v5', 'dataSource': 'Lp1nfea7z5lrtk'}
falkonry>>
```

> Data could be of following format

```json
{"time" :"2016-03-01 01:01:01", "current" : 12.4, "vibration" : 3.4, "state" : "On"}
{"time" :"2016-03-01 01:01:02", "current" : 11.3, "vibration" : 2.2, "state" : "On"}
{"time" :"2016-03-01 01:01:03", "current" : 10.5, "vibration" : 3.8, "state" : "On"}
{"time" :"2016-03-01 01:02:03", "current" : 19.2, "vibration" : 3.8, "state" : "On"}
{"time" :"2016-03-01 01:02:03", "current" : 1.2, "vibration" : 0.8, "state" : "Off"}
{"time" :"2016-03-01 01:02:05", "current" : 0.2, "vibration" : 0.3, "state" : "Off"}
```

> To add wide input data (csv format) to multi entity sliding datastream

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

var data = "time,Unit,current,vibration,state\n 2016-05-05T12:00:00.000Z,Unit1,12.4,3.4,On";
var options = new SortedDictionary<string, string>();
options.Add("timeIdentifier", "time");
options.Add("timeFormat", "iso_8601");
options.Add("timeZone", "GMT");
options.Add("streaming", "false");
options.Add("hasMoreData", "false");
options.Add("entityIdentifier", "Unit");
var inputstatus = _falkonry.AddInput(datastream.Id, data, options);
            
```

```java
import com.falkonry.client.Falkonry;
import com.falkonry.helper.models.Datasource;
import com.falkonry.helper.models.Datastream;
import com.falkonry.helper.models.Field;
import com.falkonry.helper.models.TimeObject;
import com.falkonry.helper.models.Signal;

//instantiate Falkonry
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", "auth-token");
//Add data to datastream

String data = "time,unit,L1DynVert,L1VertAvg,L1VertPk\n" + 
  "2012-01-03T18:16:00.000Z,unit1,4.6,9.95,89.95\n" + 
  "2012-01-03T18:16:00.000Z,unit1,5.2,12.95,5.85";

Map<String, String> options = new HashMap<String, String>();
options.put("timeIdentifier", "time");
options.put("timeFormat", "iso_8601");
options.put("timeZone", "GMT");
options.put("fileFormat", "csv");
options.put("streaming", "false");
options.put("hasMoreData", "false");
options.put("entityIdentifier", "unit");

falkonry.addInput('datastream-Id', data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('http://example.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
stream   = io.open('./dataMultientity.csv')

# set hasMoreData to True if data is sent in batches. When the last batch is getting sent then set  'hasMoreData' to False. For single batch upload it shpuld always be set to False

options = {'streaming': False,
            'hasMoreData': False,
            'timeFormat': "YYYY-MM-DD HH:mm:ss",
            'timeZone': "GMT",
            'timeIdentifier': "time",
            'entityIdentifier': "device"}
inputResponse = falkonry.add_input_data(datastreamId, 'csv', options, stream)
```

```shell
falkonry>> datastream_add_historical_data --path "/Users/user/InputNarrow.csv" --timeIdentifier "time" --timeFormat "iso_8601" --timeZone "GMT" --signalIdentifier "signal" --valueIdentifier "value" --entityIdentifier "device"

Default datastream set : oii0djojxc2lxt Name : New Ds -1
{'status': 'PENDING', 'datastream': 'oii0djojxc2lxt', '__$createTime': 1500538975912, '__$id': 'q68cho8foyml3gv4', 'user': 'Tza1q4g0kw5epo', 'action': 'ADD_DATA_DATASTREAM', '__$tenant': 'el7rvvqx2xr6v5', 'dataSource': 'Lp1nfea7z5lrtk'}
falkonry>>
```

> Input data could be of the following format:

```csv
  time, entity, signal, value
  2016-03-01 01:01:01, thing1, signal1, 3.4
  2016-03-01 01:01:01, thing1, signal2, 1.4
```

csv data from a historian or time series data store can be imported into a Falkonry datastream

<aside class="success">
Falkonry needs historical time series data to build pattern recognition models.
</aside>


## Add historical input data (json) from a stream to datastream 

> To add historical input data in json format from a stream, use this code: 

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

/*This particular example will read data from a AddData.json file in debug folder in bin*/

SortedDictionary<string, string> options = new SortedDictionary<string, string>();
  options.Add("timeIdentifier", "time");
  options.Add("timeFormat", "iso_8601");
  options.Add("streaming", "false");
  options.Add("hasMoreData", "false");
  options.Add("fileFormat", "json");

string folder_path = Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location);

string path = folder_path + "/AddData.json";
//Alternatively, you can directly specify the folder path in the "folder_path" variable

byte[] bytes = System.IO.File.ReadAllBytes(path);

InputStatus inputstatus = falkonry.addInputStream('datastream-id', bytes, options);
```


```java
import com.falkonry.client.Falkonry
import org.apache.commons.io.FileUtils;

  Falkonry falkonry   = new Falkonry("https://example.falkonry.ai", "auth-token");
  Map<String, String> options = new HashMap<String, String>();
    options.put("timeIdentifier", "time");
    options.put("timeFormat", "millis");
    options.put("fileFormat", "csv");
    options.put("streaming", "false");
    options.put("hasMoreData", "false");

    File file = new File("tmp/data.json");      
      ByteArrayInputStream istream = new ByteArrayInputStream(FileUtils.readFileToByteArray(file));

      InputStatus inputStatus = falkonry.addInputStream('datastream-Id',byteArrayInputStream,options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://example.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
stream   = io.open('./data.json')
        
# set hasMoreData to True if data is sent in batches. When the last batch is getting sent then set  'hasMoreData' to False. For single batch upload it shpuld always be set to False
options = {'streaming': False, 'hasMoreData':False}   
inputResponse = falkonry.add_input_data(datastreamId, 'json', options, stream)
```

```shell

```

> Input data could be of the following format:

```json
  {"time" :"2016-03-01 01:01:01", "entity": "thing1", "signal" : "signal1", "value" : 3.4}
  {"time" :"2016-03-01 01:01:01", "entity": "thing1", "signal" : "signal2", "value" : 1.4}
  {"time" :"2016-03-01 01:01:02", "entity": "thing2", "signal" : "signal1", "value" : 9.3}
  {"time" :"2016-03-01 01:01:02", "entity": "thing2", "signal" : "signal2", "value" : 4.3}
```

json data from a historian or time series data store can be imported into a Falkonry datastream

<aside class="success">
Falkonry needs historical time series data to build pattern recognition models.
</aside>


## Add historical input data (csv) from a stream to datastream 

> To add historical input data in csv format from a stream to a datastream, use this code: 

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

/*This particular example will read data from a AddData.json file in debug folder in bin*/

SortedDictionary<string, string> options = new SortedDictionary<string, string>();
  options.Add("timeIdentifier", "time");
  options.Add("timeFormat", "iso_8601");
  options.Add("streaming", "false");
  options.Add("hasMoreData", "false");
  options.Add("fileFormat", "csv");

string folder_path = Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location);

string path = folder_path + "/AddData.csv";
//Alternatively, you can directly specify the folder path in the "folder_path" variable

byte[] bytes = System.IO.File.ReadAllBytes(path);

InputStatus inputstatus = falkonry.addInputStream('datastream-id', bytes, options);
```


```java
import com.falkonry.client.Falkonry
import org.apache.commons.io.FileUtils;

  Falkonry falkonry   = new Falkonry("https://example.falkonry.ai", "auth-token");
  Map<String, String> options = new HashMap<String, String>();
  Map<String, String> options = new HashMap<String, String>();
    options.put("timeIdentifier", "time");
    options.put("timeFormat", "millis");
    options.put("fileFormat", "csv");
    options.put("streaming", "false");
    options.put("hasMoreData", "false");

    File file = new File("tmp/data.csv");     
      ByteArrayInputStream istream = new ByteArrayInputStream(FileUtils.readFileToByteArray(file));

      InputStatus inputStatus = falkonry.addInputStream('datastream-Id',byteArrayInputStream,options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://example.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
stream   = io.open('./data.csv')
        
# set hasMoreData to True if data is sent in batches. When the last batch is getting sent then set  'hasMoreData' to False. For single batch upload it shpuld always be set to False
options = {'streaming': False, 'hasMoreData':False}   
inputResponse = falkonry.add_input_data(datastreamId, 'csv', options, stream)
```

```shell

```

> Input data could be of the following format:

```csv
  time, entity, signal, value
  2016-03-01 01:01:01, thing1, signal1, 3.4
  2016-03-01 01:01:01, thing1, signal2, 1.4
```

csv data from a historian or time series data store can be imported into a Falkonry datastream

<aside class="success">
Falkonry needs historical time series data to build pattern recognition models.
</aside>


## Extract data from a datastream

It may help to extract all the data from an existing datastream for debug, editing, exporting to another datastream.


> To extract data from a datastream, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

//ID of datastream from which data is to be extracted
string datastreamId = 'id of the datastream';

//setting the format(CSV or JSON) of the data
var options = new SortedDictionary<string, string>();
options.Add("responseFormat", "application/json"); //For CSV format use ("responseFormat","text/csv")

var datastream_data = falkonry.GetDatastreamData(datastreamId, options)

//Exporting the data to an external file(for Json)
string file_name = 'input.json' #Name of the file with path
System.IO.File.WriteAllText("test.json", datastream_data.Response);

#We can use the exported file to add Data to other datastream or to create a new one with the same data    

```

```java
import com.falkonry.client.Falkonry
import com.fasterxml.jackson.databind.ObjectMapper;
import java.io.File;
import java.io.IOException;

Falkonry falkonry   = new Falkonry("https://example.falkonry.ai", "auth-token");
String datastream = "datastream-id";
Map<String, String> options = new HashMap<String, String>();
options.put("responseFormat", "text/csv");
HttpResponseFormat response = falkonry.getInputData(datastream, options);
String data = response.getResponse();

//We can use the exported file to add Data to other datastream or to create a new one with the same data

```

```python
import os, sys
from pprint import pprint
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

falkonry  = Falkonry('http://example.falkonry.ai', 'auth-token')

#Id of the datastream from which the data is to be extracted
datastreamId = 'id of the datastream'

#setting the format(CSV or JSON) of the data
options = {'format':"text/csv"} #For Json format use {'format':"application/json"}

datastream_data = falkonry.get_datastream_data(datastreamId, options)

#printing datastream's data 
pprint(datastream_data.text)

#Exporting the data to an external file
file_name = 'input.json' #Name of the file with path
with open(file_name, 'w') as file:
    file.write(str(datastream_data.text))

#We can use the exported file to add Data to other datastream or to create a new one with the same data    
    
```

```shell

falkonry>> datastream_get_data --format=application/json
Default datastream set : 1scyeeoxbdh7if Name : New Standalone
Input Data : 
============================================================================
{"time":1294078560000,"signal":"signal1","value":1.4}
{"time":1294091820000,"signal":"signal2","value":13.4}
{"time":1294099380000,"signal":"signal1","value":5.4}
{"time":1294078560000,"signal":"signal1","value":7.49}
{"time":1294091820000,"signal":"signal2","value":1.34}
{"time":1294099380000,"signal":"signal1","value":3.4}
{"time":1294078560000,"signal":"signal2","value":1.5}
{"time":1294091820000,"signal":"signal1","value":7.4}
{"time":1294099380000,"signal":"signal2","value":8.2}
{"time":1294078560000,"signal":"signal1","value":9.4}
{"time":1294091820000,"signal":"signal2","value":10.7}
============================================================================

falkonry>>
Writing input Data to file
falkonry>> datastream_get_data --format "application/json" --path "input.json"
Default datastream set : 1scyeeoxbdh7if Name : New Standalone
Input data is written to the file : input.json
falkonry>> 
```

## Delete datastream

> To delete a datastream using its id, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
falkonry.DeleteAssessment('assessment-id');
```


```java
  import com.falkonry.client.Falkonry;

  Falkonry falkonry = new Falkonry("https://example.falkonry.ai", "auth-token");

  Assessment assesssment = falkonry.deleteAssessment('assessment_id');
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://example.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'
        
falkonry.delete_datastream(datastreamId)
```

```shell
falkonry>> datastream_delete --id "oii0djojxc2lxt"
Datastream successfully deleted : oii0djojxc2lxt
falkonry>>
```

<aside class="notice">
Sometimes good things come to an end as well :(!
</aside>


## Add EntityMeta to a datastream

> To add EntityMeta to a datastream, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

// Create Datastream
datastream = falkonry.getDatastream(datastream.id);

// Create EntityMetaRequest
List<EntityMetaRequest> entityMetaRequestList = new List<EntityMetaRequest>();
EntityMetaRequest entityMetaRequest1 = new EntityMetaRequest();
  entityMetaRequest1.label = "User readbale label";
  entityMetaRequest1.sourceId = "1234-21342134";
  entityMetaRequest1.path = "//root/branch1/";

EntityMetaRequest entityMetaRequest2 = new EntityMetaRequest();
  entityMetaRequest2.label = "User readbale label2";
  entityMetaRequest2.sourceId = "1234-213421rawef";
  entityMetaRequest2.path = "//root/branch2/";

entityMetaRequestList.Add(entityMetaRequest1);
entityMetaRequestList.Add(entityMetaRequest2);

List<EntityMeta> entityMetaResponseList = falkonry.postEntityMeta(entityMetaRequestList, datastream);

```


```java
  import com.falkonry.client.Falkonry;
    
  Falkonry falkonry   = new Falkonry("https://example.falkonry.ai", "auth-token");
    String datastreamId = "hk7cgt56r3yln0";
    datastream = falkonry.getDatastream(datastreamId);
      EntityMetaRequest entityMetaRequest = new EntityMetaRequest();
      entityMetaRequest.setSourceId("entity1"); // Entity name which you have to change
      entityMetaRequest.setLabel("UNIT1"); // New Entity name
      entityMetaRequest.setPath(""); // For future use
      entityMetaRequests.add(entityMetaRequest);
      List<EntityMeta> entityMetas = falkonry.postEntityMeta(entityMetaRequests, datastream.getId());
```

```python
import os, sys
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry = Falkonry('https://example.falkonry.ai', 'auth-token')
data = [{"sourceId": "testId","label": "testName","path": "root/path"}]
datastreamId = 'id of the datastream'

entityMetaResponse = falkonry.add_entity_meta(datastreamId, {}, data)
```

```shell
falkonry>> datastream_add_entity_meta --path "/Users/user/EntityMetaRequest.json"
Entity Meta successfully added to datastream: oii0djojxc2lxt
falkonry>>
```

> Input data could be of the following format:

```json
[{"sourceId": "testId","label": "testName","path": "root/path"}]
```

EntityMeta or metadata associated with assets/ elements can be injected into an existing datastream as long as they have a similar schema.


## Get EntityMeta of a datastream

> To retrieve EntityMeta from a datastream, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
  // Get entitymeta
  entityMetaResponseList = falkonry.getEntityMeta('datastream-id');
```


```java
import com.falkonry.client.Falkonry;
    
  Falkonry falkonry   = new Falkonry("https://example.falkonry.ai", "auth-token");
    List<EntityMeta> entityMetas = falkonry.getEntityMeta("datastream_id"); //datastream's id
```

```python
import os, sys
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry = Falkonry('https://example.falkonry.ai', 'auth-token')
datastreamId = 'id of the datastream'

entityMetaResponse = falkonry.get_entity_meta(datastreamId)
```

```shell
falkonry>> datastream_get_entity_meta
Entity Meta of datastream: oii0djojxc2lxt
Entity Label : testName. Entity Id : testId
falkonry>>
```
> Input data could be of the following format:

```json
[{"sourceId": "testId","label": "testName","path": "root/path"}]
```

EntityMeta or metadata associated with assets/ elements can be injected into or extracted from an existing datastream as long as they have a similar schema.


# Assessments

The application of a model to signal data to produce a condition assessment is called recognition. The Falkonry Service supports real-time condition recognition on signal data.

<aside class="notice">
Assessments can be re-introduced into a data store as attributes to subsequently serve as datastream elements for collective or hierarchical assessments!  
</aside>


## Create Assessment

> To create an assessment, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

AssessmentRequest asmt = new AssessmentRequest();
  asmt.name = "assessment name here";
  asmt.datastream= "datastream id";
  Assessment assessment = falkonry.createAssessment(asmt);
```


```java
  import com.falkonry.client.Falkonry;
  import com.falkonry.helper.models.*;

  //instantiate Falkonry
  Falkonry falkonry = new Falkonry("https://example.falkonry.ai", "auth-token");

  Datastream ds = new Datastream();
  ds.setName("Test-DS-" + Math.random());
  TimeObject time = new TimeObject();
  time.setIdentifier("time");
  time.setFormat("iso_8601");
  time.setZone("GMT");

  Field field = new Field();
  field.setTime(time);
  ds.setField(field);
  Datasource dataSource = new Datasource();
  dataSource.setType("PI");
  dataSource.sethost("https://test.piserver.com/piwebapi");
  dataSource.setElementTemplateName("SampleElementTempalte");
  ds.setDatasource(dataSource);

  // Input List
  List<Input> inputList = new ArrayList<Input>();
  Input currents = new Input();
  ValueType valueType = new ValueType();
  EventType eventType = new EventType();
  currents.setName("current");
  valueType.setType("Numeric");
  eventType.setType("Samples");
  currents.setValueType(valueType);
  currents.setEventType(eventType);
  inputList.add(currents);

  Input vibration = new Input();
  vibration.setName("vibration");
  valueType.setType("Numeric");
  eventType.setType("Samples");
  vibration.setValueType(valueType);
  vibration.setEventType(eventType);
  inputList.add(vibration);

  Input state = new Input();
  state.setName("state");
  valueType.setType("Categorical");
  eventType.setType("Samples");
  state.setValueType(valueType);
  state.setEventType(eventType);
  inputList.add(state);

  ds.setInputList(inputList);

  Datastream datastream = falkonry.createDatastream(ds);

  AssessmentRequest assessmentRequest = new AssessmentRequest();
  assessmentRequest.setName("Health");
  assessmentRequest.setDatastream(datastream.getId());
  assessmentRequest.setAssessmentRate("PT1S");
  Assessment assessment = falkonry.createAssessment(assessmentRequest);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://example.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

asmtRequest = Schemas.AssessmentRequest()
asmtRequest.set_name('Assessment Name'))         # Set new assessment name
asmtRequest.set_datastream(response.get_id())    # Set datatsream id
asmtRequest.set_rate('PT0S')                     # Set assessment rate

# create new assessment
assessmentResponse = falkonry.create_assessment(asmtRequest)
```

```shell
Sample JSONFile:

{
  "name":"New Test Assessment",
  "datastream":"7wgwm68b9p24n4",
  "rate": "PT0S"
}

Usage :

falkonry>> assessment_create --path "/Users/user/samples/CreateAssessment.json"
Default datastream set : 7wgwm68b9p24n4 Name : Wide Multiple Entity Batch Test DS
Assessment successfully created : rrk2jl9ylvcthy
falkonry>>

Set default Assessment

You need to select default assessment for using features like adding facts to assessment, retrieving output data.

falkonry>> assessment_default_set --id "mhai7bxygkawq8"
Default datastream set: 5kzugwm1natt0l Name: Robo Arm Test 1
Default assessment set: mhai7bxygkawq8
falkonry>>

Get default Assessment
For fetching default assessment

falkonry>> assessment_default_get
Default assessment set : mhai7bxygkawq8 Name : Robo Arm Test 1
falkonry>>
```

Multiple assessments can exist for each datastream. An Assessment uses the data captured within a datastream to produce condition outputs.

Each datastream can have multiple assessments, while each assessment can have multiple models associated with it.

Only one of these models can be "active" when live monitoring is turned on.


<aside class="warning">
There may be an upper limit set to the number of assessments that can be created for your account.
If you would like to request more assessments, reach out to your Falkonry Admin.
</aside>


## Retrieve Assessments

> To retrieve all assessments, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
  List<Assessment> assessmentList = falkonry.getAssessments();
```


```java
  import com.falkonry.client.Falkonry;

  Falkonry falkonry = new Falkonry("https://example.falkonry.ai", "auth-token");

  List<Assessment> datastreams = falkonry.getAssessments();

```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://example.falkonry.ai', 'auth-token')

assessmentResponse = falkonry.get_assessments()
```

```shell
Usage :

falkonry>> assessment_get_list 
Default datastream set : 7wgwm68b9p24n4 Name : Wide Multiple Entity Batch Test DS
Fetching assessment list of datastream : 7wgwm68b9p24n4...
======================================================================================================
 Assessment Name                               Id                   Created By           Live Status         
======================================================================================================
 New Test Assessment                           rrk2jl9ylvcthy       e6q8ienqs9celz       OFF                 
 Wide Multiple Entity Batch Test DS            27cqvmcpq8tqqg       e6q8ienqs9celz       OFF                 
======================================================================================================
falkonry>> 
```
> The above command returns JSON structured like this:

```json
[
  {
    "id": "string",
    "sourceId": "string",
    "tenant": "string",
    "createdBy": "string",
    "updatedBy": "string",
    "createTime": 0,
    "updateTime": 0,
    "type": "string",
    "name": "string",
    "datastream": "string",
    "live": "ON",
    "factsMeasurement": "string",
    "aprioriConditionList": [
      "string"
    ],
    "activeModel": "string",
    "production": "string",
    "rate": "string"
  }
]
```

## Retrieve Assessment by id

> To retrieve a particular assessment by id, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
  Assessment assessment = falkonry.getAssessment('assessment-id');
```


```java
  import com.falkonry.client.Falkonry;

  Falkonry falkonry = new Falkonry("https://example.falkonry.ai", "auth-token");

  Assessment assesssment = falkonry.getAssessment('assessment_id');
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://example.falkonry.ai', 'auth-token')

assessmentId = 'id of the datastream'
assessmentResponse = falkonry.get_assessment(assessmentId)
```

```shell
Usage :

falkonry>> assessment_get_by_id --id "27cqvmcpq8tqqg"
======================================================
Id : 27cqvmcpq8tqqg
Name : Wide Multiple Entity Batch Test DS
Created By : e6q8ienqs9celz
Create Time : 2018-01-15 17:02:27.259000
Update Time : 2018-01-15 17:02:27.259000
Datastream : 7wgwm68b9p24n4
Live : OFF
Rate : PT0S
Condition List : N/A
======================================================
falkonry>> 
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": "string",
    "sourceId": "string",
    "tenant": "string",
    "createdBy": "string",
    "updatedBy": "string",
    "createTime": 0,
    "updateTime": 0,
    "type": "string",
    "name": "string",
    "datastream": "string",
    "live": "ON",
    "factsMeasurement": "string",
    "aprioriConditionList": [
      "string"
    ],
    "activeModel": "string",
    "production": "string",
    "rate": "string"
  }
]
```

## Delete Assessment

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
falkonry.DeleteAssessment('assessment-id');
```


```java
  import com.falkonry.client.Falkonry;

  Falkonry falkonry = new Falkonry("https://example.falkonry.ai", "auth-token");

  Assessment assesssment = falkonry.deleteAssessment('assessment_id');
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://example.falkonry.ai', 'auth-token')

assessmentId = 'id of the datastream'
assessmentResponse = falkonry.delete_assessment(assessmentId)
```

```shell
Usage :

falkonry>> assessment_delete --id "7wgwm68b9p24n4"
Assessment deleted successfully: 7wgwm68b9p24n4
falkonry>>
```

## Get Historian Output from Assessment

> To retrieve historical output generated by an assessment from the historian, use this code: 

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

// Create Datastream

// Create Assessment

// Add Data To dataStream

// From Falkonry UI, run a model revision.

// Fetch Historical output data for given assessment, startTime , endtime
SortedDictionary<string, string> options = new SortedDictionary<string, string>();
  options.Add("startTime", "2011-01-01T01:00:00.000Z"); // in the format YYYY-MM-DDTHH:mm:ss.SSSZ
  options.Add("endTime", "2011-06-01T01:00:00.000Z");  // in the format YYYY-MM-DDTHH:mm:ss.SSSZ
  options.Add("responseFormat", "application/json");  // also avaibale options 1. text/csv 2. application/json

HttpResponse httpResponse = falkonry.getHistoricalOutput(assessment, options);
// If data is not readily avaiable then, a tracker id will be sent with 202 status code. While falkonry will genrate ouptut data
// Client should do timely pooling on the using same method, sending tracker id (__id) in the query params
// Once data is avaiable server will response with 200 status code and data in json/csv format.

if (httpResponse.statusCode == 202)
{
  Tracker trackerResponse = javascript.Deserialize<Tracker>(httpResponse.response);
  // get id from the tracker
  string __id = trackerResponse.__id;

  // use this tracker for checking the status of the process.
  options = new SortedDictionary<string, string>();
  options.Add("trackerId", __id);
  options.Add("responseFormat", "application/json");

  httpResponse = falkonry.getHistoricalOutput(assessment, options);

  // if status is 202 call the same request again

  // if statsu is 200, output data will be present in httpResponse.response field
}
if (httpResponse.statusCode > 400)
{
  // Some Error has occured. Please httpResponse.response for detail message
}
```


```java
  Map<String, String> options = new HashMap<String, String>();
  options.put("startTime", "2011-07-17T01:00:00.000Z"); // in the format YYYY-MM-DDTHH:mm:ss.SSSZ
  options.put("endTime", "2011-08-18T01:00:00.000Z");  // in the format YYYY-MM-DDTHH:mm:ss.SSSZ
  options.put("responseFormat", "application/json");  // also avaibale options 1. text/csv 2. application/json

  assessment.setId("wpyred1glh6c5r");
  HttpResponseFormat httpResponse = falkonry.getHistoricalOutput(assessment_id, options);
```

```python
import os, sys
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

falkonry  = Falkonry('https://example.falkonry.ai', 'auth-token')
assessmentId = 'id of the datastream'

options = {'startTime':'2011-01-01T01:00:00.000Z','endTime':'2011-06-01T01:00:00.000Z','responseFormat':'application/json'}

response = falkonry.get_historical_output(assessmentId, options)

'''If data is not readily available then, a tracker id will be sent with 202 status code. While falkonry will genrate ouptut data
 Client should do timely pooling on the using same method, sending tracker id (__id) in the query params
 Once data is available server will response with 200 status code and data in json/csv format.'''

if response.status_code is 202:
    trackerResponse = Schemas.Tracker(tracker=response._content)
    #get id from the tracker
    trackerId = trackerResponse.get_id()
    #use this tracker for checking the status of the process.
    options = {"trackerId": trackerId, "responseFormat":"application/json"}
    newResponse = falkonry.get_historical_output(assessment, options)
    '''if status is 202 call the same request again
    if status is 200, output data will be present in httpResponse.response field'''
```

```shell
Options:

--path=PATH file path to write output
--trackerId=TRACKERID
tracker id of the previous output request
--modelIndex=MODELINDEX
index of the model of which output needs to be fetched
--startTime=STARTTIME
startTime of the output range
--endTime=ENDTIME endTime of the output range
--format=FORMAT format of the output. For csv pass text/csv. For JSON
output pass application/json
Usage:

Fetching data which is already processed
falkonry>> assessment_get_historical_output --startTime "2017-07-19T14:04:06+05:30" --format "text/csv"
Default assessment set : 743cveg32hkwl2 Name : Standalone DS
==================================================================================================================
time,entity,value
1500453246798,UNIT-1,unlabeled5
1500453251847,UNIT-1,unlabeled2
1500453256896,UNIT-1,unlabeled6
1500453261945,UNIT-1,unlabeled1
1500453266994,UNIT-1,unlabeled4
1500453272043,UNIT-1,unlabeled1
1500453277092,UNIT-1,unknown
1500453282141,UNIT-1,unlabeled1
1500453287190,UNIT-1,unlabeled1
1500453292239,UNIT-1,unknown
1500453297288,UNIT-1,unknown
1500453302337,UNIT-1,unknown
1500453307386,UNIT-1,unlabeled1

==================================================================================================================
falkonry>>
Fetching data which is not processed
If data is not readily available then, a tracker id will be sent with 202 status code. While falkonry will genrate ouptut data
Client should do timely pooling on the using same method, sending tracker id (__id) in the query params
Once data is available server will response with 200 status code and data in json/csv format.

falkonry>> assessment_get_historical_output --startTime "2016-07-19T14:04:06+05:30" --format "text/csv"
Default assessment set : 743cveg32hkwl2 Name : Standalone DS
{"status":"WAITING","assessment":"743cveg32hkwl2","datastream":"qh6rg4ce5g3p5j","outputSummary":"c8cr9bedv4y2jg","model":"r1ao169d3icdl3","pid":"A8vkl6bxn86qh0_umm1j0j08f1pbd","mode":"ANALYSIS","userEmail":"aniket.amrutkar@falkonry.com","userId":"c8s6wwrfczrwos","entities":null,"__$id":"nphnmcc81vqkgpvo","__$tenant":"A8vkl6bxn86qh0","__$createTime":1500544370048,"__id":"nphnmcc81vqkgpvo"}
Falkonry is generating your output. Please try following command in some time.
assessment_get_historical_output --trackerId=nphnmcc81vqkgpvo
falkonry>> assessment_get_historical_output --trackerId "nphnmcc81vqkgpvo" --format "application/json"
Default assessment set : 743cveg32hkwl2 Name : Standalone DS
==================================================================================================================
{"time":1500453241749,"entity":"UNIT-1","value":"unlabeled3"}
{"time":1500453246798,"entity":"UNIT-1","value":"unlabeled5"}
{"time":1500453251847,"entity":"UNIT-1","value":"unlabeled2"}
{"time":1500453256896,"entity":"UNIT-1","value":"unlabeled6"}
{"time":1500453261945,"entity":"UNIT-1","value":"unlabeled1"}
{"time":1500453266994,"entity":"UNIT-1","value":"unlabeled4"}
{"time":1500453272043,"entity":"UNIT-1","value":"unlabeled1"}
{"time":1500453277092,"entity":"UNIT-1","value":"unknown"}
{"time":1500453282141,"entity":"UNIT-1","value":"unlabeled1"}
{"time":1500453287190,"entity":"UNIT-1","value":"unlabeled1"}
{"time":1500453292239,"entity":"UNIT-1","value":"unknown"}
{"time":1500453297288,"entity":"UNIT-1","value":"unknown"}
{"time":1500453302337,"entity":"UNIT-1","value":"unknown"}
{"time":1500453307386,"entity":"UNIT-1","value":"unlabeled1"}

==================================================================================================================
falkonry>>
Writing output Data to file
falkonry>> assessment_get_historical_output --trackerId "nphnmcc81vqkgpvo" --format "application/json" --path "/Users/user/Output.json"
Default assessment set : 743cveg32hkwl2 Name : Standalone DS
Output data is written to the file : /Users/user/Output.json
falkonry>>
```

# Facts

A fact is a known condition for a particular episode of time. Facts can come from external sources, inspection reports, or investigations. Facts data typically becomes available after events have passed.

Facts are associated with assessments.

Facts can be added from inspection logs, failure reports, user/ expert observations, event frames within a historian, etc 

## Add facts data (json format) to Assessment of single entity datastream

> To add more facts to your assessment, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

SortedDictionary<string, string> options = new SortedDictionary<string, string>();
options.Add("startTimeIdentifier", "time");
options.Add("endTimeIdentifier", "end");
options.Add("timeFormat", "iso_8601");
options.Add("timeZone", "GMT");
options.Add("valueIdentifier", "Health");

string token = "Add your token here";   
SortedDictionary<string, string> options = new SortedDictionary<string, string>();
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
String data = "{\"time\" : \"2011-03-26T12:00:00Z\", \"end\" : \"2012-06-01T00:00:00Z\", \"Health\" : \"Normal\"}";
string response = falkonry.addFacts('assessment-id',data, options);
```


```java
import com.falkonry.client.Falkonry;

Falkonry falkonry = new Falkonry("https://example.falkonry.ai", "auth-token");
Map<String, String> options = new HashMap<String, String>();
options.put("startTimeIdentifier", "time");
options.put("endTimeIdentifier", "end");
options.put("timeFormat", "iso_8601");
options.put("timeZone", "GMT");
options.put("valueIdentifier", "Health"); 
String data = "{\"time\" : \"2011-03-26T12:00:00.000Z\", \"end\" : \"2012-06-01T00:00:00.000Z\", \"Health\" : \"Normal\"}";
String response = falkonry.addfacts(assessment.getId(),data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry      = Falkonry('http://example.falkonry.ai', 'auth-token')
assessmentId = 'id of the assessment'
data          = '{"time" : "2011-03-26T12:00:00.000Z", "end" : "2012-06-01T00:00:00.000Z", "Health" : "Normal"}'

options = {
  'startTimeIdentifier': "time",
  'endTimeIdentifier': "end",
  'timeFormat': "iso_8601",
  'timeZone': "GMT",
  'valueIdentifier': "Health"
}
inputResponse = falkonry.add_facts(assessmentId, 'json', options, data)
```

```shell
Sample JSONFile / Facts Data:
{"time":1456774261072,"end":1456774261116,"value":"label1"}
{"time":1456774261321,"end":1456774261362,"value":"label2"}
{"time":1456774261538,"end":1456774261570,"value":"label1"}
{"time":1456774261723,"end":1456774261754,"value":"label2"}

Usage:

falkonry>> assessment_add_facts --path "/Users/user/NarrowFacts.json" --startTimeIdentifier "time" --endTimeIdentifier "end" --timeFormat "millis" --timeZone "GMT" --valueIdentifier "value"
Default assessment set : hv987ptckdc6n7 Name : New Test Assessment
{'status': 'PENDING', 'datastream': 'cr77vk6mkwqwqq', '__$createTime': 1516022010240, '__$id': 'lcq4pp9jcvwgcgmp', 'action': 'ADD_FACT_DATA', '__$tenant': 'iqn80x6e2ku9id', 'assessment': 'hv987ptckdc6n7'}
falkonry>> 

```


## Add facts data (json format) with addition keyword to Assessment of multi entity datastream

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

SortedDictionary<string, string> options = new SortedDictionary<string, string>();
options.Add("startTimeIdentifier", "time");
options.Add("endTimeIdentifier", "end");
options.Add("timeFormat", "iso_8601");
options.Add("timeZone", "GMT");
options.Add("entityIdentifier", "entities");
options.Add("valueIdentifier", "Health");
options.Add("additionalKeyword", "testTag");

string token = "Add your token here";   
SortedDictionary<string, string> options = new SortedDictionary<string, string>();
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
String data = "{\"time\" : \"2011-03-26T12:00:00Z\", \"entities\" : \"entity1\", \"end\" : \"2012-06-01T00:00:00Z\", \"Health\" : \"Normal\"}";
string response = falkonry.addFacts('assessment-id',data, options);
```

```java
import com.falkonry.client.Falkonry;

Falkonry falkonry = new Falkonry("https://example.falkonry.ai", "auth-token");
Map<String, String> options = new HashMap<String, String>();
options.put("startTimeIdentifier", "time");
options.put("endTimeIdentifier", "end");
options.put("timeFormat", "iso_8601");
options.put("timeZone", "GMT");
options.put("valueIdentifier", "Health"); 
options.put("entityIdentifier", "entities");
options.put("additionalKeyword", "testTag");

String data = "{\"time\" : \"2011-03-26T12:00:00.000Z\", \"entities\" : \"entity1\", \"end\" : \"2012-06-01T00:00:00.000Z\", \"Health\" : \"Normal\"}";
String response = falkonry.addfacts(assessment.getId(),data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry      = Falkonry('http://example.falkonry.ai', 'auth-token')
assessmentId = 'id of the assessment'
data          = '{"time" : "2011-03-26T12:00:00.000Z", "end" : "2012-06-01T00:00:00.000Z", "entities": "entity1", "Health" : "Normal"}'

options = {
  'startTimeIdentifier': "time",
  'endTimeIdentifier': "end",
  'timeFormat': "iso_8601",
  'timeZone': "GMT",
  'valueIdentifier': "Health",
  'entityIdentifier': 'entities',
  'additionalKeyword': 'testTag'
}
inputResponse = falkonry.add_facts(assessmentId, 'json', options, data)
```

```shell
Sample JSONFile / Facts Data:
{"time":1456774261061,"end":1456774261103,"value":"label1"}
{"time":1456774261213,"end":1456774261258,"value":"label2"}
{"time":1456774261455,"end":1456774261512,"value":"label1"}
{"time":1456774261715,"end":1456774261767,"value":"label2"}

Usage:
falkonry>> assessment_add_facts --path "/Users/user/MoreFacts.json" --startTimeIdentifier "time" --endTimeIdentifier "end" --timeFormat "millis" --timeZone "GMT" --valueIdentifier "value" --additionalKeyword "testTag"
Default assessment set : hv987ptckdc6n7 Name : New Test Assessment
{'status': 'PENDING', 'datastream': 'cr77vk6mkwqwqq', '__$createTime': 1516087411723, '__$id': '4l8bgmd6rv2qj77j', 'action': 'ADD_FACT_DATA', '__$tenant': 'iqn80x6e2ku9id', 'assessment': 'hv987ptckdc6n7'}
falkonry>>

```


## Add facts data (csv format) to Assessment of single entity datastream

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

SortedDictionary<string, string> options = new SortedDictionary<string, string>();
options.Add("startTimeIdentifier", "time");
options.Add("endTimeIdentifier", "end");
options.Add("timeFormat", "iso_8601");
options.Add("timeZone", "GMT");
options.Add("valueIdentifier", "Health");

string token = "Add your token here";   
SortedDictionary<string, string> options = new SortedDictionary<string, string>();
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
string data = "time,end,Health\n2011-03-31T00:00:00Z,2011-04-01T00:00:00Z,Normal\n2011-03-31T00:00:00Z,2011-04-01T00:00:00Z,Normal";
string response = falkonry.addFacts('assessment-id',data, options);

```

```java
import com.falkonry.client.Falkonry;

Falkonry falkonry = new Falkonry("http://example.falkonry.ai", "auth-token");
Map<String, String> options = new HashMap<String, String>();
options.put("startTimeIdentifier", "time");
options.put("endTimeIdentifier", "end");
options.put("timeFormat", "iso_8601");
options.put("timeZone", "GMT");
options.put("valueIdentifier", "Health");
String data = "time,end,Health\n2011-03-31T00:00:00.000Z,2011-04-01T00:00:00.000Z,Normal\n2011-03-31T00:00:00.000Z,2011-04-01T00:00:00.000Z,Normal";
String response = falkonry.addFacts(assessment.getId(),data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry     = Falkonry('http://example.falkonry.ai', 'auth-token')
assessmentId = 'id of the assessment'
data         = 'time,end,Health' + "\n"
     + '2011-03-26T12:00:00.000Z,2012-06-01T00:00:00.000Z,Normal' + "\n"
     + '2014-02-10T23:00:00.000Z,2014-03-20T12:00:00.000Z,Spalling';

options = {
  'startTimeIdentifier': "time",
  'endTimeIdentifier': "end",
  'timeFormat': "iso_8601",
  'timeZone': "GMT",
  'valueIdentifier': "Health"
}

inputResponse = falkonry.add_facts(assessmentId, 'csv', options, data)
```

```shell
Sample JSONFile / Facts Data:

time,end,value
"1456774261072","1456774261116","label1"
"1456774261321","1456774261362","label2"
"1456774261538","1456774261570","label1"
"1456774261723","1456774261754","label2"

Usage:

falkonry>> assessment_add_facts --path "/Users/user/NarrowFacts.csv" --startTimeIdentifier "time" --endTimeIdentifier "end" --timeFormat "millis" --timeZone "GMT" --valueIdentifier "value"
Default assessment set : bqn8vjdb7yr9km Name : new test
{'status': 'PENDING', 'datastream': 'cr77vk6mkwqwqq', '__$createTime': 1516089750789, '__$id': 'm7b2l694wcptccmk', 'action': 'ADD_FACT_DATA', '__$tenant': 'iqn80x6e2ku9id', 'assessment': 'bqn8vjdb7yr9km'}
falkonry>>
```


## Add facts data (csv format) with keywords Assessment of multi entity datastream

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

SortedDictionary<string, string> options = new SortedDictionary<string, string>();
options.Add("startTimeIdentifier", "time");
options.Add("endTimeIdentifier", "end");
options.Add("timeFormat", "iso_8601");
options.Add("timeZone", "GMT");
options.Add("entityIdentifier", "car");
options.Add("valueIdentifier", "Health");
options.Add("keywordIdentifier", "Tag");

string token = "Add your token here";   
SortedDictionary<string, string> options = new SortedDictionary<string, string>();
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
string data = "time,end,car,Health,Tag\n2011-03-31T00:00:00Z,2011-04-01T00:00:00Z,IL9753,Normal\n2011-03-31T00:00:00Z,2011-04-01T00:00:00Z,HI3821,Normal,testTag1";
string response = falkonry.addFacts('assessment-id',data, options);

```

```java
import com.falkonry.client.Falkonry;

Falkonry falkonry = new Falkonry("http://example.falkonry.ai", "auth-token");
Map<String, String> options = new HashMap<String, String>();
options.put("startTimeIdentifier", "time");
options.put("endTimeIdentifier", "end");
options.put("timeFormat", "iso_8601");
options.put("timeZone", "GMT");
options.put("entityIdentifier", "car");
options.put("valueIdentifier", "Health");
options.put("keywordIdentifier", "Tag");

String data = "time,end,car,Health,Tag\n2011-03-31T00:00:00.000Z,2011-04-01T00:00:00.000Z,IL9753,Normal,testTag1\n2011-03-31T00:00:00.000Z,2011-04-01T00:00:00.000Z,HI3821,Normal,testTag2";
String response = falkonry.addFacts(assessment.getId(),data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry     = Falkonry('http://example.falkonry.ai', 'auth-token')
assessmentId = 'id of the assessment'
data         = 'time,car,end,Health,Tag' + "\n"
     + '2011-03-26T12:00:00.000Z,HI3821,2012-06-01T00:00:00.000Z,Normal,testTag1' + "\n"
     + '2014-02-10T23:00:00.000Z,HI3821,2014-03-20T12:00:00.000Z,Spalling,testTag2';

options = {
  'startTimeIdentifier': "time",
  'endTimeIdentifier': "end",
  'timeFormat': "iso_8601",
  'timeZone': "GMT",
  'entityIdentifier': "car",
  'valueIdentifier': "Health",
  'keywordIdentifier': 'Tag'
}

inputResponse = falkonry.add_facts(assessmentId, 'csv', options, data)
```

```shell
Sample CSVFile / Facts Data:

time,end,value,tagId
"1456774261072","1456774261116","label1","tag1"
"1456774261321","1456774261362","label2","tag1"
"1456774261538","1456774261570","label1","tag2"
"1456774261723","1456774261754","label2","tag2"
Usage:

falkonry>> assessment_add_facts --path "/Users/user/TagFacts.csv" --startTimeIdentifier "time" --endTimeIdentifier "end" --timeFormat "millis" --timeZone "GMT" --valueIdentifier "value" --keywordIdentifier "tagId"
Default assessment set : 4rrp97lk9gcvwc Name : test new
{'status': 'PENDING', 'datastream': 'cr77vk6mkwqwqq', '__$createTime': 1516090649022, '__$id': 'hly8b4727wm9prq8', 'action': 'ADD_FACT_DATA', '__$tenant': 'iqn80x6e2ku9id', 'assessment': '4rrp97lk9gcvwc'}
falkonry>>
```

Often, facts and machine/ asset state are captured in spreadsheets. 

Use this to inject csv format facts into an assessment.

<aside class="notice">
Adding more facts (ground truths) helps improve model accuracy and hence the usefulness of an assessment.
</aside>

## Add facts data (json format) from a stream to Assessment of multi entity datastream

Sample JSONFile:
```
{"time" : "2011-03-26T12:00:00.000Z", "car" : "HI3821", "end" : "2012-06-01T00:00:00.000Z", "Health" : "Normal"}
{"time" : "2014-02-10T23:00:00.000Z", "car" : "HI3821", "end" : "2014-03-20T12:00:00.000Z", "Health" : "Spalling"}
```


> To add facts in json format from an input stream, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

SortedDictionary<string, string> options = new SortedDictionary<string, string>();
options.Add("startTimeIdentifier", "time");
options.Add("endTimeIdentifier", "end");
options.Add("timeFormat", "iso_8601");
options.Add("timeZone", "GMT");
options.Add("entityIdentifier", "car");
options.Add("valueIdentifier", "Health");

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
string path = "Insert the path to your file here";
byte[] bytes = System.IO.File.ReadAllBytes(path);
string response = falkonry.AddFactsStream('assessment-id',bytes, options);
```


```java
import com.falkonry.client.Falkonry;
import org.apache.commons.io.FileUtils;

Falkonry falkonry   = new Falkonry("http://example.falkonry.ai", "auth-token");
File file = new File("res/factsData.json"); 
Map<String, String> options = new HashMap<String, String>();
options.put("startTimeIdentifier", "time");
options.put("endTimeIdentifier", "end");
options.put("timeFormat", "iso_8601");
options.put("timeZone", "GMT");
options.put("entityIdentifier", "car");
options.put("valueIdentifier", "Health");       
ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(FileUtils.readFileToByteArray(file));
String response = falkonry.addFactsStream(assessment.getId(),byteArrayInputStream, options)
```

```python
import os, sys
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

falkonry = Falkonry('http://example.falkonry.ai', 'auth-token')
assessmentId = 'id of the assessment'
stream   = io.open('./factsData.json')

options = {
  'startTimeIdentifier': "time",
  'endTimeIdentifier': "end",
  'timeFormat': "iso_8601",
  'timeZone': "GMT",
  'entityIdentifier': "car",
  'valueIdentifier': "Health"
}

response = falkonry.add_facts_stream(assessmentId, 'json', options, stream)
```

```shell

```

If facts are created from another application, they are often exported in json format.

<aside class="notice">
Adding more facts (ground truths) helps improve model accuracy and hence the usefulness of an assessment.
</aside>

## Add facts data (csv format) from a stream to Assessment of multi entity datastream

> To add facts in csv format from an input stream, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

SortedDictionary<string, string> options = new SortedDictionary<string, string>();
options.Add("startTimeIdentifier", "time");
options.Add("endTimeIdentifier", "end");
options.Add("timeFormat", "iso_8601");
options.Add("timeZone", "GMT");
options.Add("entityIdentifier", "car");
options.Add("valueIdentifier", "Health");

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
string path = "Insert the path to your file here";
byte[] bytes = System.IO.File.ReadAllBytes(path);
string response = falkonry.AddFactsStream('assessment-id',bytes, options);
```


```java
import com.falkonry.client.Falkonry;
import org.apache.commons.io.FileUtils;

Falkonry falkonry   = new Falkonry("http://example.falkonry.ai", "auth-token");
File file = new File("res/factsData.csv"); 
Map<String, String> options = new HashMap<String, String>();
options.put("startTimeIdentifier", "time");
options.put("endTimeIdentifier", "end");
options.put("timeFormat", "iso_8601");
options.put("timeZone", "GMT");
options.put("entityIdentifier", "entity");
options.put("valueIdentifier", "Health");     
ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(FileUtils.readFileToByteArray(file));
String response = falkonry.addFactsStream(assessment.getId(),byteArrayInputStream, options);
```

```python
import os, sys
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas
from falkonryclient import schemas as Schemas

falkonry = Falkonry('http://example.falkonry.ai', 'auth-token')
assessmentId = 'id of the assessment'
stream   = io.open('./factsData.csv')

options = {
  'startTimeIdentifier': "time",
  'endTimeIdentifier': "end",
  'timeFormat': "iso_8601",
  'timeZone': "GMT",
  'entityIdentifier': "car",
  'valueIdentifier': "Health"
}

response = falkonry.add_facts_stream(assessmentId, 'csv', options, stream)

```

```shell

```

Often, facts and machine/ asset state are captured in spreadsheets. 

Use this to inject csv format facts into an assessment.

<aside class="notice">
Adding more facts (ground truths) helps improve model accuracy and hence the usefulness of an assessment.
</aside>


# Live Monitoring

After a model revision has been trained, a user may want to switch to live monitoring to evaluate patterns in real-time.

Any model revision can be selected to be the basis of live monitoring. Falkonry will assign conditions to multi-variate patterns based on the signals that were used to create the model revision.

## Check live monitoring Status of datastream

> To check whether live monitoring is on for a datastream, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
  string datastream_id = "Your datastream id";
  Datastream ds = falkonry.GetDatastream("datastream_id");
  var status = ds.Live; //live status will be "ON" or "OFF".

```

```java
  import com.falkonry.client.Falkonry;

  Falkonry falkonry   = new Falkonry("https://example.falkonry.ai", "auth-token");
  String datastreamId = "hk7cgt56r3yln0";
  Datastream datastream = falkonry.getDatastream(datastreamId);
  String status = datastream.Live; //live status will be "ON" or "OFF".
```

```python
import os, sys
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

falkonry  = Falkonry('https://example.falkonry.ai', 'auth-token')
datastreamId = 'id of the datastream'
datastream = falkonry.get_datastream(datastreamId)
status = datastream.get_live()//live status will be "ON" or "OFF".
```

```shell
falkonry>> datastream_get_live_status
Default datastream set: 5kzugwm1natt0l Name: Robo Arm Test 1
Fetching Live monitoring status for datastream : 5kzugwm1natt0l
Live Monitoring : ON //live status will be "ON" or "OFF". 
falkonry>>
```

## Start live monitoring of datastream

> To turn live monitoring on for a datastream, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
string datastream_id = "Your datastream id";
falkonry.onDatastream(datastream_id);
```

```java
import com.falkonry.client.Falkonry;

Falkonry falkonry   = new Falkonry("https://example.falkonry.ai", "auth-token");
String datastreamId = "hk7cgt56r3yln0";
List<Assessment> liveAssessments = falkonry.onDatastream(datastreamId);
```

```python
import os, sys
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

falkonry  = Falkonry('https://example.falkonry.ai', 'auth-token')
datastreamId = 'id of the datastream'

# Starts live monitoring of datastream. For live monitoring datastream must have atleast one assessment with active model. 
response = falkonry.on_datastream(datastreamId)
```

```shell
falkonry>> datastream_start_live
Default datastream set: 5kzugwm1natt0l Name: Robo Arm Test 1
Turning on Live monitoring for datastream : 5kzugwm1natt0l
Datastream is ON for live monitoring
falkonry>>
```

Live monitoring is associated with the entire datastream. At this point each assessment assciated with the datastream goes into live monitoring.

Every assessment has an "active" model associated with it.

<aside class="notice">
Live monitoring can be turned "on" from the Falkonry Professional UI but you can do the same from any of these development kits.
</aside>

## Stop live monitoring of datastream

> To turn live monitoring off for a datastream, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);
string datastream_id = "Your datastream id";
falkonry.offDatastream(datastream_id);
```


```java
import com.falkonry.client.Falkonry;

Falkonry falkonry   = new Falkonry("https://example.falkonry.ai", "auth-token");
String datastreamId = "hk7cgt56r3yln0";
List<Assessment> assessments = falkonry.offDatastream(datastreamId);
```

```python
import os, sys
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

falkonry  = Falkonry('https://example.falkonry.ai', 'auth-token')
datastreamId = 'id of the datastream'

# Stops live monitoring of datastream.
response = falkonry.off_datastream(datastreamId)
```

```shell
falkonry>> datastream_stop_live
Default datastream set: 5kzugwm1natt0l Name : Robo Arm Test 1
Turning off Live monitoring for datastream : 5kzugwm1natt0l
Datastream is OFF for live monitoring
falkonry>>
```

<aside class="notice">
Live monitoring can be turned "on" from the Falkonry Professional UI but you can do the same from any of these development kits.
</aside>


## Add live input data (json) to datastream

> To inject live input data in json format into the datastream, use this code:


```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

string data = "{\"time\" :\"2016-03-01 01:01:01\", \"current\" : 12.4, \"vibration\" : 3.4, \"state\" : \"On\"}";

SortedDictionary<string, string> options = new SortedDictionary<string, string>();
  options.Add("timeIdentifier", "time");
  options.Add("timeFormat", "iso_8601");
  options.Add("fileFormat", "json");
  options.Add("streaming", "true");
  options.Add("hasMoreData", "false");
InputStatus inputstatus = falkonry.addInput('datastream-id', data, options);
```


```java
import com.falkonry.client.Falkonry;
import com.falkonry.helper.models.Datasource;
import com.falkonry.helper.models.Datastream;
import com.falkonry.helper.models.Field;
import com.falkonry.helper.models.TimeObject;
import com.falkonry.helper.models.Signal;

//instantiate Falkonry
Falkonry falkonry = new Falkonry("https://example.falkonry.ai", "auth-token");
//Add data to datastream
String data = "{\"time\" : \"2016-03-01 01:01:01\", \"signal\" : \"signal1\", \"value\" : 3.4}" + "\n"
    + "{\"time\" : \"2016-03-01 01:01:02\", \"signal\" : \"signal2\", \"value\" : 9.3}";

Map<String, String> options = new HashMap<String, String>();
  options.put("timeIdentifier", "time");
  options.put("signalIdentifier", "time");
  options.put("valueIdentifier", "time");
  options.put("timeFormat", "iso_8601");
  options.put("fileFormat", "csv");
  options.put("streaming", "true");
falkonry.addInput('datastream-Id', data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://example.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
String data = "{\"time\" : \"2016-03-01 01:01:01\", \"signal\" : \"signal1\", \"value\" : 3.4}" + "\n"
        + "{\"time\" : \"2016-03-01 01:01:01\", \"signal\" : \"signal2\", \"value\" : 1.4}" + "\n"
        + "{\"time\" : \"2016-03-01 01:01:02\", \"signal\" : \"signal1\", \"value\" : 9.3}" + "\n"
        + "{\"time\" : \"2016-03-01 01:01:02\", \"signal\" : \"signal2\", \"value\" : 4.3}";
        
options = {
  'timeIdentifier': 'time',
  'signalIdentifier': 'signal',
  'valueIdentifier': 'value',
  'fileFormat': 'csv',
  'timeFormat': 'millis',
  'streaming': True
}
inputResponse = falkonry.add_input_data(datastreamId, 'json', options, data)
```

```shell
Data :

{"time":"1447882550000","activity":"walking","person":"p1","end":"1447882565000"}
{"time":"1447882565000","activity":"sitting","person":"p1","end":"1447882570000"}
{"time":"1447882575000","activity":"cycling","person":"p1","end":"1447882580000"}
{"time":"1447882580000","activity":"sitting","person":"p1","end":"1447882585000"}
{"time":"1447882590000","activity":"sitting","person":"p1","end":"1447882595000"}
{"time":"1447882595000","activity":"walking","person":"p1","end":"1447882600000"}
{"time":"1447882600000","activity":"cycling","person":"p1","end":"1447882605000"}

Usage :

datastream_add_live_data --path "/Users/user/Input.csv"
Default datastream set : lg7k1a5jor1nvh Name : Human Activity
{'message': 'Data submitted successfully'}
```
When in live monitoring, the assessment is being computed based on live data that is streaming in, but based on the model that was built using historical data.

<aside class="warning">
When working from within the Falkonry Professional UI, make sure "Accepting Data" in turned on before starting "Live Monitoring"
</aside>

## Add live data (csv) to datastream

> To inject live input data in csv format into the datastream, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

string data = "time, entity, signal1, signal2, signal3, signal4" + "\n"
      + "1467729675422, entity1, 41.11, 62.34, 77.63, 4.8" + "\n"
      + "1467729675445, entity1, 43.91, 82.64, 73.63, 3.8";

SortedDictionary<string, string> options = new SortedDictionary<string, string>();
  options.Add("timeIdentifier", "time");
  options.Add("entityIdentifier", "entity");
  options.Add("timeFormat", "iso_8601");
  options.Add("fileFormat", "csv");
  options.Add("streaming", "true");
  InputStatus inputstatus = falkonry.addInput('datastream-id', data, options);
```


```java
import com.falkonry.client.Falkonry;
import com.falkonry.helper.models.Datasource;
import com.falkonry.helper.models.Datastream;
import com.falkonry.helper.models.Field;
import com.falkonry.helper.models.TimeObject;
import com.falkonry.helper.models.Signal;

//instantiate Falkonry
Falkonry falkonry = new Falkonry("https://example.falkonry.ai", "auth-token");
//Add data to datastream
  String data = "time, entity, signal1, signal2, signal3, signal4" + "\n"
      + "1467729675422, entity1, 41.11, 62.34, 77.63, 4.8" + "\n"
      + "1467729675445, entity1, 43.91, 82.64, 73.63, 3.8";
  Map<String, String> options = new HashMap<String, String>();
    options.put("timeIdentifier", "time");
    options.Add("entityIdentifier", "entity");
    options.put("timeFormat", "millis");
    options.put("fileFormat", "csv");
    options.put("streaming", "true");
    falkonry.addInput('datastream-Id', data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://example.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
String data = "time, entity, signal1, signal2, signal3, signal4" + "\n"
      + "1467729675422, entity1, 41.11, 62.34, 77.63, 4.8" + "\n"
      + "1467729675445, entity1, 43.91, 82.64, 73.63, 3.8"
        
options = {
  'timeIdentifier': 'time',
  'entityIdentifier': 'entity',
  'fileFormat': 'csv',
  'timeFormat': 'millis',
  'streaming': True
}
inputResponse = falkonry.add_input_data(datastreamId, 'csv', options, data)
```

```shell
Data:

time,activity,person,end
1447882550000,walking,p1,1447882565000
1447882565000,sitting,p1,1447882570000

Usage :

datastream_add_live_data --path "/Users/user/Input.json"
Default datastream set : lg7k1a5jor1nvh Name : Human Activity
{'message': 'Data submitted successfully'}
```

When in live monitoring, the assessment is being computed based on live data that is streaming in, but based on the model that was built using historical data.

<aside class="warning">
When working from within the Falkonry Professional UI, make sure "Accepting Data" is turned on before starting "Live Monitoring"
</aside>


## Add live data (json) from stream to datastream 

> To inject live input stream data in json format into the datastream, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

/*This particular example will read data from a AddData.json file in debug folder in bin*/

SortedDictionary<string, string> options = new SortedDictionary<string, string>();
  options.Add("timeIdentifier", "time");
  options.Add("timeFormat", "iso_8601");
  options.Add("streaming", "true");
  options.Add("hasMoreData", "false");
  options.Add("fileFormat", "json");

string folder_path = Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location);

string path = folder_path + "/AddData.json";
//Alternatively, you can directly specify the folder path in the "folder_path" variable

byte[] bytes = System.IO.File.ReadAllBytes(path);

InputStatus inputstatus = falkonry.addInputStream('datastream-id', bytes, options);
   
```


```java
import com.falkonry.client.Falkonry
import org.apache.commons.io.FileUtils;

  Falkonry falkonry   = new Falkonry("https://example.falkonry.ai", "auth-token");
  Map<String, String> options = new HashMap<String, String>();
    options.put("timeIdentifier", "time");
    options.put("timeFormat", "millis");
    options.put("fileFormat", "csv");
    options.put("streaming", "true");
    options.put("hasMoreData", "false");

    File file = new File("tmp/data.json");      
      ByteArrayInputStream istream = new ByteArrayInputStream(FileUtils.readFileToByteArray(file));

      InputStatus inputStatus = falkonry.addInputStream('datastream-Id',byteArrayInputStream,options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://example.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
stream   = io.open('./data.json')
        
options = {'streaming': True, 'hasMoreData':False}   
inputResponse = falkonry.add_input_data(datastreamId, 'json', options, stream)
```

```shell

```

When in live monitoring, the assessment is being computed based on live data that is streaming in, but based on the model that was built using historical data.

<aside class="warning">
When working from within the Falkonry Professional UI, make sure "Accepting Data" in turned on before starting "Live Monitoring"
</aside>


## Add live data (csv) from stream to datastream 

> To inject live input stream data in csv format into the datastream, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

/*This particular example will read data from a AddData.json file in debug folder in bin*/

SortedDictionary<string, string> options = new SortedDictionary<string, string>();
  options.Add("timeIdentifier", "time");
  options.Add("timeFormat", "iso_8601");
  options.Add("streaming", "false");
  options.Add("hasMoreData", "false");
  options.Add("fileFormat", "csv");

string folder_path = Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location);

string path = folder_path + "/AddData.csv";
//Alternatively, you can directly specify the folder path in the "folder_path" variable

byte[] bytes = System.IO.File.ReadAllBytes(path);

InputStatus inputstatus = falkonry.addInputStream('datastream-id', bytes, options);
```


```java
import com.falkonry.client.Falkonry
import org.apache.commons.io.FileUtils;

Falkonry falkonry   = new Falkonry("https://example.falkonry.ai", "auth-token");
Map<String, String> options = new HashMap<String, String>();
Map<String, String> options = new HashMap<String, String>();
  options.put("timeIdentifier", "time");
  options.put("timeFormat", "millis");
  options.put("fileFormat", "csv");
  options.put("streaming", "true");
  options.put("hasMoreData", "false");

File file = new File("tmp/data.csv");     
ByteArrayInputStream istream = new ByteArrayInputStream(FileUtils.readFileToByteArray(file));

InputStatus inputStatus = falkonry.addInputStream('datastream-Id',byteArrayInputStream,options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://example.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
stream   = io.open('./data.csv')
        
options = {'streaming': True}   
inputResponse = falkonry.add_input_data(datastreamId, 'csv', options, stream)
```

```shell

```

When in live monitoring, the assessment is being computed based on live data that is streaming in, but based on the model that was built using historical data.

<aside class="warning">
When working from within the Falkonry Professional UI, make sure "Accepting Data" in turned on before starting "Live Monitoring"
</aside>

## Get Streaming Output

> To retrieve live streaming assessment output, use this code:

```csharp
using FalkonryClient;
using FalkonryClient.Helper.Models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://example.falkonry.ai", token);

string assessment_id ="assessment ID here";


//On successfull live streaming output EventSource_Message will be triggered
private void EventSource_Message(object sender, EventSource.ServerSentEventArgs e)
{
    try { var falkonryEvent = JsonConvert.DeserializeObject<FalkonryEvent>(e.Data); }
    catch(System.Exception exception) 
    { //log error in case of error parsing the output event }
        
}

//On any error while getting live streaming output, EventSource_Error will be triggered
private void EventSource_Error(object sender, EventSource.ServerSentErrorEventArgs e)
{ // Error handling }

EventSource eventSource = falkonry.GetOutput(assessment,null,null);
eventSource.Message += EventSource_Message;
eventSource.Error += EventSource_Error;

// NOTE: To stop listening to output
eventSource.Dispose();
```


```java
import com.falkonry.client.Falkonry;

Falkonry falkonry   = new Falkonry("https://example.falkonry.ai", "auth-token");
String assessment = "wpyred1glh6c5r";
BufferedReader outputBuffer;
outputBuffer = falkonry.getOutput(assessment);
```

```python
import os, sys
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

falkonry  = Falkonry('https://example.falkonry.ai', 'auth-token')
assessmentId = 'id of the datastream'
stream    = falkonry.get_output(assessmentId)
for event in stream.events():
    print(json.dumps(json.loads(event.data)))
```

```shell
falkonry>>assessment_output_listen --format "application/json"
Fetching live assessments :
{"time":1500453241749,"entity":"UNIT-1","value":"unlabeled3"}
{"time":1500453246798,"entity":"UNIT-1","value":"unlabeled5"}
```
Assessments in live monitoring mode can be streamed out to suggest real time machine/ asset condition/ state.






<!--  -->