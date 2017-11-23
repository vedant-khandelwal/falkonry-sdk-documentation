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

# Falkonry Integrator SDKs

Welcome to the Falkonry Integrator API! You can use our API to create, ingest and extract information from various datastreams, assessments, and facts (ground truths). The API also allows the user to generate output for historical data, set/ get metadata for entities as well as toggle datastreams on/ off.

We have language bindings in Shell, Java, C# and Python! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.


# Authentication


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


## Create Datastream for narrow/historian style data from a single entity

> To create a datastream for narrow/historian style data from a single entity, use this code:

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);
    
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
  Signal.TagIdentifier = "tag";
  Signal.IsSignalPrefix = true;
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
  Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
  Datastream ds = new Datastream();
  ds.setName("Test-DS-" + Math.random());

  TimeObject time = new TimeObject();
  time.setIdentifier("time");
  time.setFormat("iso_8601");
  time.setZone("GMT");

  Signal signal = new Signal();
  signal.setTagIdentifier("tag");
  signal.setValueIdentifier("value");
  signal.setDelimiter("_");
  signal.setIsSignalPrefix(false);

  Datasource dataSource = new Datasource();
  dataSource.setType("STANDALONE");

  Field field = new Field();
  field.setSiganl(signal);
  field.setTime(time);

  ds.setDatasource(dataSource);
  ds.setField(field);

  Datastream datastream = falkonry.createDatastream(ds);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')
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
signal.set_delimiter(None)                                  # set delimiter to None 
signal.set_tagIdentifier("tag")                             # set tag identifier
signal.set_valueIdentifier("value")                         # set value identifier
signal.set_isSignalPrefix(False)                            # as this is single entity, set signal prefix flag to false
field.set_signal(signal)                                    # set signal in field
datasource.set_type("STANDALONE")                           # set datastource type in datastream
datastream.set_datasource(datasource)
datastream.set_field(field)
        
#create Datastream
createdDatastream = fclient.create_datastream(datastream)
```

```shell

```

Wide and narrow (sometimes un-stacked and stacked) are terms used to describe two different presentations for tabular data.
Here, we create a datastream and set it up with narrow style data for a single entity or element.

### Sample format for narrow/historian style

Person  | Variable | Value
------- |----------| -------
Bob     | Age      | 32
Bob     | Weight   |128
Alice   | Age      | 24
Alice   | Weight   | 86
Steve   | Age      | 64
Steve   | Weight   | 95

<aside class="warning">
Structuring data as key-value pairs — as is done in narrow-form datasets — facilitates conceptual clarity. 
</aside>

> Input data could be of the following format:

```json
 {"time" :"2016-03-01 01:01:01", "tag" : "signal1", "value" : 3.4}
 {"time" :"2016-03-01 01:01:02", "tag" : "signal2", "value" : 9.3}
 ```


## Setup datastream for narrow/historian style data from multiple entities

> To setup a datastream using narrow style data for multiple entities, use this code:

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;
    
string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);
    
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
  Signal.TagIdentifier = "tag";
  Signal.IsSignalPrefix = true;
  Signal.Delimiter = "_";
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
  Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");


  Datastream ds = new Datastream();
  ds.setName("Test-DS-" + Math.random());

  TimeObject time = new TimeObject();
  time.setIdentifier("time");
  time.setFormat("iso_8601");
  time.setZone("GMT");

  Signal signal = new Signal();
  signal.setTagIdentifier("tag");
  signal.setValueIdentifier("value");
  signal.setDelimiter("_");
  signal.setIsSignalPrefix(false);

  Datasource dataSource = new Datasource();
  dataSource.setType("STANDALONE");

  Field field = new Field();
  field.setSiganl(signal);
  field.setTime(time);

  ds.setDatasource(dataSource);
  ds.setField(field);

  Datastream datastream = falkonry.createDatastream(ds);
  

  //Add data to datastream
  String data = "{\"time\" : \"2016-03-01 01:01:01\", \"tag\" : \"signal1_entity1\", \"value\" : 3.4}" + "\n"
      + "{\"time\" : \"2016-03-01 01:01:01\", \"tag\" : \"signal2_entity1\", \"value\" : 1.4}" + "\n"
      + "{\"time\" : \"2016-03-01 01:01:02\", \"tag\" : \"signal1_entity1\", \"value\" : 9.3}" + "\n"
      + "{\"time\" : \"2016-03-01 01:01:02\", \"tag\" : \"signal2_entity2\", \"value\" : 4.3}";
  Map<String, String> options = new HashMap<String, String>();
  options.put("timeIdentifier", "time");
  options.put("timeFormat", "iso_8601");
  options.put("fileFormat", "csv");
  falkonry.addInput(datastream.getId(), data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')
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
signal.set_delimiter("_")                                   # set delimiter
signal.set_tagIdentifier("tag")                             # set tag identifier
signal.set_valueIdentifier("value")                         # set value identifier
signal.set_isSignalPrefix(True)                             # set signal prefix flag
field.set_signal(signal)                                    # set signal in field
datasource.set_type("STANDALONE")                           # set datastource type in datastream
datastream.set_datasource(datasource)
datastream.set_field(field)
        
#create Datastream
createdDatastream = fclient.create_datastream(datastream)
```

```shell

```

> Input data could be of the following format:

```json

  {"time" :"2016-03-01 01:01:01", "tag" : "signal1_entity1", "value" : 3.4}
  {"time" :"2016-03-01 01:01:01", "tag" : "signal2_entity1", "value" : 1.4}
  {"time" :"2016-03-01 01:01:02", "tag" : "signal1_entity2", "value" : 9.3}
  {"time" :"2016-03-01 01:01:02", "tag" : "signal2_entity2", "value" : 4.3}
```

