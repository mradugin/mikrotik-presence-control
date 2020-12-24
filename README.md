# mikrotik-presence-control
Mikrotik router script for reliable detection of WiFi client and sending HTTP requests when devices are registered or unregistered from the network. 
This approach works very reliably with Apple iPhone, iPad and other devices that are not reachable with TCP or UDP, but are still registered in the network. 

## How it Works

Mikrotik scheduler calls presence control script with specified interval to check if desired devices are registered in WiFi network. 
When script detects device state change in the network, it will send HTTP request to a desired server to report that device is registered, same when device is no longer present in the WiFi network. 
To improve reliability with multiple access point setup, when device is registered script will send up to `arriveSendTimes` requests to the server, as it may happen that access point where device was unregistered can send this event after registration on the second access point happened. 

Tested on `RouterOS v6.45.1 (stable)`

## Installation and Configuration

### Configuration 

**Variables**

* `arriveSendTimes` - number of times to send requests to a server when device is registered within WiFi network
* `subjects` - array of subjects to check presence of, each entry contains `name`, `mac` and `state`
  * `name` - name of person who owns particular device, is optinal and is only used in when sending HTTP requests
  * `mac` - MAC adress of wathced device, should be in uppercase
  * `state` - internal state variable used for detecting state changes, should be set to 0

**In-script Modification**

Modify following lines based on your requirements, in current example it was used for reporting state to [homebridge-people-x](https://www.npmjs.com/package/homebridge-people-x) via webhooks:

```
:tool fetch mode=http http-method=post http-data="" url="http://homebridge:51828/?sensor=$name&state=false"
...
:tool fetch mode=http http-method=post http-data="" url="http://homebridge:51828/?sensor=$name&state=true"
```

### Installation

From Mikrotik WebFig IDE:
* Create new script `System` -> `Scripts` -> `Scripts tab` -> `Add New`
* Set Name to `presence_control`
* Paste [presence-control.script](presence-control.script) content into `Source`
* Withing script:
  * Modify `subjects` array
  * Adjust values of `url` and `http-data`
* Leave other options as-is
* Click `Apply`
* Create new scheduler `System` -> `Schedulers` -> `Add New`
* Tick `Enabled`
* Set `Name` to `presence_control`
* Set `Interval` to `00:00:05`
* Set `On Event` to `presence_control`
* Click `Apply`
