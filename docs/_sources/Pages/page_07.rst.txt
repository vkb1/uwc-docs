=========================
7.0	UWC Modbus Operations
=========================
This section provides configurations required to read and write data from sensors and actuators connected over Modbus TCP or RTU on UWC gateway.
An application can perform following operations using UWC containers:

    •	Data Polling
    •	On-Demand Write
    •	On-Demand Read

Following section explains how to use MQTT topics to perform above operations. Further these operations can be performed in realtime (RT) and non-realtime (Non-RT) mode.
Multiple modules are involved in processing the operation. To capture the time taken by each module (i.e., a step), epoch timestamps in microseconds are added at various levels. These timestamps are present in JSON message. 

**The table of terms here is useful for interpreting the JSON payloads of the messages.**

.. figure:: Doc_Images/table10_1_update.png
    :scale: 70 %
    :align: center

.. figure:: Doc_Images/table10_2_update.png
    :scale: 70 %
    :align: center

.. figure:: Doc_Images/table10_3_update.png
    :scale: 70 %
    :align: center

.. figure:: Doc_Images/table10_4_update.png
    :scale: 70 %
    :align: center

--------------------
7.1 	Data Polling
--------------------

In the datapoint YML configuration file, a polling frequency is configured. As per polling frequency, data is fetched from the end point and published on MQTT by the UWC container. This section describes how to read the data for polled points using MQTT.

