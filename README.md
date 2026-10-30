
                                             Team 11 
                                             Group name: Infinity
                                    
                                    Members: Safwan Al Muktadir
                                             Mostafiz Mahim Ahmed
                                
                                Project: Web-Controlled Obstacle-Avoiding Car


# Web-Controlled Obstacle-Avoiding Car

A small robotics project that lets a user drive a car through a web
browser while an onboard sensor monitors the path ahead. When the car
detects an obstacle within a configured safety distance, it stops and
automatically moves backwards to reduce the risk of a collision.

> **Project status:** Prototype / educational project\
> **Control method:** Web browser over Wi-Fi\
> **Safety feature:** Automatic obstacle detection and reverse movement

## Table of Contents

-   [Overview](#overview)
-   [Features](#features)
-   [How It Works](#how-it-works)
-   [Hardware Requirements](#hardware-requirements)
-   [Software Requirements](#software-requirements)
-   [Suggested Wiring](#suggested-wiring)
-   [Getting Started](#getting-started)
-   [Web Controls](#web-controls)
-   [Obstacle Detection Logic](#obstacle-detection-logic)
-   [Customisation](#customisation)
-   [Testing and Safety](#testing-and-safety)
-   [Possible Improvements](#possible-improvements)
-   [Troubleshooting](#troubleshooting)
-   [Contributing](#contributing)


## Overview

This project combines wireless control, basic web development, motor
control, and distance sensing in a small robotic vehicle.

The user opens a control webpage hosted by the car's microcontroller (or
by a computer on the same network). Buttons on the webpage send movement
commands to the car. An ultrasonic distance sensor continuously checks
the area in front of the vehicle. If an obstacle is detected closer than
the chosen threshold, the controller overrides the forward command,
stops the motors, and briefly reverses the car.

The automatic safety behaviour is intended for a controlled
demonstration. It is not a substitute for certified collision-avoidance
equipment.

## Features

-   Control the car from a phone, tablet, or computer browser.
-   Move forward, backward, left, and right.
-   Stop the car using the web interface.
-   Measure the distance to objects in front of the car.
-   Automatically stop and reverse when an obstacle is too close.
-   Adjust the obstacle-distance threshold and reverse duration in the
    code.

## How It Works

1.  The controller connects to a Wi-Fi network and starts a small web
    server.
2.  The user opens the car's local IP address in a browser.
3.  A button press sends a movement command to the controller.
4.  The motor driver supplies power to the DC motors according to that
    command.
5.  The ultrasonic sensor measures the distance to the nearest object
    ahead.
6.  If the measured distance is below the safety threshold, the
    controller:
    -   cancels the forward command;
    -   stops the motors briefly;
    -   drives the motors in reverse for a short, configured period; and
    -   stops again and waits for a new command.

**Important:** The obstacle check should run repeatedly and should take
priority over web commands. The car should not continue moving forward
while the sensor reading is invalid or an obstacle is inside the safety
threshold.

## Hardware Requirements

  -----------------------------------------------------------------------
  Component                           Purpose
  ----------------------------------- -----------------------------------
  ESP32 development board             Wi-Fi connection, web server, and
  (recommended)                       control logic

  Motor driver module (such as L298N  Controls motor direction and power
  or TB6612FNG)                       

  Four geared 6V DC motors                Drive the wheels

  Robot car chassis and wheels        Mechanical platform

  HC-SR04 ultrasonic sensor (or       Detects obstacles ahead
  compatible distance sensor)         

  Battery pack suitable for the       Supplies power
  motors and controller               

  Jumper wires and switch             Electrical connections and power
                                      control

  Optional caster wheel               Supports the chassis
  -----------------------------------------------------------------------

Choose a motor driver and battery that are suitable for the motors'
voltage and current. Do not power motors directly from the ESP32 pins.

## Software Requirements

-   Arduino IDE or PlatformIO
-   ESP32 board support package
-   A modern web browser
-   ESP32 Wi-Fi and web-server libraries supported by your chosen
    development environment

The exact libraries and pin assignments depend on the controller and
motor driver used.

## Suggested Wiring

The following is a **functional connection guide, not a universal
pinout**. Check the pin labels and voltage requirements of your exact
modules before connecting them.

  -----------------------------------------------------------------------
  Module                              Connects to
  ----------------------------------- -----------------------------------
  Motor driver's logic inputs         Four suitable ESP32 GPIO pins

  Motor driver's enable/PWM inputs    Suitable PWM-capable ESP32 GPIO
                                      pins, if required

  Left motor                          Motor driver's left output
                                      terminals

  Right motor                         Motor driver's right output
                                      terminals

  Ultrasonic sensor TRIG              Suitable ESP32 GPIO output

  Ultrasonic sensor ECHO              Suitable ESP32 GPIO input through a
                                      safe voltage level

  Sensor VCC and GND                  Appropriate supply and common
                                      ground

  Motor driver motor supply           Battery supply within the driver's
                                      rated range

  ESP32 supply                        A suitable regulated supply

  ESP32 GND, sensor GND, and driver   Common ground
  logic GND                           
  -----------------------------------------------------------------------

**Voltage warning:** Many HC-SR04 modules output a 5 V ECHO signal,
while ESP32 GPIO pins are not 5 V tolerant. Use an appropriate voltage
divider or level shifter before connecting ECHO to the ESP32. Keep motor
power and logic power within their components' ratings.

## Getting Started

1.  Assemble the chassis, motors, wheels, motor driver, controller, and
    distance sensor.
2.  Connect the components using the documentation for your specific
    modules.
3.  Install the ESP32 board support package and required libraries.
4.  Open the project source code in Arduino IDE or PlatformIO.
5.  Set the Wi-Fi credentials, GPIO pin assignments, obstacle threshold,
    and reverse duration.
6.  Upload the program to the ESP32.
7.  Open the serial monitor and note the IP address printed after the
    board connects to Wi-Fi.
8.  Connect your phone or computer to the same Wi-Fi network.
9.  Enter the displayed IP address in a browser to open the control
    page.
10. Test the car at low speed in a clear, open area before trying the
    obstacle-detection feature.

If your project uses a different controller or a different web-server
setup, update these steps to match your implementation.

## Web Controls

The webpage should provide the following controls:

  Button     Action
  ---------- -----------------------------------------------------------
  Forward    Move forward, unless the safety system blocks the command
  Backward   Move backward
  Left       Turn left
  Right      Turn right
  Stop       Stop both motors

A useful interface can also display the current sensor distance,
connection status, and whether the safety system has activated.

## Obstacle Detection Logic

Set a minimum safe distance in the program. For example, a prototype
might begin testing with a threshold of **20 cm**, then adjust it based
on the sensor, vehicle speed, braking distance, and test results.

Example high-level logic:

``` text
repeat:
    measure distance ahead

    if distance reading is invalid:
        stop motors
    else if distance is below the safety threshold:
        stop motors
        wait briefly
        reverse for the configured duration
        stop motors
    else:
        read the latest web command
        perform the requested movement
```

The reverse movement should be short and controlled. A rear-facing
sensor is recommended if the car may reverse into objects. Without one,
the system cannot confirm that the space behind the car is clear.

## Customisation

You can modify the project by changing:

-   **Safety threshold:** The distance at which the car triggers its
    safety response.
-   **Reverse duration:** How long the car moves backwards.
-   **Motor speed:** PWM values used for movement.
-   **Webpage design:** Button layout, status indicators, and distance
    display.
-   **Network mode:** Connect to a Wi-Fi router or configure the ESP32
    as an access point.

Use conservative settings first, then tune them through supervised
testing.

## Testing and Safety

-   Test with the wheels raised before allowing the car to drive on the
    floor.
-   Begin at low speed and use a clear, level test area.
-   Test each motor direction separately before testing browser control.
-   Place a soft, stationary object in front of the car and verify that
    it stops and reverses.
-   Test different obstacle distances and confirm that forward motion is
    blocked when required.
-   Check what happens when Wi-Fi disconnects or the sensor gives an
    invalid reading. The safest default is to stop the motors.
-   Keep hands, loose wires, and clothing away from moving wheels and
    gears.
-   Switch off power before changing wiring.
-   Never test near stairs, roads, people, pets, or fragile objects.
-   Do not rely on this prototype to protect people or prevent real
    vehicle accidents.

## Possible Improvements

-   Add a rear distance sensor so reverse movement is also monitored.
-   Add side sensors for more complete obstacle awareness.
-   Show live distance readings on the webpage.
-   Add speed control and a clearly visible emergency-stop control.
-   Add a connection timeout that stops the car if commands stop
    arriving.
-   Use a more reliable distance sensor if the project needs better
    performance in different environments.
-   Record sensor readings and movement events for debugging.

## Troubleshooting

  -----------------------------------------------------------------------
  Problem                             Possible checks
  ----------------------------------- -----------------------------------
  Webpage does not load               Confirm the phone/computer is on
                                      the same network and check the IP
                                      address in the serial monitor

  Motors do not move                  Check battery charge, motor-driver
                                      wiring, common ground, and
                                      motor-driver enable pins

  Car moves in the wrong direction    Swap the relevant motor wires or
                                      correct the direction logic in code

  Distance readings are incorrect     Check sensor wiring, power, timing,
                                      and the ECHO voltage-level circuit

  Car does not reverse                Check that the obstacle threshold
                                      is reached and that the reverse
                                      motor command is implemented

  ESP32 restarts when motors run      Check battery capacity, motor
                                      current, power regulation, and
                                      electrical noise
  -----------------------------------------------------------------------

## Contributing

Suggestions and improvements are welcome. Before sharing changes, test
them carefully and document any new hardware requirements, pin
assignments, or software dependencies.


## Motor Selection Justification

Four 6V TT geared DC motors were selected to drive the robotic car. These motors are commonly used in small robotic vehicles due to their compact size, affordability, and ability to provide sufficient torque for lightweight mobile robots.

### Reasons for Selection

1. **Adequate Torque:** The built-in gearbox reduces rotational speed while increasing torque, helping the car move with its chassis and electronic components.

2. **Four-Wheel Drive:** Using four motors provides power to all four wheels, improving traction and movement across different floor surfaces.

3. **Bidirectional Movement:** With an H-bridge motor driver, the motors can rotate in both directions, allowing the car to move forward and backward.

4. **Better Manoeuvrability:** The motors can be controlled in groups or individually to enable turning and directional movement.

5. **Obstacle Avoidance:** The motors allow the controller to stop the car and activate reverse movement when the ultrasonic sensor detects an obstacle.

6. **Low Operating Voltage:** Their 6V rating makes them suitable for small, battery-powered robotic projects.

7. **Cost-Effectiveness:** TT motors are inexpensive, readily available, and easy to integrate into educational robotics projects.

### Conclusion

Four 6V TT geared DC motors were chosen because they provide a practical combination of torque, traction, affordability, and directional control. Their four-wheel configuration makes them suitable for a compact robotic car that requires web-based movement control and automatic obstacle avoidance.

**Motor Driver Consideration:** The motor driver must be capable of handling the combined current requirements of all four motors, particularly their startup and stall currents.