Wide and narrow (sometimes un-stacked and stacked) are terms used to describe two different presentations for tabular data.
Here, we create a datastream and set it up with narrow style data for multiple entities or elements.


### Sample format for narrow/historian style

Person  | Variable | Value
------- |----------| -------
Bob     | Age      | 32
Bob     | Weight   |128
Alice   | Age      | 24
Alice   | Weight   | 86
Steve   | Age      | 64
Steve   | Weight   | 95

<aside class="warning">
Structuring data as key-value pairs — as is done in narrow-form datasets — facilitates conceptual clarity. 
</aside>


## Setup datastream for wide style data from a single entity

> To setup a datastream using wide style data for a single entity, use this code:

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);
    
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
    Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");

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
    

    //Add data to datastream
    String data = "{\"time\":1467729675422,\"signal1\":41.11,\"signal2\":82.34,\"signal3\":74.63,\"signal4\":4.8}" + "\n"
        + "{\"time\":1467729668919,\"signal1\":78.11,\"signal2\":2.33,\"signal3\":4.6,\"signal4\":9.8}";
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
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')
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

```

> Input data could be of the following format:

```json
{"time":1467729675422, "entities": "entity1", "signal1":41.11, "signal2":82.34, "signal3":74.63, "signal4":4.8}
{"time":1467729668919, "entities": "entity2", "signal1":78.11, "signal2":2.33, "signal3":4.6, "signal4":9.8}
```

Wide and narrow (sometimes un-stacked and stacked) are terms used to describe two different presentations for tabular data.
Here, we create a datastream and set it up with wide style data for a single entity or element.

### Sample format for wide style

Person  | Age | Weight
------- |----| -------
Bob | 32 | 128
Alice | 24 | 86
Steve | 64 | 95


<aside class="warning">
If you have many value variables, it is difficult to summarize wide-form datasets at a glance
</aside>


## Setup datastream for wide style data from multiple entities


> To setup a datastream using wide style data for multiple entities, use this code:

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);
    
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
    Field.EntityIdentifier = "Unit";
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
  Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");

  Datastream ds = new Datastream();
  ds.setName("Test-DS-" + Math.random());

  TimeObject time = new TimeObject();
  time.setIdentifier("time");
  time.setFormat("millis");
  time.setZone("GMT");

  Signal signal = new Signal();
  signal.setTagIdentifier("tag");
  signal.setValueIdentifier("value");
  signal.setDelimiter("_");
  signal.setIsSignalPrefix(false);

  Datasource dataSource = new Datasource();
  dataSource.setType("STANDALONE");

  Field field = new Field();
  field.setSiganl(signal);
  field.setTime(time);
  field.setEntityIdentifier("entities");

  ds.setDatasource(dataSource);
  ds.setField(field);

  Datastream datastream = falkonry.createDatastream(ds);


  //Add data to datastream
  String data = "time, entities, signal1, signal2, signal3, signal4" + "\n"
      + "1467729675422, entity1, 41.11, 62.34, 77.63, 4.8" + "\n"
      + "1467729675445, entity1, 43.91, 82.64, 73.63, 3.8";
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
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')
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
field.set_entityIdentifier("thing")                         # set entity identifier as "thing"
datasource.set_type("STANDALONE")                           # set datastource type in datastream
datastream.set_datasource(datasource)
datastream.set_field(field)
datastream.set_inputs(inputs)
        
#create Datastream
createdDatastream = falkonry.create_datastream(datastream)
```

```shell

```
> Input data could be of the following format:

```json
  {"time":1467729675422, "entities": "entity1", "signal1":41.11, "signal2":82.34, "signal3":74.63, "signal4":4.8}
  {"time":1467729668919, "entities": "entity2", "signal1":78.11, "signal2":2.33, "signal3":4.6, "signal4":9.8}
```

### Sample format for wide style

Person  | Age | Weight
------- |----| -------
Bob | 32 | 128
Alice | 24 | 86
Steve | 64 | 95

<aside class="warning">
If you have many value variables, it is difficult to summarize wide-form datasets at a glance
</aside>


## Retrieving all datastreams

> To retrieve a list of existing datastreams, use this code:

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);
  List<Datastream> datastreams = _falkonry.GetDatastreams();
```

```java
  import com.falkonry.client.Falkonry;
  
  Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");

  datastream = falkonry.getDatastreams();
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')
        
#return list of Datastreams
datastreams = falkonry.get_datastreams()
```

```shell

```


## Retrieve datastream by id

> To retrieve datastream by id, use this code:


```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);
  Datastream datastream = _falkonry.GetDatastream('datastreamId')

```

```java
  import com.falkonry.client.Falkonry;

  Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");

  datastream = falkonry.getDatastream("datastream_id"); //datastream's id
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'
        
#return sigle datastream
datastreams = falkonry.get_datastream(datastreamId)
```

```shell

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
      "tagIdentifier": "string",
      "delimiter": "string",
      "isSignalPrefix": true,
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


## Add historical input data (json) to datastream  

> To add historical input data in json format, use this code: 

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);

string data = "{\"time\" :\"2016-03-01 01:01:01\", \"current\" : 12.4, \"vibration\" : 3.4, \"state\" : \"On\"}";

