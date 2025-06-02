# ROS 2 Humble with Arduino Due

This repository describes how to connect an Arduino Due to a ROS 2 system using **micro-ROS**. The steps below focus on Ubuntu 22.04 running ROS 2 Humble, but the same approach works with Ubuntu 24.04 (ROS 2 Jazzy).

## Requirements

- Ubuntu 22.04 with ROS 2 Humble installed
- An Arduino Due board and USB cable

## Host (PC) Setup

1. Install ROS 2 and the micro-ROS agent:
   ```bash
   sudo apt update
   sudo apt install ros-humble-desktop ros-humble-micro-ros-agent
   ```
2. Add your user to the `dialout` group so the agent can access `/dev/ttyACM0`:
   ```bash
   sudo usermod -a -G dialout $USER
   ```
   Log out and back in for the group change to take effect.
3. Connect the Arduino Due to your PC. It should appear as `/dev/ttyACM0` or `/dev/ttyACM1`.

## Arduino Setup

1. Install the Arduino IDE and add the **Arduino SAM Boards** package for Arduino Due.
2. Use the library manager to install **micro_ros_arduino**.
3. Open `File → Examples → micro_ros_arduino → micro-ros_publisher`.
4. Modify the sketch to use the serial transport:
   ```cpp
   #include <micro_ros_arduino.h>
   void setup() {
     Serial.begin(115200);
     set_microros_serial_transports(Serial);
     // rest of initialization...
   }
   ```
5. Select the "Arduino Due (Programming Port)" board and the corresponding port.
6. Upload the sketch to the board.

## Running the Agent

On the PC, source your ROS 2 environment and start the micro-ROS agent:

```bash
source /opt/ros/humble/setup.bash
ros2 run micro_ros_agent micro_ros_agent serial --dev /dev/ttyACM0
```

When the board resets, it will connect to the agent. You can then use standard ROS 2 tools (for example `ros2 topic echo /micro_ros/publisher`) to interact with data from the Arduino Due.

## Notes

- If communication fails, ensure the baud rate in the sketch matches the agent settings.
- The same steps should work on Ubuntu 24.04 using ROS 2 Jazzy.
