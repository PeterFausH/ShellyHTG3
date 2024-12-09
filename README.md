## Name
Telegraf Config Shelly HTG3

## Description
as there are not many descriptions how to send climate data from Shelly to Influx-Database please feel free to use this telegraf config.

## Visuals
the device itself:
![Shelly HTG3 device](/media/ShellyHTG3.jpg "the device itself"]

topics we are interested in:
![mosquitto](/media/mosquitto_topic.png "topics we are interested in")

used part of the telegraf.conf

![the config part](/media/telegraf_conf.png "config part" )

temporary output of telegraf
![telegraf Output](/media/telegraf_out.png "output details")

how to call data in Grafana
![Grafana Query](/media/Grafana_Query.png "how to call data" )

## Updated Media if having multiple HTG3s in operation:

first modify your MQTT-Setup for easy identification:
![the config part](/media/HTG3_MQTT_Setup.png "config part" )

easy separation with tags

![the config part](/media/Telegraf_conf_allgemein.png "config part" )

easy identification of wanted device info

![Grafana Query](/media/Grafana_query.png "how to call data" )


## Installation
this is mentioned to be copied into your existing telegraf.conf where you already may have more topics and an already existing influxdb-output.

## Details
`name_override = "Shelly_HTG3"`
makes it easy to determine your statements in telegraf.out

`topics = ["shellyhtg3-mac/status/temperature:0",`

`          "shellyhtg3-mac/status/humidity:0"]`

These are the two statements i was interested in. Please use mac of your device instead of mac.

`data_format = "json_v2"`
as your shelly device delivers json data like this: 

`shellyhtg3-5432045634f0/status/humidity:0 {"id": 0,"rh":44.9}`
it's easy to parse if json is used as data_format


#[[inputs.mqtt_consumer.topic_parsing]]

`      topic =  "shellyhtg3-5432045634f0/status/+"`

`      measurement = "ShellyHTG3"`

`      tags = "serial/_/_"`

`      [[processors.pivot]]`

`        tag_key = "serial"`

`        value_key = "value"`

check that you are using your proper mac for your device
measurement is the name you can select in your grafana dashboard
If having more than one device, use a tag to specify the one you want to select data from.


#[[inputs.mqtt_consumer.json_v2]]

`    [[inputs.mqtt_consumer.json_v2.object]]`

`      path = "@this"`

"@this" in path"selects the entire json payload


`#[inputs.mqtt_consumer.json_v2.object.fields]`

`        tC = "float"`

`        rh = "float"`

here we define that we want to have float values from tC and rH. 

## Multiple devices
`#HTG allgemein. Im Gerät den Topic anpassen auf shellyhtg3/Keller_2827/ oder so`

`#shellyhtg3/Keller_2878/status/humidity:0 {"id": 0,"rh":62.9}`

`#shellyhtg3/Keller_2878/status/temperature:0 {"id": 0,"tC":27.5, "tF":81.6}`

`#shellyhtg3/Keller_2878/status/devicepower:0 {"id": 0,"battery":{"V":6.34, "percent":100} ?`

`[[inputs.mqtt_consumer]]`

`  name_override = "Shelly_HTG3_a"`

`  servers = ["tcp://localhost:1883"]
  qos = 0
  connection_timeout = "30s"`
`  topics = ["shellyhtg3/+/status/temperature:0",`
`            "shellyhtg3/+/status/humidity:0",`
`            "shellyhtg3/+/status/devicepower:0"]`
`  persistent_session = false`
`  data_format = "json_v2"`
`  [[inputs.mqtt_consumer.topic_parsing]]`
`      topic =  "shellyhtg3/+/status/+"`
`      #measurement = "ShellyHTG3"`
`      tags = "_/serial/_/_"`
`      [[processors.pivot]]`
`        tag_key = "serial"`
`        value_key = "value"`
`[[inputs.mqtt_consumer.json_v2]]`
`    [[inputs.mqtt_consumer.json_v2.object]]`
`      path = "@this"`
`      [inputs.mqtt_consumer.json_v2.object.fields]`
`        tC = "float"`
`        rh = "float"`
`        V  = "float"`


## Info
lots of people are complaining about the time between messages been sent. I checked with 2 devices, one close to the ventilation of my laptop which results in frequent temperature changes, and another one in a chilled corner of the building. See the number of messages been sent below:
![Shelly HTG3 messages](/media/Meldezeiten_HTG3.png "depending on themperature change you get more or less messages")

## Support
pf@nc-x.com

## Roadmap
done

## Contributing
open to contributions

## License
MIT

## Project status
done