SortedDictionary<string, string> options = new SortedDictionary<string, string>();
  options.Add("timeIdentifier", "time");
  options.Add("timeFormat", "iso_8601");
  options.Add("fileFormat", "json");
  options.Add("streaming", "false");
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
  Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
  //Add data to datastream
  String data = "{\"time\" : \"2016-03-01 01:01:01\", \"tag\" : \"signal1\", \"value\" : 3.4}" + "\n"
      + "{\"time\" : \"2016-03-01 01:01:02\", \"tag\" : \"signal2\", \"value\" : 9.3}";

  Map<String, String> options = new HashMap<String, String>();
    options.put("timeIdentifier", "time");
    options.put("timeFormat", "iso_8601");
    options.put("fileFormat", "csv");
    options.put("streaming", "false");
    options.put("hasMoreData", "false");
    falkonry.addInput('datastream-Id', data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
String data = "{\"time\" : \"2016-03-01 01:01:01\", \"tag\" : \"signal1_thing1\", \"value\" : 3.4}" + "\n"
        + "{\"time\" : \"2016-03-01 01:01:01\", \"tag\" : \"signal2_thing1\", \"value\" : 1.4}" + "\n"
        + "{\"time\" : \"2016-03-01 01:01:02\", \"tag\" : \"signal1_thing1\", \"value\" : 9.3}" + "\n"
        + "{\"time\" : \"2016-03-01 01:01:02\", \"tag\" : \"signal2_thing2\", \"value\" : 4.3}";
        
# set hasMoreData to True if data is sent in batches. When the last batch is getting sent then set  'hasMoreData' to False. For single batch upload it shpuld always be set to False
options = {'streaming': False, 'hasMoreData':False}   
inputResponse = falkonry.add_input_data(datastreamId, 'json', options, data)
```

```shell

```
> Input data could be of the following format:

```json
  {"time" :"2016-03-01 01:01:01", "tag" : "signal1_thing1", "value" : 3.4}
  {"time" :"2016-03-01 01:01:01", "tag" : "signal2_thing1", "value" : 1.4}
  {"time" :"2016-03-01 01:01:02", "tag" : "signal1_thing2", "value" : 9.3}
  {"time" :"2016-03-01 01:01:02", "tag" : "signal2_thing2", "value" : 4.3}
```

json data from a historian or time series data store can be imported into a Falkonry datastream

<aside class="success">
Falkonry needs historical time series data to build pattern recognition models.
</aside>


## Add historical input data (csv) to datastream 

> To add historical input data in csv format, use this code: 

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);

string data = "time, entities, signal1, signal2, signal3, signal4" + "\n"
      + "1467729675422, entity1, 41.11, 62.34, 77.63, 4.8" + "\n"
      + "1467729675445, entity1, 43.91, 82.64, 73.63, 3.8";

SortedDictionary<string, string> options = new SortedDictionary<string, string>();
  options.Add("timeIdentifier", "time");
  options.Add("timeFormat", "iso_8601");
  options.Add("fileFormat", "csv");
  options.Add("streaming", "false");
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
  Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
 //Add data to datastream
    String data = "time, entities, signal1, signal2, signal3, signal4" + "\n"
        + "1467729675422, entity1, 41.11, 62.34, 77.63, 4.8" + "\n"
        + "1467729675445, entity1, 43.91, 82.64, 73.63, 3.8";
    Map<String, String> options = new HashMap<String, String>();
      options.put("timeIdentifier", "time");
      options.put("timeFormat", "millis");
      options.put("fileFormat", "csv");
      options.put("streaming", "false");
      options.put("hasMoreData", "false");
      falkonry.addInput('datastream-Id', data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
String data = "time, tag, value " + "\n"
        + "2016-03-01 01:01:01, signal1_thing1, 3.4" + "\n"
        + "2016-03-01 01:01:01, signal2_thing1, 1.4";
        
# set hasMoreData to True if data is sent in batches. When the last batch is getting sent then set  'hasMoreData' to False. For single batch upload it shpuld always be set to False
options = {'streaming': False, 'hasMoreData':False}   
inputResponse = falkonry.add_input_data(datastreamId, 'csv', options, data)
```

```shell

```

> Input data could be of the following format:

```csv
  time, tag, value
  2016-03-01 01:01:01, signal1_thing1, 3.4
  2016-03-01 01:01:01, signal2_thing1, 1.4
```

csv data from a historian or time series data store can be imported into a Falkonry datastream

<aside class="success">
Falkonry needs historical time series data to build pattern recognition models.
</aside>


## Add historical input data (json) from a stream to datastream 

> To add historical input data in json format from a stream, use this code: 

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);

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

  Falkonry falkonry   = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
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
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')

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
  {"time" :"2016-03-01 01:01:01", "tag" : "signal1_thing1", "value" : 3.4}
  {"time" :"2016-03-01 01:01:01", "tag" : "signal2_thing1", "value" : 1.4}
  {"time" :"2016-03-01 01:01:02", "tag" : "signal1_thing2", "value" : 9.3}
  {"time" :"2016-03-01 01:01:02", "tag" : "signal2_thing2", "value" : 4.3}
```

json data from a historian or time series data store can be imported into a Falkonry datastream

<aside class="success">
Falkonry needs historical time series data to build pattern recognition models.
</aside>


## Add historical input data (csv) from a stream to datastream 

> To add historical input data in csv format from a stream to a datastream, use this code: 

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);

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

  Falkonry falkonry   = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
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
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')

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
  time, tag, value
  2016-03-01 01:01:01, signal1_thing1, 3.4
  2016-03-01 01:01:01, signal2_thing1, 1.4
```

csv data from a historian or time series data store can be imported into a Falkonry datastream

