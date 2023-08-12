<h3 align="center">Pyduino_Metadata</h3>

  <p align="center">
    🔌 Arduino and Python serial communication metadata.
    <br />
    <a href="https://github.com/sourceduty/"><strong>Sourceduty »</strong></a>
    <br />
    <br />
  </p>
</div>

## PROCESS

Sensor module → Arduino UNO → Python program → .csv data → .txt metadata 

The .txt metadata files are stored in the "Metadata" folder. Statistical data for every file in the "Metadata" folder is tracked and stored in Tracking.txt.

## CODE

### ARDUINO

```
#define sensorPower 2
#define sensorPin A0

// Value for storing water level
int val = 0;

void setup() {
  // Set D7 as an OUTPUT
  pinMode(sensorPower, OUTPUT);
  
  // Set to LOW so no power flows through the sensor
  digitalWrite(sensorPower, LOW);
  
  Serial.begin(9600);
}

void loop() {
  //get the reading from the function below and print it
  int level = readSensor();
  
  Serial.println(level);
  
  delay(1000);
}

//This is a function used to get the reading
int readSensor() {
  digitalWrite(sensorPower, HIGH);  // Turn the sensor ON
  delay(10);              // wait 10 milliseconds
  val = analogRead(sensorPin);    // Read the analog value form sensor
  digitalWrite(sensorPower, LOW);   // Turn the sensor OFF
  return val;             // send current reading
```

### PYTHON

Save Arduino sensor module serial data in real-time to a .csv file with Python.

```
ser = serial.Serial('COM3')
ser.flushInput()

while True:
    try:
        ser_bytes = ser.readline()
        decoded_bytes = float(ser_bytes[0:len(ser_bytes)-2].decode("utf-8"))
        print(decoded_bytes)
        with open("test_data.csv","a") as f:
            writer = csv.writer(f,delimiter=",")
            writer.writerow([time.time(),decoded_bytes])
    except:
        print("Interrupted")
        break
```

## FUTURE UPDATES 

- Sorting and visualizing Pyduino sensor .txt metadata.
- Sorting and visualizing Tracking.txt file.
- Logging program session times and total time.
- Optional total data reset.

#
ℹ️ This software is free and open-source; anyone can redistribute it and/or modify it.
