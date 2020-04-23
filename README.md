# Smoke_Sensor_IoT
Integrating smoke sensor with IoT
#include <ESP8266WiFi.h>
String apiKey = "Enter the API key"; // Enter your Write API key from ThingSpeak
const char *ssid = "Enter ssid"; // replace with your wifi ssid and wpa2 key
const char *pass = "Enter password";
const char* server = "api.thingspeak.com";
WiFiClient client;
void setup()
{
Serial.begin(115200);
delay(10);
Serial.println("Connecting to ");
Serial.println(ssid);
WiFi.begin(ssid, pass);
while (WiFi.status() != WL_CONNECTED)
{
delay(500);
Serial.print(".");
}
Serial.println("");
Serial.println("WiFi connected");
}
void loop()
{
float h = analogRead(A0);
if (isnan(h))
{
Serial.println("Failed to read from MQ-5 sensor!");
return;
}

if (client.connect(server, 80)) // "184.106.153.149" or api.thingspeak.com
{
String postStr = apiKey;
postStr += "&field1=";
postStr += String(h); 
postStr += "\r\n\";
client.print("POST /update HTTP/1.1\n");
client.print("Host: api.thingspeak.com\n");
client.print("Connection: close\n");
client.print("X-THINGSPEAKAPIKEY: " + apiKey + "\n");
client.print("Content-Type: application/x-www-form-urlencoded\n");
client.print("Content-Length: ");
client.print(postStr.length());
client.print("\n\n");
client.print(postStr);
Serial.print("Gas meter");
Serial.print(h);
Serial.println("%. Send to Thingspeak.");
}
client.stop();
Serial.println("Waiting...");
delay(3000);
}