<aside class="success">
Falkonry needs historical time series data to build pattern recognition models.
</aside>


## Extract data from a datastream

It may help to extract all the data from an existing datastream for debug, editing, exporting to another datastream.


> To extract data from a datastream, use this code:

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);

//Creating a datastream to add data to later
  string name="data stream name here";
  var time = new Time();
  time.Zone = "GMT";
  time.Identifier = "time";
  time.Format = "iso_8601";
  Field field = new Field();
  Field.EntityIdentifier = "Unit";
  
Datasource datasource = new Datasource();
  datasource.type = "STANDALONE"; 

DatastreamRequest ds = new DatastreamRequest();
  ds.name = "datastream name here";
  field.Time = time;
  ds.Field = field;
    
ds.dataSource = datasource;
Datastream datastream = falkonry.createDatastream(ds);
    
/*This particular example will read data from a AddData.json file in debug folder in bin*/
    
  SortedDictionary<string, string> options = new SortedDictionary<string, string>();
    options.Add("timeIdentifier", "time");
    options.Add("timeFormat", "iso_8601");
    
    string folder_path = Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location);

    string path = folder_path + "/AddData.json";

//Alternatively, you can directly specify the folder path in the "folder_path" variable

    byte[] bytes = System.IO.File.ReadAllBytes(path);

    InputStatus inputstatus = falkonry.addInputStream(datastream.id, bytes, options);

//The updated datastream
  datastream = falkonry.getDatastream(datastream.id);
```

```java

```

```python

```

```shell

```

## Delete datastream

> To delete a datastream using its id, use this code:

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);
falkonry.DeleteAssessment('assessment-id');
```


```java
  import com.falkonry.client.Falkonry;

  Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");

  Assessment assesssment = falkonry.deleteAssessment('assessment_id');
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'
        
falkonry.delete_datastream(datastreamId)
```

```shell

```

<aside class="notice">
Sometimes good things come to an end as well :(!
</aside>


## Add EntityMeta to a datastream

> To add EntityMeta to a datastream, use this code:

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);

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
    
  Falkonry falkonry   = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
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
falkonry = Falkonry('https://sandbox.falkonry.ai', 'auth-token')
data = [{"sourceId": "testId","label": "testName","path": "root/path"}]
datastreamId = 'id of the datastream'

entityMetaResponse = fclient.add_entity_meta(datastreamId, {}, data)
```

```shell

```

> Input data could be of the following format:

```json
[{"sourceId": "testId","label": "testName","path": "root/path"}]
```

EntityMeta or metadata associated with assets/ elements can be injected into an existing datastream as long as they have a similar schema.


## Get EntityMeta of a datastream

> To retrieve EntityMeta from a datastream, use this code:

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);
  // Get entitymeta
  entityMetaResponseList = falkonry.getEntityMeta('datastream-id');
```


```java
import com.falkonry.client.Falkonry;
    
  Falkonry falkonry   = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
    List<EntityMeta> entityMetas = falkonry.getEntityMeta("datastream_id"); //datastream's id
```

```python
import os, sys
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry = Falkonry('https://sandbox.falkonry.ai', 'auth-token')
datastreamId = 'id of the datastream'

entityMetaResponse = fclient.get_entity_meta(datastreamId)
```

```shell

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
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);

AssessmentRequest asmt = new AssessmentRequest();
  asmt.name = "assessment name here";
  asmt.datastream= "datastream id";
  Assessment assessment = falkonry.createAssessment(asmt);
```


```java
  import com.falkonry.client.Falkonry;
  import com.falkonry.helper.models.*;

  //instantiate Falkonry
  Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");

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
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

asmtRequest = Schemas.AssessmentRequest()
asmtRequest.set_name('Assessment Name'))         # Set new assessment name
asmtRequest.set_datastream(response.get_id())    # Set datatsream id
asmtRequest.set_rate('PT0S')                     # Set assessment rate

# create new assessment
assessmentResponse = fclient.create_assessment(asmtRequest)
```

```shell

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
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);
  List<Assessment> assessmentList = falkonry.getAssessments();
```


```java
  import com.falkonry.client.Falkonry;

  Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");

  List<Assessment> datastreams = falkonry.getAssessments();

```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')

assessmentResponse = fclient.get_assessments()
```

```shell

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

> To retrieve a particular assessment bu id, use this code:

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);
  Assessment assessment = falkonry.getAssessment('assessment-id');
```


```java
  import com.falkonry.client.Falkonry;

  Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");

  Assessment assesssment = falkonry.getAssessment('assessment_id');
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')

assessmentId = 'id of the datastream'
assessmentResponse = fclient.get_assessment(assessmentId)
```

```shell

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
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);
falkonry.DeleteAssessment('assessment-id');
```


```java
  import com.falkonry.client.Falkonry;

  Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");

  Assessment assesssment = falkonry.deleteAssessment('assessment_id');
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')

assessmentId = 'id of the datastream'
assessmentResponse = fclient.delete_assessment(assessmentId)
```

```shell

