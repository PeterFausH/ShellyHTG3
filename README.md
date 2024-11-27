## Name
Telegraf Config Shelly HTG3

## Description
as there are not many descriptions how to send climate data from Shelly to Influx-Database please feel free to use this telegraf config.

## Visuals
![Shelly HTG3 device](/media/ShellyHTG3.jpg "the device itself")
![mosquitto](/media/mosquitto_topic.png "topics we are interested in")
![the config part](/media/telegraf_conf.png "config part" )
![telegraf Output](/media/telegraf_out.png "output details")
![Grafana Query](/media/Grafana_Query.png "how to call data" )

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
    [[inputs.mqtt_consumer.json_v2.object]]
      path = "@this"
"@this" in path"selects the entire json payload

#[inputs.mqtt_consumer.json_v2.object.fields]
        tC = "float"
        rh = "float"
here we define that we want to have float values from tC and rH. 


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

