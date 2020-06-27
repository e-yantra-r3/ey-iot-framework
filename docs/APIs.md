# APIs

Our framework consists of APIs which users can use in the code as per their need. All the user will have to do is include these function in the code. So, whenever any IoT event occurs the user defined function attached to this event will be executed automatically without having the user call the function explicitly in main() or elsewhere.

So, below mentioned are the APIs that users can use to get their work done .  

<br>

## **Wi-Fi**

-----------------

```c
void ey_init_wifi_sta(char str_ssid[], char str_pass[])
```

> This API is used to connect to an existing Wi-Fi network. You must be having a  Wi-Fi hotspot to which you want your esp to be connected. For Wi-Fi security, WPA/WPA2 on an existing Wi-Fi network is used. Since we are using WPA/WPA2 authentication mode, this function takes SSID name and SSID key of your Wi-Fi network in order to join to this Wi-Fi network.
>

#### Parameters:

- str_ssid[] = SSID name of your Wi-Fi network
- str_pass[] = SSID password of your Wi-Fi network

------

```c
void ey_wifi_auto_reconnect(void)
```

> Your esp may be disconnected from your Wi-Fi network due to many reasons, e.g. the connected AP is restarted etc. It's the application's responsibility to do the reconnect. 
>
> So , you can use this API to auto reconnect to your Wi-Fi network This API internally calls esp_wifi_connect() if your Wi-Fi STA is disconnected.

#### Parameters: None 

<br>

## **MQTT**

-------------------------------------------

```c
void ey_mqtt_start(void)
```

> This API is used to  create an mqtt client handle based on the configuration and starts mqtt client with created client handle. You can set the configuration (which includes MQTT server URL and MQTT server port)  in the *ey_iot_config.h* file
>

#### Parameters: None

------

```c
int ey_mqtt_publish(char str_topic[], char str_data[], unsigned char qos)
```

> This API is used to send a publish message to the broker for a particular topic.
>

#### Parameters:

- str_topic[] :  array which stores the topic string.
- str_data[] : array which stores the  payload string (set to NULL, sending empty payload message).
- qos : qos of publish message

#### Return:

- msg_id  of the publish message (for QoS 0 message_id will always be zero) on success, -1 on failure.

------

```c
int ey_mqtt_subscribe(char str_topic[], unsigned char qos);
```

> This API is used to subscribe the client to defined topic with defined qos.
>

#### Parameters:

- str_topic[] : array which stores the topic string.


- qos : qos of subscribe message


#### Return: 

- msg_id of the subscribe message on success,  -1 on failure.

------

```c
int ey_mqtt_unsubscribe(char str_topic[]);
```

> This API is used to unsubscribe the client from defined topic.
>

#### Parameters:

- str_topic[] : array which stores the topic string.


#### Return: 

- msg_id of the subscribe message on success,  -1 on failure.

<br>

## **OTA**

-----------------

```c
int ey_ota_start(void);
```

> This API is used to start the remote OTA. It  allocates HTTPS OTA Firmware upgrade context, establishes HTTPS connection, reads image data from HTTP stream and writes it to OTA partition and finishes HTTPS OTA Firmware upgrade operation. This API supports URL redirection, but if CA cert of URLs differ then it should be appended to `cert_pem` member of `config`  (defined in function *void ey_iot_ota_task(void* *pvParameter)*). You can define your Remote OTA URL in *ey-iot_config.h*  file.
>

#### Parameters: None

------

```c
int ey_ota_advanced_start(int ota_mode);
```

> ​    It is used to create the advanced_ota_example with a user defined mode passed as a parameter to the function. 

#### Parameter:

- To start OTA in smart mode pass int ota_mode as 0.
- To start OTA in forced mode pass int ota_mode as 1.
- For any other value of int ota_mode forced mode will be activated. 

#### Return:

- Returns 0 on success.

<br>

## **Database**

-----------------

```c
int ey_push_sensor_data(unsigned char mode, float sensor_value);
```

> This API is used to used push one sensor data to your database.
>

#### Parameters: 

- mode : GOOGLE_SHEET if you are using google Spreadsheets as your database.


- sensor_value : readings of your sensor.

------

```c
int x_ey_push_sensor_data(unsigned char mode, int number_of_values, ... );
```

> This API is used to push more than one sensor data  to your database.
>

#### Parameters:

- mode : GOOGLE_SHEET ,  if you are using google spreadsheets as your database.


- number_of_values : number of values you wanted to push

<br>

## **NTP**

-----------------

```c
int ey_init_nvs(void);
```

> Initialize the default NVS partition.
>
> This API initialises the default NVS partition. The default NVS partition is the one that is labeled “nvs” in the partition table.

#### Return:

- ESP_OK if storage was successfully initialized.
- ESP_ERR_NVS_NO_FREE_PAGES if the NVS storage contains no empty pages (which may happen if NVS partition was truncated)
- ESP_ERR_NOT_FOUND if no partition with label “nvs” is found in the partition table
- one of the error codes from the underlying flash storage driver.

------

```c
void ey_init_ntp(void)
```

> It is used to set the system time. It makes 10 retrys and restarts the esp if the system time is not set even after 10th try.
>

------

```c
time_t ey_sntp_get_epoch(void);
```

> This API gets the current system time and checks whether it is set or not. If it is not set then it first initializes SNTP and then tries to set the time following the method in *void ey_init_ntp*. it restarts the esp module and returns the current set time. 

#### Return:

- It returns the current set system time.

------

```c
char* ey_sntp_date_string(void)
```

> This API is used to get the current date and the time string. It returns the date and time through a single character array. The date and time are stored as *'YY-MM-DD HH:MM:SS'* in the datestring returned by this function.

#### Return:

- Returns character array storing value of system date and time in the above mentioned format.

------

<br>

## **Location**

-----------------

```c
void ey_populate_isp_location(void)
```

This API is used to fetch information about ISP including its location. Information is received in JSON format.

```json
    {
        "status": "success", 
        "country": "India", 
        "countryCode": "IN", 
        "region": "GJ", 
        "regionName": "Gujarat", 
        "city": "Ahmedabad", 
        "zip": "380052", 
        "lat": 23.0276, 
        "lon": 72.5871, 
        "timezone": "Asia/Kolkata", 
        "isp": "Reliance Jio Infocomm Limited", 
        "org": "RJIL Gujarat LTE SUBSCRIBER PUBLIC", 
        "as": "AS55836 Reliance Jio Infocomm Limited", 
        "query": "157.32.130.153"
    }
```

 This information from the JSON is stored in `ey_iot` object.

```c++
ey_iot.isp_country
ey_iot.isp_region
ey_iot.isp_regionName
ey_iot.isp_city
ey_iot.isp_zip
ey_iot.isp_lat
ey_iot.isp_lon
ey_iot.isp_timezone
ey_iot.isp_org
ey_iot.isp_as
ey_iot.isp_public_ip
```