```

## Get Historian Output from Assessment

> To retrieve historical output generated by an assessment from the historian, use this code: 

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);

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
  options.Add("tarckerId", __id);
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

falkonry  = Falkonry('https://sandbox.falkonry.ai', 'auth-token')
assessmentId = 'id of the datastream'

options = {'startTime':'2011-01-01T01:00:00.000Z','endTime':'2011-06-01T01:00:00.000Z','responseFormat':'application/json'}

response = fclient.get_historical_output(assessment, options)

'''If data is not readily available then, a tracker id will be sent with 202 status code. While falkonry will genrate ouptut data
 Client should do timely pooling on the using same method, sending tracker id (__id) in the query params
 Once data is available server will response with 200 status code and data in json/csv format.'''

if response.status_code is 202:
    trackerResponse = Schemas.Tracker(tracker=response._content)
    #get id from the tracker
    trackerId = trackerResponse.get_id()
    #use this tracker for checking the status of the process.
    options = {"tarckerId": trackerId, "responseFormat":"application/json"}
    newResponse = fclient.get_historical_output(assessment, options)
    '''if status is 202 call the same request again
    if status is 200, output data will be present in httpResponse.response field'''
```

```shell

```

# Facts

A fact is a known condition for a particular episode of time. Facts can come from external sources, inspection reports, or investigations. Facts data typically becomes available after events have passed.

Facts are associated with assessments.

Facts can be added from inspection logs, failure reports, user/ expert observations, event frames within a historian, etc 

## Add facts data (json format) to Assessment of single entity datastream

> To add more facts to your assessment, use this code:

```csharp
    using falkonry_csharp_client;
    using falkonry_csharp_client.helper.models;

    SortedDictionary<string, string> options = new SortedDictionary<string, string>();
    options.Add("startTimeIdentifier", "time");
    options.Add("endTimeIdentifier", "end");
    options.Add("timeFormat", "iso_8601");
    options.Add("timeZone", "GMT");
    options.Add("valueIdentifier", "Health");

    string token = "Add your token here";   
    SortedDictionary<string, string> options = new SortedDictionary<string, string>();
    Falkonry falkonry = new Falkonry("http://localhost:8080", token);
    String data = "{\"time\" : \"2011-03-26T12:00:00Z\", \"end\" : \"2012-06-01T00:00:00Z\", \"Health\" : \"Normal\"}";
    string response = falkonry.addFacts('assessment-id',data, options);
```


```java
  import com.falkonry.client.Falkonry;

  Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
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
falkonry      = Falkonry('http://localhost:8080', 'auth-token')
assessmentId = 'id of the assessment'
data          = '{"time" : "2011-03-26T12:00:00.000Z", "end" : "2012-06-01T00:00:00.000Z", "Health" : "Normal"}'

options = {
        'startTimeIdentifier': "time",
        'endTimeIdentifier': "end",
        'timeFormat': "iso_8601",
        'timeZone': time.get_zone(),
        'valueIdentifier': "Health"
    }
inputResponse = falkonry.add_facts(assessmentId, 'json', options, data)
```

```shell

```
> The above command returns JSON structured like this:

```json
{
  "schema": [
    {
      "id": "string",
      "name": "string",
      "valueType": "string",
      "dimensionList": [
        "string"
      ],
      "tags": [
        "string"
      ]
    }
  ]
}
```


## Add facts data (json format) with addition tag to Assessment of multi entity datastream

```csharp
    using falkonry_csharp_client;
    using falkonry_csharp_client.helper.models;

    SortedDictionary<string, string> options = new SortedDictionary<string, string>();
    options.Add("startTimeIdentifier", "time");
    options.Add("endTimeIdentifier", "end");
    options.Add("timeFormat", "iso_8601");
    options.Add("timeZone", "GMT");
    options.Add("entityIdentifier", "entities");
    options.Add("valueIdentifier", "Health");
    options.Add("additionalTag", "testTag");

    string token = "Add your token here";   
    SortedDictionary<string, string> options = new SortedDictionary<string, string>();
    Falkonry falkonry = new Falkonry("http://localhost:8080", token);
    String data = "{\"time\" : \"2011-03-26T12:00:00Z\", \"entities\" : \"entity1\", \"end\" : \"2012-06-01T00:00:00Z\", \"Health\" : \"Normal\"}";
    string response = falkonry.addFacts('assessment-id',data, options);
```

```java
  import com.falkonry.client.Falkonry;

  Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
  Map<String, String> options = new HashMap<String, String>();
  options.put("startTimeIdentifier", "time");
  options.put("endTimeIdentifier", "end");
  options.put("timeFormat", "iso_8601");
  options.put("timeZone", "GMT");
  options.put("valueIdentifier", "Health"); 
  options.put("entityIdentifier", "entities");
  options.put("additionalTag", "testTag");

  String data = "{\"time\" : \"2011-03-26T12:00:00.000Z\", \"entities\" : \"entity1\", \"end\" : \"2012-06-01T00:00:00.000Z\", \"Health\" : \"Normal\"}";
  String response = falkonry.addfacts(assessment.getId(),data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry      = Falkonry('http://localhost:8080', 'auth-token')
assessmentId = 'id of the assessment'
data          = '{"time" : "2011-03-26T12:00:00.000Z", "end" : "2012-06-01T00:00:00.000Z", "entities": "entity1", "Health" : "Normal"}'

options = {
        'startTimeIdentifier': "time",
        'endTimeIdentifier': "end",
        'timeFormat': "iso_8601",
        'timeZone': time.get_zone(),
        'valueIdentifier': "Health",
        'entityIdentifier': 'entities',
        'additionalTag': 'testTag'
    }
inputResponse = falkonry.add_facts(assessmentId, 'json', options, data)
```


## Add facts data (csv format) to Assessment of single entity datastream

