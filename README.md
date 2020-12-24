# mikrotik-presence-control
Mikrotik router script for reliable detection of WiFi client and sending HTTP requests when devices are registered or unregistered from the network. 
This approach works very reliably with Apple iPhone, iPad and other devices that are not reachable with TCP or UDP, but are still registered in the network. 

## How it works

Mikrotik scheduler calls presence control script with specified interval to check if desired devices are registered in WiFi network. 
When script detects device state change in the network, it will send HTTP request to a desired server to report that device is registered, same when device is no longer present in the WiFi network. 
To improve reliability with multiple access point setup, when device is registered script will send up to `arriveSendTimes` requests to the server, as it may happen that access point where device was unregistered can send this event after registration on the second access point happened. 

## Installation and configuration

From Mikrotik WebFig IDE:
* Create new script `System` -> `Scripts` -> `Scripts tab` -> `Add New`
* Set Name to `presence_control`
* Paste file://presence-control.script content into `Source`
* Withing script:
  * Modify `subjects` array
  * Adjust values of `url` to point to a desired URL which will be called when device enters or leaves WiFi network
  * If needed modify `http-data` as well
* Leave other options as-is
* Click `Apply`
* Create new scheduler `System` -> `Schedulers` -> `Add New`
* Tick `Enabled`
* Set `Name` to `presence_control`
* Set `Interval` to `00:00:05`
* Set `On Event` to `presence_control`
* Click `Apply`
