# Rest Radar
## Overview

Rest Radar is a physical workspace wellness system designed to prevent sedentary fatigue. Instead of annoying notifications on your screen, it uses a peripheral "fuel gauge" on your desk to visualize how long you have been sitting or working continuously in front of your screen.

- The Wearable Sensor detects when you are seated at your computer.

- The Desk Gauge fills up slowly like a timer. When it hits the "Red Zone" (e.g., 50 minutes), it alerts you to take a break.

<img width="667" height="544" alt="e2fff865effaae6e8601e1a5b340a896" src="https://github.com/user-attachments/assets/d24ab8f0-d394-4b7a-93ef-a991d71031ff" />

## Why am I doing this?
According to University Health Services at Princeton, "In order to prevent RSI, adjust your desk and computer area to promote good posture. Remember that the human body is not made to sit still for long periods of time, so get up and move around as much as you can. This may involve taking 30-60 second breaks every ten minutes or so, and **getting up to walk around and stretch your muscles every hour**".

## Sensor
The sensing unit is a compact, unobtrusive "badge" designed to be magnetically clipped to your shirt collar or pocket. Its primary function is Sedentary State Detection. It houses a small rechargeable battery and features a status LED to indicate battery life and connection status.
Using an onboard MPU-6050 IMU (accelerometer), the device continuously tracks:

1. Orientation: Determines if the user is upright (sitting/standing) versus reclining or off-body.

2. Activity Level: Monitors micro-movements to determine if the user is stationary (working) or active (walking/moving).


![6bc67e3b8cabc195183e0a3682292223](https://github.com/user-attachments/assets/c7c04268-74c5-48e3-9a79-62d765753f30)

### Logic
- When the device detects the user is "Stationary & Upright" for a continuous period of 60 minutes, it triggers a "Stretch Alert." This status is transmitted wirelessly via Bluetooth to the Display Unit to notify the user.

### Bill of Materials (Sensor):

- Microcontroller: Seeed Studio XIAO ESP32C3 (Supports BLE & Battery Charge) X 1

- Sensor: InvenSense MPU-6050 (6-axis Accelerometer/Gyro) X 1 

- Power: 3.7V 400mAh LiPo Battery X 1

- User Interface: 3mm LED (Red/Green) for status indication X 1

- Mounting: N52 Neodymium Magnetic Backplate X 1
- Enclosure: Custom 3D-printed housing (PLA/PETG) X 1


## Display
The display unit is a minimalist, custom-designed desktop gauge that visualizes your Cumulative Sedentary Time. It acts as a peripheral "fuel gauge" for your energy levels, receiving wireless status updates from the wearable badge. To ensure a wireless and clean desktop setup, the unit is powered by an internal rechargeable battery and features a USB Type-C port for charging.

### Visual Logic
- The gauge face represents a 60-minute timeline. As you sit, the stepper motor slowly advances the needle from the "Fresh" zone (Green/Left) toward the "Fatigue" zone (Red/Right).
### Alert
- When the needle hits the 60-minute mark, the integrated RGB LED ring pulses red, visually signaling that it is time to stand up and stretch.
### Interaction
- A capacitive touch button below the dial allows you to manually pause the timer (for meetings) or reset it if you take a break at your desk.

![f7ea4114e332eb58a0610be65a6286a8](https://github.com/user-attachments/assets/fbd595fc-ea00-41fb-a785-b40e76e10854)

### Bill of Materials (Display):

- Microcontroller: Seeed Studio XIAO ESP32C3 (Surface mounted on Custom PCB) X 1

- Actuator: 28BYJ-48 5V Stepper Motor X 1

- Motor Driver: ULN2003 Driver IC (Integrated on PCB) X 1

- User Interface: Capacitive Touch Button with integrated WS2812B RGB LED Ring X 1

- Power: 3.7V 1000mAh LiPo Battery (USB-C Rechargeable) X 1

- Enclosure: Custom 3D-printed housing (PLA/PETG) X 1

## Diagrams

<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/6c21b9b6-9a5f-41c6-a959-890110ea0382" />

### Figure 1: Device Communication Architecture (Hardware Breakdown)

1. Wearable Sensor Unit (The Sender) This unit attaches to the user's body (or chair) to detect movement and posture.

#### Motion Sensor (IMU): MPU-6050
A 6-axis Accelerometer and Gyroscope. It detects if you are sitting upright, leaning forward (posture), or moving around (activity level).

#### Microcontroller: Seeed Studio XIAO ESP32C3

Reads data from the MPU-6050 and transmits status updates wirelessly via Bluetooth (BLE).

#### Power: 3.7V LiPo Battery (400mAh)

Small, rechargeable battery to keep the wearable lightweight.

2. Desk Display Unit (The Receiver) This unit sits on the desk, receives the signal, and provides visual feedback.

#### Microcontroller: Seeed Studio XIAO ESP32C3

Function: Receives the Bluetooth signal and controls the motor and LEDs.

#### Actuator (Motor): 28BYJ-48 (5V Stepper Motor)

Function: Physically rotates the gauge needle.

#### Motor Driver: ULN2003 Driver Board

Function: Interface board required to power and control the stepper motor.

#### User Interface (Input): TTP223 Capacitive Touch Sensor

Function: Detects when the user taps the button to reset the timer.

User Interface (Output): WS2812B RGB LED Ring (e.g., 8-bit or 12-bit ring)

Function: Integrated into the button to glow Green (good), Yellow (warning), or flash Red (alert).

### Figure 2: Operational Logic (Sensor & Data Flow)

#### Data Acquisition
The MPU-6050 sensor constantly reads X, Y, Z acceleration data.

#### Processing
The first XIAO ESP32C3 runs a "Stationary Detection Algorithm." If the variation in accelerometer data is below a specific threshold for >5 seconds, it flags the user as "Sedentary."

#### Wireless Transmission
The status (e.g., STATUS: SEDENTARY) is sent via BLE (Bluetooth Low Energy) to the desk unit.

#### Action
The desk unit's XIAO ESP32C3 processes this message. If "Sedentary" is true, it pulses the ULN2003 driver to move the 28BYJ-48 motor one step forward.

#### Reset
When the user touches the TTP223 sensor, the desk unit moves the motor back to the start position (Zero).
