# localtuya-homeassistant

Local handling for Tuya Devices under Home-Assistant and Hassio, getting parameters from them (as Power Meters: Voltage, Current, Watt)

# How it works:

   1. Copy switch.py to /custom_components/mytuya/ folder, inside /config folder (via Samba for HASSIO).
   2. Identify on your Home-Assistant logs (putting your logging into debug mode), the different attributes you want to handle by HA.

   3. Find in the switch.py file that part, and edit it for ID/DPS that is correct for your device.
```
          @property
          def device_state_attributes(self):
            attrs = {}
            try:
                attrs[ATTR_CURRENT] = "{}".format(self._status['dps']['104'])
                attrs[ATTR_CURRENT_CONSUMPTION] = "{}".format(self._status['dps']['105']/10)
                attrs[ATTR_VOLTAGE] = "{}".format(self._status['dps']['106']/10)
            except KeyError:
                pass
            return attrs
```
   4. Use this declaration on your configuration.yaml file (you need to get the 'device_id' and 'local_key' parameters for your device, as it can be obtained on other tutorials on the web:
```
       switch:
         - platform: localtuya
           host: 192.168.1.251
           local_key: 1234567890abcdef
           device_id: abcdef1234567890abcdef
           switches:
             switch1:
             friendly_name: TUYA_LED
             id: 1
           switch2:
             friendly_name: TUYA_SW01
             id: 101
```
      NOTE: (as many switch declared as the device has, take note that: If your device is composed (ex. one switch with a independent led light in it), this led can be declared as a 'switch'. ¡This Python script does not include RGB handling! (RGB Handling is independent and must be declared as a 'light' custom device, you can search web for examples, but i have not test this).
   5. Use this declaration on your configuration.yaml file, for stating sensors that handle its attributes:
```   
       sensor:
         - platform: template
           sensors:
             device_voltage:
               value_template: >-
                 {{ states.switch.TUYA_SW01.attributes.voltage }}
               unit_of_measurement: 'V' 
             device_current:
               acs_voltage: >-     
                 {{ states.switch.TUYA_SW01.attributes.current }}
               unit_of_measurement: 'mA'      
             device_current_consumption:
               value_template: >-
                 {{ states.switch.TUYA_SW01.attributes.current_consumption }}
               unit_of_measurement: 'W' 
```               
   6. If all gone OK (your device's parameters local_key and device_id are correct), your switch is working, so the sensors are working too.
   
   NOTE: You can do as changes as you want in scripts ant/or yaml files. But: You can't declare your "custom_component" as "tuya", tuya is a forbidden word from 0.88 version or so. So if you declare a switch.tuya, the embedded (cloud based) Tuya component will be load instead custom_component one.

# Thanks to:
sean6541, for the working (standard) Python Handler for Tuya devices.

some-other-user, i really can't find now, (i will credit/thank him here), who published a partialy Python script that inspired this solution
   