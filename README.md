#define BLYNK_TEMPLATE_ID "TMPL6qBcFbBu"
#define BLYNK_DEVICE_NAME "IoT"
#define BLYNK_AUTH_TOKEN "yabYyVFQ83D5y4TTjWU23YkSLrjbKElc"

#define BLYNK_PRINT Serial

#include <ESP32Servo.h>
#include <WiFi.h>
#include <WiFiClient.h>
#include <BlynkSimpleEsp32.h>
char auth[] = BLYNK_AUTH_TOKEN;
char ssid[] = "Wokwi-GUEST";
char pass[] = "";
BlynkTimer timer;

const int ECHO = 13;
const int TRIG = 12;
Servo servo;
void setup() {
pinMode(TRIG, OUTPUT);
pinMode(ECHO, INPUT);
servo.attach(32, 500, 2400);
Serial.begin(115200);
//-------------------
Blynk.begin(auth, ssid, pass);
}
int pos = 0;
bool buka = false;
String perintah = "";
float readDistanceCM() {
digitalWrite(TRIG, LOW);
delayMicroseconds(2);
digitalWrite(TRIG, HIGH);
delayMicroseconds(10);
digitalWrite(TRIG, LOW);
int duration = pulseIn(ECHO, HIGH);
return duration * 0.034 / 2;
}
void bukaGerbang(){
Blynk.virtualWrite(V1, "Gerbang Terbuka");
for (pos = 90; pos >= 0; pos -= 1) {
servo.write(pos);
delay(10);
}
buka = true;
}
void tutupGerbang(){
Blynk.virtualWrite(V1, "Gerbang Tertutup");
for (pos = 0; pos <= 90; pos += 1) {
servo.write(pos);
delay(10);
}
buka = false;
}
void loop() {
Blynk.run();
timer.run();
String status = "";
float distance = readDistanceCM();
Blynk.virtualWrite(V0, distance);
if((distance < 100) && !buka){
bukaGerbang();
status = "aman";
}else if((distance > 100) && buka){
tutupGerbang();
status = "bahaya";
}
Blynk.virtualWrite(V2,status);
Serial.print("Measured distance: ");
Serial.println(readDistanceCM());
Serial.println(status);
delay(1000);
}
