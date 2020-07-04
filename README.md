#include <DHT.h>
#define DHTTYPE DHT22
#include <ESP8266WiFi.h>
uint8_t pinDHT = D7;

String apiKey = "OQIYUNBXJ4EWWJ6S";
const char *ssid = "f e r i t";
const char *pass = "";
const char* server = "api.thingspeak.com";

DHT dht(pinDHT,DHTTYPE);
WiFiClient client;
float suhu; // Ambil nilai Suhu
float kelembaban; // Ambil nilai Kelembaban

void setup() {
 
  Serial.begin(9600);
  pinMode(pinDHT, INPUT);
  dht.begin();

  Serial.println("Connecting to ");
  Serial.println(ssid);
    WiFi.begin(ssid, pass);
    while (WiFi.status() != WL_CONNECTED){
     delay(500);
      Serial.println(".");
}

 Serial.println("");
  Serial.println("WiFi connected");
}
void loop() {
 
  //int readData = DHT.read22(DATA_PIN); // baca Data dari sensor
  suhu = dht.readTemperature(); // Ambil nilai Suhu
  kelembaban = dht.readHumidity(); // Ambil nilai Kelembaban
  if(client.connect(server, 80)){
    String postStr= apiKey;
    postStr += "&field1=";
    postStr += String(suhu);
    postStr += "&field2=";
    postStr += String(kelembaban);
    postStr += "\r\n\r\n";
      client.print("POST /update HTTP/1.1\n");
      client.print("Host: api.thingspeak.com\n");
      client.print("Connection: close\n");
      client.print("X-THINGSPEAKAPIKEY: "+apiKey+"\n");
      client.print("Content-Type: application/x-www-form-urlencoded\n");
      client.print("Content-Length: ");
      client.print(postStr.length());
      client.print("\n\n");
      client.print(postStr);
  // Mencetak output ke Serial monitor
  Serial.print("SUHU = ");
  Serial.println(suhu);
  Serial.print("KELEMBABAN = ");
  Serial.println(kelembaban);

  delay(1000);
  //Serial.println(" % ");
  }
}
