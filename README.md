<h3 align="center">Pyduino_Metadata</h3>

  <p align="center">
    🔌 Arduino and Python serial communication metadata.
    <br />
    <a href="https://github.com/sourceduty/"><strong>Sourceduty »</strong></a>
    <br />
    <br />
  </p>
</div>

## PROCESS THEORY

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

Chart the Python .csv file data and save a .png chart image. Also, save .csv file metadata in Metadata.txt file.

```
import os
import sys
from collections import Counter
import matplotlib.pyplot as plt
import webbrowser

path = 'Folder1'
contents_list = []

for filename in filter(lambda p: p.endswith(".csv"), os.listdir(path)):
    filepath = os.path.join(path, filename)
    with open(filepath, mode='r') as meta_file:
        contents_list += [meta_file.read()]

    with open('Metadata.txt', 'w') as meta_file:
        sys.stdout = meta_file
        print(contents_list)

    with open(filepath, mode='r') as f:
        contents = f.read()
        words = contents.split()
        total_word_count = len(words)

        word_counter = Counter(words)
        most_common_words = word_counter.most_common()

        analysis_filename = f"Analysis_{filename}"
        with open(analysis_filename, 'w') as analysis_file:
            sys.stdout = analysis_file
            print("Total number of words:", total_word_count)
            print("\nRecurring words:")
            for word, count in most_common_words:
                print(f"{word}: {count}")

        top_words = 20
        top_words_data = most_common_words[:top_words]
        words, counts = zip(*top_words_data)

        plt.figure(figsize=(10, 6))
        plt.bar(words, counts)
        plt.title(f"Top {top_words} Most Common Words in {filename}")
        plt.xlabel("Words")
        plt.ylabel("Frequency")
        plt.xticks(rotation=45)
        plt.tight_layout()

        figure_filename = f"Word_Frequency_{filename}.png"
        plt.savefig(figure_filename)
        plt.close()
        webbrowser.open(figure_filename)
```

## FUTURE UPDATES 

- Sorting and visualizing Pyduino sensor .txt metadata.
- Sorting and visualizing Tracking.txt file.
- Logging program session times and total time.
- Optional total data reset.

#
ℹ️ This software is free and open-source; anyone can redistribute it and/or modify it.
