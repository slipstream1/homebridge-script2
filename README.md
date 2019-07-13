homebridge-script2
==============

Custom Script2 fork - for Philips Air Purifier specifically

Registers Air purifer in homekit via homebridge

Allows switch on and switch off ONLY and is able to detect state of air purifier


Core of the code written by [@xxcombat](https://github.com/xxcombat/)

Original plugin [homebridge-script](https://github.com/xxcombat/homebridge-script)

Thanks to [@ybizeul](https://github.com/ybizeul/) for the code snipet that allows for state.sh to execute

Thanks to .[pponce] (https://github.com/pponce/homebridge-script2) for putting this switch2 code together

Thanks to .[rgerganov] (https://github.com/rgerganov/py-air-control) for working out how to communicate with Philips Air purifier 


Personally im using a windows 10 machine to run homebridge and the User, used to login is called "Home"


## Installation
(Requires node >=6.0.0)

1. Install homebridge using: `npm install -g homebridge` or https://github.com/jvmahon/homebridge-homeseer/wiki/Windows-10-Installation

2. Install this plugin using: `npm install -g homebridge-script2`

3. Install the following code - https://github.com/rgerganov/py-air-control

3a. Install Python 3.4 or above

3b. Install the libary `pip3 install py-air-control`

4. Create a Scripts folder in your homebridge directory

5. Add the accessory portion from the config.json into your homebridge config.json

5a. Amend the config.json to change "Home" to your user account

5b. Change the name from "Fan" to whatever you want if desired

6. Find out the IP address of your philips air purifier - Check your router and see what address it has been given / give it a static IP /  DHCP reservation to ensure it doesnt change.

7. Copy the on.sh / off.sh / state.sh and air.sh to your scripts directory

8. Amend on.sh / off.sh and air.sh - with the IP address of your purifier

9. Amend state.sh to change "Home" to your user account

10. Restart homebridge

11. In the apple home app, change the Type from 'switch' to 'fan'



TroubleShooting

If using windows - 
make sure you can run the scipts from command prompt

test purifier connectivity - airctrl X.X.X.X (where X is your IP address of the purifier) - this should return the state of your purifier

test power on - airctrl X.X.X.X --pwr 1
test power off - airctrl X.X.X.X --pwr 0

with the power on - test the state - airctrl X.X.X.X | grep "Power: ON"

Test you can run shell scripts
If windows - ensure your PATHs are correct for python and node







Below are the orginal notes related to Script2 standard installation / use
----

3. Update your configuration file. See examples below that show the plugin working by using filestate for current state check as well as an example using state.sh script for current state check.
4. Make sure scripts have been made executable (chmod +x scriptname.sh) and also accessible by the homebridge user. 

For autostart homebridge with OSX copy com.homebridge.startup.plist to /Library/LaunchDaemons

Homebridge-script configuration parameters

Name | Value | Required | Notes
----------- | ------- | -------------- | --------------
`accessory` | "Script2" | yes | Must be set to "Script2" and is required
`name` | _(custom)_ | yes | Name of accessory that will appear in homekit app and is required
`on` | _(custom)_ | yes | Location of script to execute the on action and is required
`off` | _(custom)_ | yes | Location of script to execute the off action and is required
`fileState` | _(custom)_ | fileState or state is required (see note) | Location of file that flags on or off current state. If this is configured the plugin will use the existence of this file to determine the current on or off state. If file exists, accessory is determined to be on. If file does not exist, accessory is determined to be off. This is not required. But if set, it will override using the state script. fileState or state must be configured.
`state` | _(custom)_ | fileState or state is required (see note) | Location of script to execute the current state check. It must output to stdout the current state. It is not required if fileState is being used instead. fileState or state must be configured.
`on_value` | _(custom)_ | no* (see note, default set to "true") | Used in conjunction with the state script. If using the state script this is the value that will be used to match against the state script output. If this value matches the output, then the accessory will be determined to be on. Required if using state script.

## Configuration

### Configuration example 1, using filestate for current state check:

```
"accessories": [
	{
              "accessory": "Script2",
              "name": "RPC3 Socket 1",
              "on": "/var/homebridge/rpc3control/on.sh 1",
              "off": "/var/homebridge/rpc3control/off.sh 1",
              "state": "/var/homebridge/rpc3control/state.sh 1",
              "fileState": "/var/homebridge/rpc3control/script1.flag",
              "on_value" : "true"
	}
]
```
#### Notes
##### Using the above configuration as an example:
- The on.sh script executes when you turn on the accessory via a homekit app. (In this case we are the using existence of a file to determine on or off current state, so you must insure the on.sh script creates the configured fileState file.
- The off.sh script executes when you turn off the accessory via a homekit app. ( In this case we are using existence of a file to determine on or off current state, insure the off.sh script deletes the configured fileState file.)
- The state.sh script in this case would not execute as fileState parameter overrides its use.
- The configured fileState file is used as a flag. When the homekit app checks for current state it checks for the existence of this file. If it exists, current state is on. If it does not exist, current state is off.
- The on_value in this case is not being used as it is only used when the state script is used to check for current state.

### Configuration example 2, executing state.sh script for current state check:
```
"accessories": [
	{
              "accessory": "Script2",
              "name": "Alarm of bike",
              "on": "~/on.sh",
              "off": "~/off.sh",
              "state": "~/state.sh",
              "on_value" : "true"
	}
]
```
#### Notes
##### Using the above configuration as an example:
- The on.sh script executes when you turn on the accessory via a homekit app. (In this case we are executing a state script to determine on or off current state.)
- The off.sh script executes when you turn off the accessory via a homekit app. ( In this case we are executing a state script to determine on or off current state.)
- The state.sh script in this case would be executed to check current state.  Insure that this script outputs to stdout the matching on value as configured by the on_value config parameter. If the on_value matches the on value output of this script then the accessory will be determined to be on.
- The configured fileState file is not used in this example. Because it was not configured, the state script is being used.
- The on_value in this case is used to match against the state script output. If the value matches the output of the state script, the accessory is determined to be on.

