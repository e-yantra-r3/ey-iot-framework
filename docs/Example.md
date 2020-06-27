# Example

In the previous sections, you have seen that our framework consists of APIs which you can use in the code as per your need. All you have to do is include these functions in your code.  So, whenever any IoT event occurs the user defined function attached to this event will be executed automatically without having the user call the function explicitly in main() or elsewhere.

This section consists of an example code that uses our APIs. This example pushes a data to the google Spreadsheet , performs Over-the-Air Updates using encrypted firmware stored in a Github Repo .

```c
//------------------------------------------------------------------------------
#include "ey_iot.h"
#include "ey_iot_config.h"

//------------------------------------------------------------------------------
#define SUB_TOPIC   "/ey/001/endpoint"
#define PUB_TOPIC   "/ey/001/server"
#define DELAY_SEC	60

//------------------------------------------------------------------------------
void init_callback(void);
void my_wifi_start(void);
void my_wifi_disconnect(void);
void my_mqtt_rx(void);
void my_mqtt_disconnect(void);
void my_ota_success(void);
void my_ota_fail(void);
void my_task_sensor_data_logging(void *p);

//------------------------------------------------------------------------------
void app_main(void){

	init_callback();
    ey_init_nvs();
    ey_init_wifi_sta(EY_CONFIG_WIFI_SSID, EY_CONFIG_WIFI_PASS);
    ey_init_ntp();
    ey_mqtt_start();

    ey_mqtt_subscribe(SUB_TOPIC, 0);
	ey_populate_isp_location();

    xTaskCreate(&my_task_sensor_data_logging, "my_task_sensor_data_logging", 8120, NULL, 10, NULL);
}


//------------------------------------------------------------------------------
void init_callback(void){
	
	ey_register_callback(EY_WIFI_EVENT_STA_START, my_wifi_start);
	ey_register_callback(EY_WIFI_EVENT_STA_DISCONNECTED, my_wifi_disconnect);

	ey_register_callback(EY_MQTT_EVENT_DATA_RX, my_mqtt_rx);

	ey_register_callback(EY_OTA_EVENT_SUCCESS, my_ota_success);
	ey_register_callback(EY_OTA_EVENT_FAIL, my_ota_fail);
}


void my_wifi_start() { 
	printf("USER: Wi-Fi Start\n");
}


void my_wifi_disconnect() { 
	printf("USER: Wi-Fi Disconnect\n");
	ey_wifi_auto_reconnect();
}


void my_mqtt_rx(){
	printf("SUB_TOPIC: %s\n", ey_mqtt_sub_topic);
    printf("SUB_DATA: %s\n", ey_mqtt_sub_data);

    if(strcmp(ey_mqtt_sub_data, "ota-start") == 0){
        printf("%s\n", "OTA Start Command Received");

        ey_mqtt_publish(PUB_TOPIC, "ota-start-cmd-received", 0);
        printf("PUB_DATA: %s\n", "ota-start-cmd-received");

        // Start OTA
        ey_ota_advanced_start(OTA_MODE_FORCED);
    }
}


void my_mqtt_disconnect(){
	printf("USER: MQTT Disconnect\n");
	ey_wifi_auto_reconnect();
}


void my_ota_success(){
	printf("USER: OTA Success\n");
	ey_mqtt_publish(PUB_TOPIC, "ota-success", 0);
	vTaskDelay(2000 / portTICK_PERIOD_MS);
	esp_restart();
}

void my_ota_fail(){
	printf("USER: OTA Fail\n");
}


void my_task_sensor_data_logging(void *p){

	long unsigned int start_time = 0, current_time = 0;
    char sensor_data = 99;

    start_time = ey_sntp_get_epoch();
    
    while(1){
        
		current_time = ey_sntp_get_epoch();

        if((current_time - start_time) >= 10){
			// Print Time
			printf(WHT "Time: " RESET); printf(CYN "%s\n" RESET, ey_sntp_date_string());
			
			// Print ISP Location
			if(ey_iot.isp_loc_status == 1){
				printf(WHT "Location: " RESET); printf(MAG "%s, %s, %s\n" RESET, ey_iot.isp_city, ey_iot.isp_regionName, ey_iot.isp_country);
			}

			// Print Sensor
			printf(WHT "Sensor: " RESET); printf(YEL "%d\n" RESET,sensor_data);
        	ey_push_sensor_data(GOOGLE_SHEET, sensor_data);
			printf("\n");
 
        	sensor_data--;
        	if(sensor_data == 0){
        		sensor_data = 99;
        	}

        	start_time = current_time;
        }
    }
}
//------------------------------------------------------------------------------
//------------------------------------------------------------------------------
```



And below is the demo video that shows the whole working of the above code.

<iframe width="560" height="315" src="https://www.youtube.com/embed/ODZHQ3rvVj0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>