```csharp
    using falkonry_csharp_client;
    using falkonry_csharp_client.helper.models;

    SortedDictionary<string, string> options = new SortedDictionary<string, string>();
    options.Add("startTimeIdentifier", "time");
    options.Add("endTimeIdentifier", "end");
    options.Add("timeFormat", "iso_8601");
    options.Add("timeZone", "GMT");
    options.Add("valueIdentifier", "Health");

    string token = "Add your token here";   
    SortedDictionary<string, string> options = new SortedDictionary<string, string>();
    Falkonry falkonry = new Falkonry("http://localhost:8080", token);
    string data = "time,end,Health\n2011-03-31T00:00:00Z,2011-04-01T00:00:00Z,Normal\n2011-03-31T00:00:00Z,2011-04-01T00:00:00Z,Normal";
    string response = falkonry.addFacts('assessment-id',data, options);

```

```java
  import com.falkonry.client.Falkonry;

  Falkonry falkonry = new Falkonry("http://localhost:8080", "auth-token");
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
falkonry      = Falkonry('http://localhost:8080', 'auth-token')
assessmentId = 'id of the assessment'
data          = 'time,end,Health' + "\n"
                 + '2011-03-26T12:00:00.000Z,2012-06-01T00:00:00.000Z,Normal' + "\n"
                 + '2014-02-10T23:00:00.000Z,2014-03-20T12:00:00.000Z,Spalling';

options = {
        'startTimeIdentifier': "time",
        'endTimeIdentifier': "end",
        'timeFormat': "iso_8601",
        'timeZone': time.get_zone(),
        'valueIdentifier': "Health"
    }

inputResponse = falkonry.add_facts(assessmentId, 'csv', options, data)
```

```shell

```


#### Add facts data (csv format) with tags Assessment of multi entity datastream

```csharp
    using falkonry_csharp_client;
    using falkonry_csharp_client.helper.models;

    SortedDictionary<string, string> options = new SortedDictionary<string, string>();
    options.Add("startTimeIdentifier", "time");
    options.Add("endTimeIdentifier", "end");
    options.Add("timeFormat", "iso_8601");
    options.Add("timeZone", "GMT");
    options.Add("entityIdentifier", "car");
    options.Add("valueIdentifier", "Health");
    options.Add("tagIdentifier", "Tag");

    string token = "Add your token here";   
    SortedDictionary<string, string> options = new SortedDictionary<string, string>();
    Falkonry falkonry = new Falkonry("http://localhost:8080", token);
    string data = "time,end,car,Health,Tag\n2011-03-31T00:00:00Z,2011-04-01T00:00:00Z,IL9753,Normal\n2011-03-31T00:00:00Z,2011-04-01T00:00:00Z,HI3821,Normal,testTag1";
    string response = falkonry.addFacts('assessment-id',data, options);

```

```java
  import com.falkonry.client.Falkonry;

  Falkonry falkonry = new Falkonry("http://localhost:8080", "auth-token");
  Map<String, String> options = new HashMap<String, String>();
  options.put("startTimeIdentifier", "time");
  options.put("endTimeIdentifier", "end");
  options.put("timeFormat", "iso_8601");
  options.put("timeZone", "GMT");
  options.put("entityIdentifier", "car");
  options.put("valueIdentifier", "Health");
  options.put("tagIdentifier", "Tag");

  String data = "time,end,car,Health,Tag\n2011-03-31T00:00:00.000Z,2011-04-01T00:00:00.000Z,IL9753,Normal,testTag1\n2011-03-31T00:00:00.000Z,2011-04-01T00:00:00.000Z,HI3821,Normal,testTag2";
  String response = falkonry.addFacts(assessment.getId(),data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry      = Falkonry('http://localhost:8080', 'auth-token')
assessmentId = 'id of the assessment'
data          = 'time,car,end,Health,Tag' + "\n"
                 + '2011-03-26T12:00:00.000Z,HI3821,2012-06-01T00:00:00.000Z,Normal,testTag1' + "\n"
                 + '2014-02-10T23:00:00.000Z,HI3821,2014-03-20T12:00:00.000Z,Spalling,testTag2';

options = {
        'startTimeIdentifier': "time",
        'endTimeIdentifier': "end",
        'timeFormat': "iso_8601",
        'timeZone': time.get_zone(),
        'entityIdentifier': "car",
        'valueIdentifier': "Health",
        'tagIdentifier': 'Tag'
    }

inputResponse = falkonry.add_facts(assessmentId, 'csv', options, data)
```

```shell

```


> The above command returns JSON structured like this:

```json
{
  "schema": [
    {
      "id": "string",
      "name": "string",
      "valueType": "string",
      "dimensionList": [
        "string"
      ],
      "tags": [
        "string"
      ]
    }
  ]
}
```

Often, facts and machine/ asset state are captured in spreadsheets. 

Use this to inject csv format facts into an assessment.

<aside class="notice">
Adding more facts (ground truths) helps improve model accuracy and hence the usefulness of an assessment.
</aside>

## Add facts data (json format) from a stream to Assessment of multi entity datastream

Sample JSONFile:
{"time" : "2011-03-26T12:00:00.000Z", "car" : "HI3821", "end" : "2012-06-01T00:00:00.000Z", "Health" : "Normal"}
{"time" : "2014-02-10T23:00:00.000Z", "car" : "HI3821", "end" : "2014-03-20T12:00:00.000Z", "Health" : "Spalling"}


> To add facts in json format from an input stream, use this code:

```csharp
    using falkonry_csharp_client;
    using falkonry_csharp_client.helper.models;

    SortedDictionary<string, string> options = new SortedDictionary<string, string>();
    options.Add("startTimeIdentifier", "time");
    options.Add("endTimeIdentifier", "end");
    options.Add("timeFormat", "iso_8601");
    options.Add("timeZone", "GMT");
    options.Add("entityIdentifier", "car");
    options.Add("valueIdentifier", "Health");

    string token="Add your token here";   
    Falkonry falkonry = new Falkonry("http://localhost:8080", token);
    string path = "Insert the path to your file here";
    byte[] bytes = System.IO.File.ReadAllBytes(path);
    string response = falkonry.AddFactsStream('assessment-id',bytes, options);
```