The data actions which are “Polling” actions are initiated by the Protocol container (in this case the Modbus protocol application (i.e., the driver) within the Modbus container. 

To receive polled data: Application should use a topic in following format to receive (i.e., subscribe) polling data from MQTT:

**MQTT topic to receive (i.e., subscribe) write response:**
     **/device/wellhead/point/update**

Please refer to the table in section 6 for details of fields.


.. code-block:: javascript

**Example:**

**Polling Topic**: /flowmeter/PL0/D3/update

**Polling Message**: Success Response 

{ 

    "driver_seq":	"1153204567290051305",

    "timestamp":	"2020-05-01T06:40:25",
    
    "version":	"2.0",

    "realtime":	"1",

    "data_topic":	/flowmeter/PL0/D3/update",

    "wellhead":	"PL0",

    "metric":	"D3",

    "tsPollingTime":	"1588315225331550",

    "reqRcvdInStack":	"1588315225331890",

    "reqSentByStack":	"1588315225333180",

    "respRcvdByStack":	"1588315225333900",

    "respPostedByStack":	"1588315225333950",

    "status":	"Good",

    "value":	"0x01",

    “datatype”:”boolean”

    "scaledValue":	true,

    "usec":	"1588315225334040",

    "tsMsgRcvdForProcessing":	"1588315225335060",

    "tsMsgReadyForPublish":	"1588315225335420"

} 


**Polling Message**: Error Response

{
    "driver_seq":	"1153204567290051305",

    "timestamp":	"2020-05-01T06:40:25",

    "version":	"2.0",

    "realtime":	"1",

    "data_topic":	/flowmeter/PL0/D3/update",

    "wellhead":	"PL0",

    "metric":	"D3",

    "tsPollingTime":	"1588315225331550",

    "reqRcvdInStack":	"1588315225331890",

    "reqSentByStack":	"1588315225333180",

    "respRcvdByStack":	"0",

    "respPostedByStack":	"1588315225333950",

    "status" : "Bad",

    "error_code" : "2003",

    "lastGoodUsec" : "1588315225333897",

    "value" : "0x01",

    “datatype”:”boolean”

    "scaledValue":	true,

    "usec" : "1588315225334040

    "tsMsgRcvdForProcessing" : "1588315225335060",

    "tsMsgReadyForPublish" : "1588315225335420"

}

-----------------------
7.2 	On-Demand Write
-----------------------

This section describes how to write data to some specific Modbus point using MQTT.

To send request: Application should use a topic in the following format to send (i.e., publish) write request on MQTT:

**MQTT topic to send (i.e., publish) write request: /device/wellhead/point/write**



To receive response: Application should use a topic in the following format to receive (i.e., subscribe) response of write request from MQTT:

**MQTT topic to receive (i.e., subscribe) write response: /device/wellhead/point/writeResponse**

Please refer to the table in section 6 for details of fields.

**Example:**

**Request Topic**: /flowmeter/PL0/Flow/write 

**Request Message:**

{"wellhead":"PL0","command":"Flow","value":"0x00","timestamp":"2019-09-20 12:34:56","usec":"1571887474111145","version":"2.0","app_seq":"1234"}

A message without *“realtime”* field is treated as a non-realtime message. To execute a message in realtime way, a field called *“realtime”* should be added as shown below:

{"wellhead":"PL0","command":"Flow","value":"0x00","timestamp":"2019-09-20 12:34:56","usec":"1571887474111145","version":"2.0","app_seq":"1234",”realtime”:”1”}

A message with *“value”* is treated as On-Demand Write from vendor App.

{"wellhead" : "PL0","command" : "INT16_MF10","timestamp" : "2019-09-20 12:34:56",
"usec" : "1571887474111145","version" : "2.0","realtime" : "0","app_seq" : "1234",
"scaledValue" : 12}

A message with *“scaledValue”* is treated as On-Demand Write from Ignition system.

The *“value”* / *"scaledValue"* field represents value to be written to the end device as a part of on-demand write operation.  

**Response Topic:** /flowmeter/PL0/Flow/writeResponse 

**Response Message:** Success Response 

{

    "app_seq":    "1234",

    "version":    "2.0",

    "realtime":    "0",

    "data_topic":    "/flowmeter/PL0/Time0/writeResponse",

    "wellhead":    "PL0",

    "metric":    "Time0",

    "tsMsgRcvdFromMQTT":    "1585660044014345",

    "tsMsgPublishOnEII":    "1585660044017877",

    "reqRcvdByApp":    "1585660044021380",

    "reqRcvdInStack":    "1585660044023582",
    
    "reqSentByStack":    "1585660044025671",

    "respRcvdByStack":    "1585660044025636",
    
    "respPostedByStack":    "1585660044025712",

    "status":    "Good",

    "usec":    "1585660044026131",
    
    "tsMsgRcvdForProcessing":    "1585660044027184",

    "tsMsgReadyForPublish":    "1585660045024371"

}


**Response Message**: Error Response 

{

    "app_seq" : "1234",

    "timestamp" : "2020-04-24 06:10:30",

    "version" : "2.0",

    "realtime" : "1",

    "data_topic" : "/flowmeter/PL0/Flow/writeResponse",

    "wellhead" : "PL0",

    "metric" : "Flow",

    "tsMsgRcvdFromMQTT" : "1587708630464551",

    "tsMsgPublishOnEII" : "1587708630465375",

    "reqRcvdByApp" : "1587708630465988",

    "reqRcvdInStack" : "1587708630466345",

    "reqSentByStack" : "0",

    "respRcvdByStack" : "0",

    "respPostedByStack" : "1587708630466627",

    "status" : "Bad",

    "error_code" : "2003",

    "usec" : "1587708630466935",

    "tsMsgRcvdForProcessing" : "1587708630467797",

    "tsMsgReadyForPublish" : "1587708630468239"

}

**Response Message:** Error Response for Invalid request JSON

{

    "app_seq":"1234",

    "timestamp":"2020-05-13 06:43:50",

    "version":"2.0",

    "realtime":"0",

    "topic":"/iou/PL0/D1/writeResponse",
    
    "wellhead":"PL0",

    "metric":"D1",

    "tsMsgRcvdFromMQTT":"1589352230212884",

    "tsMsgPublishOnEII":"1589352230214516",

    "reqRcvdByApp":"1589352230215485",

    "reqRcvdInStack":"0",

    "reqSentByStack":"0",

    "respRcvdByStack":"0",

    "respPostedByStack":"0",
    
    "status":"Bad",

    "usec":"1589352230217118",

    "error_code":"102",

    "tsMsgRcvdForProcessing":"1589352230220450",

    "tsMsgReadyForPublish":"1589352230220503"

}

----------------------
7.3 	On-Demand Read
----------------------

This section describes how to read data from some specific Modbus points using MQTT.

To send request: Application should use a topic in the following format to send (i.e., publish) read request on MQTT:

**MQTT topic to send (i.e. publish) read request: /device/wellhead/point/read**

To receive response: Application should use a topic in the following format to receive (i.e., subscribe) response of read request from MQTT:

**MQTT topic to receive (i.e., subscribe) write response:**
     **/device/wellhead/point/readResponse**
     
Please refer to the table in section 6 for details of fields.

**Example:**

**Request Topic: /flowmeter/PL0/Flow/read**

**Request Message:** 

    {"wellhead":"PL0","command":"Flow","timestamp":"2019-09-20 12:34:56","usec":"1571887474111145","version":"2.0","app_seq":"1234"}

A message without “realtime” field is treated as a non-realtime message. To execute a message in realtime way, a field called “realtime” should eb added as shown below:

    {"wellhead":"PL0","command":"Flow","timestamp":"2019-09-20 12:34:56","usec":"1571887474111145","version":"2.0","app_seq":"1234",”realtime”:”1”}

**Response Topic:** /flowmeter/PL0/Flow/readResponse 


**Response Message:** Success Response

{

    "app_seq":"1234",

    "timestamp":"2020-04-24 05:24:02",
    
    "version":"2.0",

    "realtime":"0",

    "data_topic":"/flowmeter/PL0/Flow/readResponse",
    
    "wellhead":"PL0",

    "metric":"Flow",

    "tsMsgRcvdFromMQTT":"1587705842296135",

    "tsMsgPublishOnEII":"1587705842296550",

    "reqRcvdInStack":"1587705842296921",

    "reqSentByStack":"1587705842298063",

    "reqRcvdByApp":"1587705842296836",

    "respRcvdByStack":"1587705842298666",

    "respPostedByStack":"1587705842298686",

    "status":"Good",

    "value":"0x01",

    “datatype”:”boolean”

    "scaledValue":	true,

    "usec":"1587705842298811",

    "tsMsgRcvdForProcessing":"1587705842299038",

    "tsMsgReadyForPublish":"1587705842299115"

}


**Response Message:** Error Response

{

    "app_seq" : "1234",

    "timestamp" : "2020-04-24 06:17:33",

    "version" : "2.0",

    "realtime" : "0",

    "data_topic" : "/flowmeter/PL0/Flow/readResponse",

    "wellhead" : "PL0",

    "metric" : "Flow",

    "tsMsgRcvdFromMQTT" : "1587709053533410",
    
    "tsMsgPublishOnEII" : "1587709053534618",

    "reqRcvdInStack" : "1587709053535694",
    
    "reqRcvdByApp" : "1587709053535467",

    "reqSentByStack" : "0",

    "respRcvdByStack" : "0",

    "respPostedByStack" : "1587709053536172",

    "status" : "Bad",

    "value" : "",

    “datatype”:”boolean”

    "scaledValue":,

    "error_code" : "2003",

    "usec" : "1587709053536590",

    "tsMsgRcvdForProcessing" : "1587709053537377",

    "tsMsgReadyForPublish" : "1587709053537647"

} 


**Response Message**: Error Response for Invalid Input JSON 

{

    "app_seq" : "1234",

    "timestamp" : "2020-04-24 06:22:42",

    "version" : "2.0",

    "realtime" : "0",

    "data_topic" : "/flowmeter/PL0/Flow/readResponse",

    "wellhead" : "PL0",

    "metric" : "Flow1",

    "tsMsgRcvdFromMQTT" : "1587709362173590",

    "tsMsgPublishOnEII" : "1587709362173872",

    "reqRcvdInStack" : "0",

    "reqSentByStack" : "0",

    "reqRcvdByApp" : "1587709362174221",

    "respRcvdByStack" : "0",

    "respPostedByStack" : "0",

    "status" : "Bad",

    "value" : "",

    “datatype”:”boolean”

    "scaledValue": ,

    "error_code" : "2014",

    "usec" : "1587709362174333",
    
    "tsMsgRcvdForProcessing" : "1587709362174590",

    "tsMsgReadyForPublish" : "1587709362174647"

}

-----------------------
7.4 	KPI Application
-----------------------

Following data (if available) is logged in a log-file by KPI Application for control loops.

.. figure:: Doc_Images/table11_1_update.png
    :scale: 80 %
    :align: center

.. figure:: Doc_Images/table11_2_update.png
    :scale: 80 %
    :align: center



