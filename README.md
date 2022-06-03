# ups-telegraf

Collect, parse and push data from USB attached UPS device with Telegraf and Python to InfluxDB and Grafana 

Transforms `upsc` output like this:
```
battery.charge: 100
battery.charge.low: 10
battery.charge.warning: 20
battery.mfr.date: CPS
battery.runtime: 3133
battery.runtime.low: 300
battery.type: PbAcid
battery.voltage: 13.1
battery.voltage.nominal: 12
```
into InfluxDB Line Protocol like this: 
```
ups battery.charge=100,battery.charge.low=10,battery.charge.warning=20,battery.mfr.date="CPS",battery.runtime=2970,battery.runtime.low=300,battery.type="PbAcid",battery.voltage=13.1,battery.voltage.nominal=12
```

## Usage

install telegraf
```
sudo apt install -y telegraf
```

clone the repo via 
```
cd /opt
git clone https://github.com/jovalle/ups-telegraf.git
cd /opt/ups-telegraf
```

install python dependencies
```
pip install -r requirements.txt
```

check the `config/` directory for your device or generate a configuration using:
```
upsc $(upsc -l 2>/dev/null | tail -n 1) 2>/dev/null | awk -F ":" '{ $2 = "" ; print $0 }'
```

update the telegraf configuration (default: `/etc/telegraf/telegraf.conf`) with the new input:
```
[[inputs.exec]]
   commands = ["python /opt/ups-telegraf/get_ups_data.py --config tripplite/smart1500lcd --name tripplite"]
   timeout = "5s"
   data_format = "influx"
```

## Compatibility
Tested on:
* CyberPower CP1000AVRLCDa
* CyberPower SL700U (`cyberPower/sl700u`)
* Dell UPS 1000T/1920T/1920R HV (`dell/5px`)
* Eaton eco 1600 (`eaton/eco`)
* MGE Pulsar 2200 (`eaton/pulsar`)
* Tripp Lite SMART1500LCD (`tripplite/smart1500lcd`)

Feel free to test and submit a PR!

## Contributors

Thanks to the following for helping improve this repo.

* @openincident
* @mattster98
* @Graffics
* @Depfryer