```java
    import com.falkonry.client.Falkonry;
    import org.apache.commons.io.FileUtils;

    Falkonry falkonry   = new Falkonry("http://localhost:8080", "auth-token");
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

    falkonry = Falkonry('http://localhost:8080', 'auth-token')
    assessmentId = 'id of the assessment'
    stream   = io.open('./factsData.json')

    options = {
            'startTimeIdentifier': "time",
            'endTimeIdentifier': "end",
            'timeFormat': "iso_8601",
            'timeZone': time.get_zone(),
            'entityIdentifier': "car",
            'valueIdentifier': "Health"
        }

    response = falkonry.add_facts_stream(assessmentId, 'json', options, stream)
```

```shell

```
> The above command returns JSON structured like this:

```json
{
  "schema": [
    {
      "id": "string",
      "name": "string",
      "valueType": "string",
      "dimensionList": [
        "string"
      ],
      "tags": [
        "string"
      ]
    }
  ]
}
```

If facts are created from another application, they are often exported in json format.

<aside class="notice">
Adding more facts (ground truths) helps improve model accuracy and hence the usefulness of an assessment.
</aside>

## Add facts data (csv format) from a stream to  Assessment of multi entity datastream

> To add facts in csv format from an input stream, use this code:

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

SortedDictionary<string, string> options = new SortedDictionary<string, string>();
options.Add("startTimeIdentifier", "time");
options.Add("endTimeIdentifier", "end");
options.Add("timeFormat", "iso_8601");
options.Add("timeZone", "GMT");
options.Add("entityIdentifier", "car");
options.Add("valueIdentifier", "Health");

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);
string path = "Insert the path to your file here";
byte[] bytes = System.IO.File.ReadAllBytes(path);
string response = falkonry.AddFactsStream('assessment-id',bytes, options);
```


```java
  import com.falkonry.client.Falkonry;
  import org.apache.commons.io.FileUtils;

  Falkonry falkonry   = new Falkonry("http://localhost:8080", "auth-token");
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

falkonry = Falkonry('http://localhost:8080', 'auth-token')
assessmentId = 'id of the assessment'
stream   = io.open('./factsData.csv')

options = {
        'startTimeIdentifier': "time",
        'endTimeIdentifier': "end",
        'timeFormat': "iso_8601",
        'timeZone': time.get_zone(),
        'entityIdentifier': "car",
        'valueIdentifier': "Health"
    }

response = falkonry.add_facts_stream(assessmentId, 'csv', options, stream)

```

```shell

```

> The above command returns JSON structured like this:

```json
{
  "schema": [
    {
      "id": "string",
      "name": "string",
      "valueType": "string",
      "dimensionList": [
        "string"
      ],
      "tags": [
        "string"
      ]
    }
  ]
}
```

Often, facts and machine/ asset state are captured in spreadsheets. 

Use this to inject csv format facts into an assessment.

<aside class="notice">
Adding more facts (ground truths) helps improve model accuracy and hence the usefulness of an assessment.
</aside>aside>


# Live Monitoring

After a model revision has been trained, a user may want to switch to live monitoring to evaluate patterns in real-time.

Any model revision can be selected to be the basis of live monitoring. Falkonry will assign conditions to multi-variate patterns based on the signals that were used to create the model revision.


## Start live monitoring of datastream

> To turn live monitoring on for a datastream, use this code:

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);
  string datastream_id = "Your datastream id";
  falkonry.onDatastream(datastream_id);
```

```java
  import com.falkonry.client.Falkonry;

  Falkonry falkonry   = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
  String datastreamId = "hk7cgt56r3yln0";
  List<Assessment> liveAssessments = falkonry.onDatastream(datastreamId);
```

```python
import os, sys
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

falkonry  = Falkonry('https://sandbox.falkonry.ai', 'auth-token')
datastreamId = 'id of the datastream'

# Starts live monitoring of datastream. For live monitoring datastream must have atleast one assessment with active model. 
response = falkonry.on_datastream(datastreamId)
```

```shell

```

Live monitoring is associated with the entire datastream. At this point each assessment assciated with the datastream goes into live monitoring.

Every assessment has an "active" model associated with it.

<aside class="notice">
Live monitoring can be turned "on" from the Falkonry Professional UI but you can do the same from any of these development kits.
</aside>aside>

## Stop live monitoring of datastream

> To turn live monitoring off for a datastream, use this code:

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);
  string datastream_id = "Your datastream id";
  falkonry.offDatastream(datastream_id);
```


```java
  import com.falkonry.client.Falkonry;

  Falkonry falkonry   = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
  String datastreamId = "hk7cgt56r3yln0";
  List<Assessment> assessments = falkonry.offDatastream(datastreamId);
```

```python
import os, sys
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

falkonry  = Falkonry('https://sandbox.falkonry.ai', 'auth-token')
datastreamId = 'id of the datastream'

# Stops live monitoring of datastream.
response = falkonry.off_datastream(datastreamId)
```

```shell

