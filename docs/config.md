# Configuring e-Yantra IoT Framework

* `ey_iot_config.h` file present in the framework is used to configure various services offered by the framework.
* Following is a sample of `ey_iot_config.h`. User can change it according to their usage.

```json
#ifndef __EY_IOT_CONFIG_H__
#define __EY_IOT_CONFIG_H__

//--------- Wi-Fi --------- 
#define EY_CONFIG_WIFI_SSID      	"my-wifi-ssid"
#define EY_CONFIG_WIFI_PASS      	"my-wifi-password"

//--------- MQTT --------- 
#define EY_CONFIG_MQTT_SERVER_URL 	"mqtt://broker.mqttdashboard.com"
#define EY_CONFIG_MQTT_SERVER_PORT 	1883
#define EY_SUB_TOPIC_LEN			100
#define EY_SUB_DATA_LEN				100

//--------- Encryption --------- 
#define EY_ENCRYP_XOR_KEY			'e'

//--------- NTP --------- 
#define EY_NTP_SERVER	    		"pool.ntp.org"
#define EY_NTP_TIMEZONE     		"UTC-5:30"

//--------- OTA --------- 
#define EY_REMOTE_OTA_URL           "https://raw.githubusercontent.com/username/reponame/master/hello-world.bin"
#define EY_REMOTE_OTA_TIMEOUT_MS	60000

//--------- Database --------- 
#define SPREADSHEET_ID              "unique-spreadsheet-id"

#endif /* __EY_IOT_CONFIG_H__ */
```

