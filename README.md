# Zabbix Sender output for [Telegraf](https://docs.influxdata.com/telegraf/)
For using with [Zabbix trapper items](https://www.zabbix.com/documentation/current/manual/config/items/itemtypes/trapper).
## Usage
### Installation
Just clone this repo somewhere, for example, in `/opt/`.
This script requires Python 3 (tested with 3.6) and [py-zabbix](https://pypi.python.org/pypi/py-zabbix/). Currently, the script depends on [specific patch which is in an open Pull Request in upstream](https://github.com/adubkov/py-zabbix/pull/131) so you have to use bundled version:
```
git submodule init
git submodule update
```
### Telegraf Configuration
Add to Telegraf config:
```toml
[[outputs.exec]]
    command = ['/path/to/telegraf-zabbix-sender/telegraf-zabbix-sender']
    data_format = 'json'
    timeout = '20s'

    [outputs.exec.tagpass]
        zabbix_key = ['*']

```
Hint: You can replace `['/path/to/telegraf-zabbix-sender/telegraf-zabbix-sender']` with `['systemd-cat', '/path/to/telegraf-zabbix-sender/telegraf-zabbix-sender']` if you want to always see script's stdout and stderr in [journal](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html) logs.
### Zabbix Configuration
Configure [Zabbix trapper item according to the documentation](https://www.zabbix.com/documentation/current/manual/config/items/itemtypes/trapper).
### Using
* Only measurements with `zabbix_key` tag are processed. Value of this tag is used as an [item key](https://www.zabbix.com/documentation/current/manual/config/items/itemtypes/trapper#configuration). Other tags and measurement name are ignored.
* Measurements should have only one value field (with any key). If there are multiple of them, random one will be used as a value. You should use [Unpivot Processor](https://github.com/influxdata/telegraf/blob/master/plugins/processors/unpivot/README.md) to split multi field series into single valued metrics to avoid undefined results.
