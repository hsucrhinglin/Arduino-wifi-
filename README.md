#include <WiFiEsp.h>
#include <Servo.h>

#define LAN_SSID "133"
#define LAN_PASS "33333333"

WiFiEspServer server(80);
int status = WL_IDLE_STATUS;     // the Wifi radio's status
RingBuffer buf(32);
char cmd[32];
Servo myServo1;
Servo myServo2;
Servo myServo3;
Servo myServo4;

int ledPin = 13;
int servo1Pin = 8;
int servo2Pin = 9;
int servo3Pin = 10;
int servo4Pin = 11;
int angleOf50 = 0;
int angleOf10 = 0;
int angleOf5 = 0;
int angleOf1 = 0;
int bufIndex = 0;


void setup() {
  pinMode(ledPin, OUTPUT);
  myServo1.attach(servo1Pin);
  myServo2.attach(servo2Pin);
  myServo3.attach(servo3Pin);
  myServo4.attach(servo4Pin);
  Serial.begin(9600);
  Serial1.begin(9600);
  InitWiFi();
}

void loop() {
  WiFiEspClient client = server.available();  // listen for incoming clients
 
  if(client) {
    Serial.println("New client");
    buf.init();
    int index = 0;
    while(client.connected()) {
      if(client.available()) {
        char c = client.read();
        buf.push(c);
        cmd[index++] = c;
        if (buf.endsWith(";")) {
          cmd[index]=0;
          execCmd(client);
          index = 0;
        }
      }
      delay(10);
    }
 
    // close the connection
    client.stop();
    Serial.println("Client disconnected");
  }
}

void execCmd(WiFiEspClient client)
{
  Serial.println(cmd);
  bufIndex = 0;
  while(cmd[bufIndex]!='@')
    bufIndex++;
  while(cmd[bufIndex]!=';')
  {
    if(cmd[bufIndex]=='C')
    {
      bufIndex++;
      int u = getInt(cmd);
      Serial.println(u);  // 1
      bufIndex++;
      int v = getInt(cmd);
      Serial.println(v);  // 50
      if(v==50){
        getCoin50(u);
        //Servo1.write(v%181);
        break;
      }
      else if (v==10){
        getCoin10(u);
        //Servo2.write(v%181);
        break;
      }
      else if (v==5){
        getCoin5(u);
        //Servo3.write(v%181);
        break;
      }
      else if (v==1){
        getCoin1(u);
        //Servo4.write(v%181);
        break;
      }
    }
    bufIndex++;
    delay(10);
  }
  Serial.println("Job done");
}

void getCoin50(int n)
{
  for(int i=0; i<n; i++) {
    angleOf50 = 180-angleOf50;
    myServo1.write(angleOf50);
    delay(500);
  }
}

void getCoin10(int n)
{

  for(int i=0; i<n; i++) {
    angleOf10 = 180-angleOf10;
    myServo2.write(angleOf10);
    delay(500);
  }
  }
  void getCoin5(int n)
{
  for(int i=0; i<n; i++) {
    angleOf5 = 180-angleOf5;
    myServo3.write(angleOf5);
    delay(500);
  }
}
void getCoin1(int n)
{
  for(int i=0; i<n; i++) {
    angleOf1 = 180-angleOf1;
    myServo4.write(angleOf1);
    delay(500);
  }
}
void turnOnLed(int ledpin)
{
  digitalWrite(ledpin, HIGH);  
}

void turnOffLed(int ledpin)
{
  digitalWrite(ledpin, LOW);  
}

int getInt(char *cmd)
{
  int v = 0;
  while(cmd[bufIndex]<'0'||cmd[bufIndex]>'9')
    bufIndex++;
  while(cmd[bufIndex]>='0'&&cmd[bufIndex]<='9')
  {
    v = v*10+(cmd[bufIndex]-'0');
    bufIndex++;
  }
  return v;
}

void InitWiFi()
{
  WiFi.init(&Serial1);

  if (WiFi.status() == WL_NO_SHIELD) {
    Serial.println("WiFi shield not present");
    while (true); // don't continue
  }

  Serial.print("Attempting to start AP ");
  Serial.println(LAN_SSID);

  // start access point
  status = WiFi.beginAP(LAN_SSID, 10, LAN_PASS, ENC_TYPE_WPA2_PSK);

  Serial.println("Access point started");
  printWifiStatus();

  // start the web server on port 80
  server.begin();
  Serial.println("Server started");
}