```

<aside class="notice">
Live monitoring can be turned "on" from the Falkonry Professional UI but you can do the same from any of these development kits.
</aside>aside>


## Add live input data (json) to datastream

> To inject live input data in json format into the datastream, use this code:


```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);

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
  Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
  //Add data to datastream
  String data = "{\"time\" : \"2016-03-01 01:01:01\", \"tag\" : \"signal1\", \"value\" : 3.4}" + "\n"
      + "{\"time\" : \"2016-03-01 01:01:02\", \"tag\" : \"signal2\", \"value\" : 9.3}";

  Map<String, String> options = new HashMap<String, String>();
    options.put("timeIdentifier", "time");
    options.put("timeFormat", "iso_8601");
    options.put("fileFormat", "csv");
    options.put("streaming", "true");
    options.put("hasMoreData", "false");
    falkonry.addInput('datastream-Id', data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
String data = "{\"time\" : \"2016-03-01 01:01:01\", \"tag\" : \"signal1_thing1\", \"value\" : 3.4}" + "\n"
        + "{\"time\" : \"2016-03-01 01:01:01\", \"tag\" : \"signal2_thing1\", \"value\" : 1.4}" + "\n"
        + "{\"time\" : \"2016-03-01 01:01:02\", \"tag\" : \"signal1_thing1\", \"value\" : 9.3}" + "\n"
        + "{\"time\" : \"2016-03-01 01:01:02\", \"tag\" : \"signal2_thing2\", \"value\" : 4.3}";
        
options = {'streaming': True}   
inputResponse = falkonry.add_input_data(datastreamId, 'json', options, data)
```

```shell

```
When in live monitoring, the assessment is being computed based on live data that is streaming in, but based on the model that was built using historical data.

<aside class="warning">
When working from within the Falkonry Professional UI, make sure "Accepting Data" in turned on before starting "Live Monitoring"
</aside>

## Add live data (csv) to datastream

> To inject live input data in csv format into the datastream, use this code:

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);

string data = "time, entities, signal1, signal2, signal3, signal4" + "\n"
      + "1467729675422, entity1, 41.11, 62.34, 77.63, 4.8" + "\n"
      + "1467729675445, entity1, 43.91, 82.64, 73.63, 3.8";

SortedDictionary<string, string> options = new SortedDictionary<string, string>();
  options.Add("timeIdentifier", "time");
  options.Add("timeFormat", "iso_8601");
  options.Add("fileFormat", "csv");
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
Falkonry falkonry = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
//Add data to datastream
  String data = "time, entities, signal1, signal2, signal3, signal4" + "\n"
      + "1467729675422, entity1, 41.11, 62.34, 77.63, 4.8" + "\n"
      + "1467729675445, entity1, 43.91, 82.64, 73.63, 3.8";
  Map<String, String> options = new HashMap<String, String>();
    options.put("timeIdentifier", "time");
    options.put("timeFormat", "millis");
    options.put("fileFormat", "csv");
    options.put("streaming", "true");
    options.put("hasMoreData", "false");
    falkonry.addInput('datastream-Id', data, options);
```

```python
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

#instantiate Falkonry
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')

datastreamId = 'id of the datastream'

#add data to Datastream
String data = "time, tag, value " + "\n"
        + "2016-03-01 01:01:01, signal1_thing1, 3.4" + "\n"
        + "2016-03-01 01:01:01, signal2_thing1, 1.4";
        
options = {'streaming': True}   
inputResponse = falkonry.add_input_data(datastreamId, 'csv', options, data)
```

```shell

```

When in live monitoring, the assessment is being computed based on live data that is streaming in, but based on the model that was built using historical data.

<aside class="warning">
When working from within the Falkonry Professional UI, make sure "Accepting Data" in turned on before starting "Live Monitoring"
</aside>


## Add live data (json) from stream to datastream 

> To inject live input stream data in json format into the datastream, use this code:

```csharp
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);

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

  Falkonry falkonry   = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
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
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')

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
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);

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

  Falkonry falkonry   = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
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
falkonry   = Falkonry('https://sandbox.falkonry.ai', 'auth-token')

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
using falkonry_csharp_client;
using falkonry_csharp_client.helper.models;

string token="Add your token here";   
Falkonry falkonry = new Falkonry("http://localhost:8080", token);

string assessment_id ="assessment ID here";
System.IO.Stream streamrecieved = falkonry.getOutput(assessment_id, null, null);
//The folder path below by default is debug in bin.
string folder_path = System.IO.Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location);
//Alternatively, you can also specify the path of the folder in thr folder_path variable
//The outflow will get saved in an outflow.txt file there
string path = folder_path + "/outflow.txt";

System.IO.StreamReader streamreader = new System.IO.StreamReader(streamrecieved);
System.IO.StreamWriter streamwriter = new System.IO.StreamWriter(path);
  string line;
    using (streamwriter)
    {
        while ((line = streamreader.ReadLine()) != null)
        {
            streamwriter.WriteLine(line);
        }
    }
```


```java
import com.falkonry.client.Falkonry;

  Falkonry falkonry   = new Falkonry("https://sandbox.falkonry.ai", "auth-token");
    String assessment = "wpyred1glh6c5r";
    BufferedReader outputBuffer;
    outputBuffer = falkonry.getOutput(assessment);
```

```python
import os, sys
from falkonryclient import client as Falkonry
from falkonryclient import schemas as Schemas

falkonry  = Falkonry('https://sandbox.falkonry.ai', 'auth-token')
assessmentId = 'id of the datastream'
stream    = falkonry.get_output(assessmentId)
for event in stream.events():
    print(json.dumps(json.loads(event.data)))
```

```shell

```
Assessments in live monitoring mode can be streamed out to suggest real time machine/ asset condition/ state.






<!--  -->