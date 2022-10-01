# p2p

```
/*
   Project Title: p2p
   Author: zainmfd
   Date: 01/10/2022
   Time: 1:10 PM
   Last Update:01/10/2022
*/
#include <Adafruit_MPU6050.h>
#include <Adafruit_Sensor.h>
#include <Wire.h>
#include <SD.h>
File sdcard_file;
Adafruit_MPU6050 mpu;

void setup(void) {
  Serial.begin(115200);
  if (!mpu.begin()) {
    Serial.println("Failed to find MPU6050 chip");
    while (1) {
      delay(10);
    }
  }
  Serial.println("MPU6050 Found!");
  mpu.setAccelerometerRange(MPU6050_RANGE_8_G);
  mpu.setGyroRange(MPU6050_RANGE_500_DEG);
  mpu.setFilterBandwidth(MPU6050_BAND_21_HZ);
  delay(100);

  // SD Card Initialization
  if (SD.begin()) {
    Serial.println("SD card is ready to use.");
  } else {
    Serial.println("SD card initialization failed");
    return;
  }
  Serial.println("Acc X (m/s^2) , Acc Y ,Acc Z ,Gyro X (rad/s) ,Gyro Y,Gyro Z");
  sdcard_file = SD.open("data.txt", FILE_WRITE);
  if (sdcard_file) {
    sdcard_file.print("Acc X (m/s^2)");
    sdcard_file.print(", ");
    sdcard_file.print("Acc Y");
    sdcard_file.print(", ");
    sdcard_file.print("Acc Z");
    sdcard_file.print(", ");
    sdcard_file.print("Gyro X (rad/s)");
    sdcard_file.print(", ");
    sdcard_file.print("Gyro Y");
    sdcard_file.print(", ");
    sdcard_file.print("Gyro Z");
    sdcard_file.println(" ");
    sdcard_file.close();  // close the file
  }
  // if the file didn't open, print an error:
  else {
    Serial.println("error opening data.txt");
  }

}

void loop() {
  sensors_event_t a, g, temp;
  mpu.getEvent(&a, &g, &temp);

  Serial.print(a.acceleration.x);
  Serial.print(",");
  Serial.print(a.acceleration.y);
  Serial.print(",");
  Serial.print(a.acceleration.z);
  Serial.print(",");

  Serial.print(g.gyro.x);
  Serial.print(",");
  Serial.print(g.gyro.y);
  Serial.print(",");
  Serial.print(g.gyro.z);
  Serial.println("");
  //Saving data into SD card
  sdcard_file = SD.open("data.txt", FILE_WRITE);
  if (sdcard_file) {
    sdcard_file.print(a.acceleration.x);
    sdcard_file.print(",");
    sdcard_file.print(a.acceleration.y);
    sdcard_file.print(",");
    sdcard_file.print(a.acceleration.z);
    sdcard_file.print(",");
    sdcard_file.print(g.gyro.x);
    sdcard_file.print(",");
    sdcard_file.print(g.gyro.y);
    sdcard_file.print(",");
    sdcard_file.println(g.gyro.z);
    sdcard_file.close();  // close the file
  }
  // if the file didn't open, print an error:
  else {
    Serial.println("error opening test.txt");
  }
  delay(500);
}
} 
```
Code for GPS
```
#include <Adafruit_GPS.h>
#include <SoftwareSerial.h>

SoftwareSerial p2pSerial(3, 2);
Adafruit_GPS GPS(&p2pSerial);

char c;

void setup() {
  Serial.begin(9600);
  GPS.begin(9600);

  GPS.sendCommand(PMTK_SET_NMEA_OUTPUT_RMCGGA);

  GPS.sendCommand(PMTK_SET_NMEA_UPDATE_1HZ);
  delay(1000);
}

void loop() {

  clearGPS();

  while (!GPS.newNMEAreceived()) {
    c = GPS.read();
  }

  GPS.parse(GPS.lastNMEA());

  Serial.print("Time: ");
  Serial.print(GPS.hour, DEC);
  Serial.print(':');
  Serial.print(GPS.minute, DEC);
  Serial.print(':');
  Serial.print(GPS.seconds, DEC);
  Serial.print('.');
  Serial.println(GPS.milliseconds);

  Serial.print("Date: ");
  Serial.print(GPS.day, DEC);
  Serial.print('/');
  Serial.print(GPS.month, DEC);
  Serial.print("/20");
  Serial.println(GPS.year, DEC);

  Serial.print("Fix: ");
  Serial.print(GPS.fix);
  Serial.print(" quality: ");
  Serial.println(GPS.fixquality);
  Serial.print("Satellites: ");
  Serial.println(GPS.satellites);

  if (GPS.fix) {
    Serial.print("Location: ");
    Serial.print(GPS.latitude, 4);
    Serial.print(GPS.lat);
    Serial.print(", ");
    Serial.print(GPS.longitude, 4);
    Serial.println(GPS.lon);
    Serial.print("Google Maps location: ");
    Serial.print(GPS.latitudeDegrees, 4);
    Serial.print(", ");
    Serial.println(GPS.longitudeDegrees, 4);

    Serial.print("Speed (knots): ");
    Serial.println(GPS.speed);
    Serial.print("Heading: ");
    Serial.println(GPS.angle);
    Serial.print("Altitude: ");
    Serial.println(GPS.altitude);
  }
  Serial.println("-------------------------------------");
}

void clearGPS() {
  while (!GPS.newNMEAreceived()) {
    c = GPS.read();
  }
  GPS.parse(GPS.lastNMEA());

  while (!GPS.newNMEAreceived()) {
    c = GPS.read();
  }
  GPS.parse(GPS.lastNMEA());
}
```
