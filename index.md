## Small scale localization using IR

### Abstract
We will explore the possibility of using IR beacons for small-scale relative localization between objects. Each node will have one IR emitter beacon and multiple IR receivers. The beacons will each be equipped with BLE and IMU from an Arduino Nano 33 BLE Sense. These beacons will coordinate blinking patterns over BLE to avoid crosstalk. To generate a symmetric light pattern, we will use a card or irregular lens to scatter the light in a uniform pattern around the beacon. Since each beacon will have an IMU we will use this to improve localization accuracy and determine directions of motion. If we have time, we will also explore using a version of IMU dead reckoning to improve localization accuracy.

### Main Project Components
1.       Hardware: This project includes a relatively small custom hardware design that we will complete this week so we can order parts early.
2.       Sensor Calibration: IR emitters and receivers will require calibration to gather distance and direction values as soon as the hardware arrives.
3.       BLE Synchronization: Since we intend to have independent “equal” nodes, we will need to work out synchronizing the beacons without a clear master node.
4.       IMU Dead Reckoning Localization: We will try to implement a version of IMU dead reckoning based on existing literature.
5.       Data Fusion: We will use data from sensor distances and IMU to calculate relative locations of the nodes.

### Plan to Maintain Safe Social Distancing
Since the IR beacons will be relatively inexpensive, we plan to design and order enough of those that we will not need to work together in person. We will design and order the boards and parts for these early with the intent for each of us to have components totaling 5 beacons. In addition to the 2 Arduino Nano 33 BLE Sense modules we own, we will be able to borrow 3 more, so we will only need to purchase one more to have 3 each. This will give us access to 3 BLE/IMU modules to go with 5 IR beacon nodes. We will share code and hardware designs on github and use Zoom for brainstorming and troubleshooting meetings.

### Methods

### Analysis

### Limitations

### Future Improvements


