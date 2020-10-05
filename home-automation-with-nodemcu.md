NodeMCU ESP8266 Home Automation
===

Prerequisites
---
- NodeMCU 8266
- Relay Module
- Jumper Cable
- Electrical Cable
- Lamp
- ArduinoIDE
- Internet Access
- Server with Ubuntu OS (recommend: Ubuntu 16.04)
- Basic knowledge of Linux Command Line
- Basic knowledge of ArduinoIDE

Installing MQTT Broker on Server
---
```sh
$ sudo apt install mosquitto mosquitto-clients
$ sudo echo -n "allow_anonymous false\npassword_file /etc/mosquitto/passwd" > /etc/mosquitto/conf.d/default.conf
$ sudo mosquitto_passwd -c /etc/mosquitto/passwd <mqtt_username>
$ sudo service mosquitto restart
```

Install ArduinoIDE
---
To install ArduinoIDE please refer to this [link](https://www.arduino.cc/en/Main/Software).
After that you need to install `ESP8266 Library`, you can follow this [link](https://randomnerdtutorials.com/how-to-install-esp8266-board-arduino-ide/)

Wiring
---
DC Current
| GPIO Relay Pin | NodeMCU Pin  |
| ------------ | -----------:|
| `IN`           |  `D0`        |
| `VCC`          | `3V`         |
| `GND`          | `G`          | 
AC Current
Cut one of the electrical cable side. Connect to relay `COM` and `NO` on `Relay Module`

ArduinoIDE Code
---
```c++
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>
#include <WiFiClientSecure.h>
#include <PubSubClient.h>

#define LIGHT_PIN D0

const char* ssid = "<wifi_ssid>";
const char* pswd = "<wifi_password>";
const char* mqttServer = "<mqtt_server_ip>";
const int mqttPort = 1883;
const char* mqttUser = "<mqtt_username>";
const char* mqttPassword = "<mqtt_password>";
const char* pubSubTopic = "esp8266/garden_lamp";

int wifiStatus;
WiFiClientSecure httpsClient;
HTTPClient http;
WiFiClient espClient;
PubSubClient client(espClient);
bool alarmStatus = false;

void setup() {
  Serial.begin(115200);
  pinMode(LIGHT_PIN, OUTPUT);
  delay(200);

  // WiFi Connection
  Serial.println();
  Serial.println();
  Serial.print("You are connecting to : ");
  Serial.println(ssid);
  WiFi.begin(ssid, pswd);
  while(WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  wifiStatus = WiFi.status();
  if(wifiStatus == WL_CONNECTED) {
    Serial.println("");
    Serial.print("Connected to : "); 
    Serial.println(ssid);
    Serial.print("Your IP address is : ");
    Serial.println(WiFi.localIP());
  } else {
    Serial.println("");
    Serial.println("WiFi not connected!");
  }

  // MQTT Connection
  client.setServer(mqttServer, mqttPort);
  client.setCallback(callback);
  while (!client.connected()) {
    Serial.println("Connecting to MQTT Broker...");
    if (client.connect("ESP8266Client", mqttUser, mqttPassword )) {
      Serial.print("Connected to : mqtt://");
      Serial.println(mqttServer);
    } else {
      Serial.print("Failed with state ");
      Serial.print(client.state());
      delay(2000);
    }
  }
  client.subscribe(pubSubTopic);
  
}

void callback(char* topic, byte* payload, unsigned int length) {
  payload[length] = '\0';
  String message = (char*)payload;
  if (message == "off") {
    digitalWrite(LIGHT_PIN, LOW);
  } else if (message == "on") {
    digitalWrite(LIGHT_PIN, HIGH);
  }
}

void loop() {
  client.loop();
}
```
Compile the code and upload to your `ESP8266`

Testing
---
- Connect your `ESP8266` to AC Power with adapter
- Connect your lamp to AC Power
- SSH to your server and run this command
```sh
 $ mosquitto_pub -t "esp8266/garden_lamp" -u "mqtt_username" -P "mqtt_password" -m "on"
 $ mosquitto_pub -t "esp8266/garden_lamp" -u "mqtt_username" -P "mqtt_password" -m "off"
```
If you can see your light `on` or `off` depend on your `mqtt message` it mean you are successfully setup your `NodeMCU ESP8266` and connect it with MQTT Broker.
To make it automate, add cron scheduler on the MQTT Server.
- Turn On Lamp Every 6PM : `0 18 * * * mosquitto_pub -t "esp8266/garden_lamp" -u "mqtt_username" -P "mqtt_password" -m "on"`
- Turn Off Lamp Every 6AM : `0 6 * * * mosquitto_pub -t "esp8266/garden_lamp" -u "mqtt_username" -P "mqtt_password" -m "off"`
```sh
 # Copy above command to crontab
 $ sudo crontab -e
```

