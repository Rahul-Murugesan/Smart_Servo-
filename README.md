# Smart_Servo
Servo motor will control by mobile Using Esp8266 and Blynk.
#include <WiFi.h>
#include <WebServer.h>
#include <ESP32Servo.h>

// Replace with your network credentials
const char* ssid = "ROCKSTAR";
const char* password = "1234567890";

WebServer server(80);
Servo myservo;

int servoPin = 13;  // GPIO pin connected to the servo

void setup() {
  myservo.attach(servoPin);  // attach servo to pin
  Serial.begin(115200);

  // Connect to Wi-Fi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  
  // Print the IP address
  Serial.println(WiFi.localIP());

  // Define routes
  server.on("/", HTTP_GET, handleRoot);
  server.on("/servo", HTTP_GET, handleServo);

  // Start server
  server.begin();
}

void loop() {
  server.handleClient();
}

void handleRoot() {
  String html = "<html><head><title>Servo Control</title></head><body>"
                "<h1>Servo Control Interface</h1>"
                "<p><a href=\"/servo?pos=90\">Turn Servo to 90°</a></p>"
                "<p><a href=\"/servo?pos=180\">Turn Servo to 180°</a></p>"
                "<p><a href=\"/servo?pos=0\">Turn Servo to 0°</a></p>"
                "</body></html>";
  server.send(200, "text/html", html);
}

void handleServo() {
  String position = server.arg("pos");
  int angle = position.toInt();
  myservo.write(angle);  // Move servo to angle
  
  String html = "<html><head><title>Servo Moved</title></head><body>"
                "<h1>Servo Moved to " + position + "°</h1>"
                "<p><a href=\"/\">Return to Home</a></p>"
                "</body></html>";
  server.send(200, "text/html", html);
}
