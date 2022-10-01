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
