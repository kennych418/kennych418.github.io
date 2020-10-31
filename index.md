## Small scale localization using IR

###
[Source Code Github Link](https://github.com/kennych418/IR_Localization/tree/master)

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

### Project Proposal
With the increase in IoT devices hitting the market, there is a rising need for orientation tracking between many mobile or wearable devices. However, traditional localization methods either require stationary beacons or have limited accuracy in a smaller, more personal scale. 

We will build a system that resolves constraints by tracking relative position based on mobile IR beacons. The beacons will contain identical hardware and be based around the Arduino Nano 33 BLE Sense. These beacons triangulate relative position based on the received IR intensity and the locations of the other beacons. To avoid crosstalk, the beacons will flash in turn in an order coordinated over BLE as shown in Figure 1. Although a standard approach based on distances alone would use 3 stationary beacons and a single moving device, we will attempt to use only 3 devices and handle the additional degrees of freedom using each device’s IMU and/or angle measurements.

![3Beacons](../Pictures/3pics.png)
**Figure 1**: IR Beacons. We will calculate distance based on IR intensity at the receiving beacons.

Our beacon hardware design will include three upward-facing wide-angle IR phototransistors arranged around a single wide-angle IR LED as seen in Figure 2. We chose three phototransistors so that each device can calculate a rough angle measurement as shown in Figure 3. Additionally, each device will have an IMU for orientation tracking. If we are able to determine the angle with a reasonable degree of accuracy, we can leverage the IMU’s magnetometer to calculate a heading and track each device’s angle from one another. Doing so will remove a degree of freedom and reduce the minimum number of nodes from 4 to 3, matching the requirements of the traditional 3 stationary beacon localization.

![PCB](../Pictures/PCB.png)
**Figure 2**: High Level Beacon Design

![Angle](../Pictures/Angle.png)
**Figure 3**: Angle Measurement With Two Receivers on a Single Beacon

To generate a symmetric distribution of light emitted from the beacon, we will use a card of some sort suspended over the emitter to reflect and scatter light as in Figure 4. After assembling the hardware, we will test a couple different ideas to reflect and scatter light. The simplest idea is to simply cut an index card into a circle, but we could also try a cone shape or try attaching aluminum foil to the index card to increase reflection.

![Reflect](../Pictures/Reflect.png)
**Figure 4**: IR Led with Reflector.

Since we are constrained to a small number of beacons due to the cost of additional nanos, we will use the IMU on the Arduino Nano 33 BLE Sense to detect which beacons are moving and will need to update their positions and to implement a dead reckoning system that will provide redundancy and allow us to detect and handle errors in IR distances.

### Deliverables
1. Each beacon can measure distances from other beacons.<br>
2. As a beacon moves, its position updates correctly on a relative coordinate system.<br>
3. Comparison of IMU results and IR beacon results.<br>
4. Improved localization using both IMU and IR beacon results.<br>

### Analysis of Previous Literature
There is a large amount of prior work in the realm of indoor localization using IR beacons, but our intended approach of using beacons with changing positions to determine relative positioning seems to be somewhat new.

Indoor IR localization has been used in past work for navigation for robots and UAVs (1, 4, 5). Most of these works use some form of stationary beacons or markers and they range from using a very small number of sophisticated emitters and receiver devices to using a large number of simple markers for navigation. The simplest technique to determine location is to triangulate using distances from known points (2, 3) but there is one source that used servo motors and highly directional receivers to determine angles to a flying object (1). Since our goal is to determine relative location in a 2D plane without adding moving parts to our beacons, we will use a technique similar to the first method with a few added complications due to making the beacons mobile instead of stationary. To use emitter beacons for triangulation, signals from the beacons need to be distinguished from each other. The IR beacons can flash in coded patterns or with different frequencies so they can be separated using signal processing (2, 3). Since our beacons will have BLE, we are planning to coordinate blinking order over BLE to avoid signal processing overhead.

One of the challenges that differentiates our project from other IR localization projects is that we are intending to update relative locations from mobile beacons and each node will both be a beacon and a device that is being tracked. Similar problems to these are addressed in a thesis by Andreas Biri from ETH Zurich using UWB for relative localization (9). This paper describes multidimensional scaling and ways to decrease errors with multiple moving nodes. One of the conclusions from this work is that accuracy will improve significantly when using more than 4 nodes, since this will provide redundancy and correct for errors. Since we are planning to use a smaller number of nodes, we will use the IMU to determine whether a node is moving or stationary and for dead reckoning location tracking to provide redundancy.

In addition to tracking the distance between nodes through IR, we can give each node device an IMU to measure their motion and orientation. Without an accurate heading measurement, we would need 4 or more nodes to track their relative positions. Including an IMU reduces that to 3 nodes with our proposed localization method.

To further utilize the IMU and improve our accuracy, we can use the RoNIN navigation method developed by Sachini Herath to get a second estimation of each node’s positioning (10). The RoNIN method uses a trained neural network to read IMU measurements and track the device’s position over a long period of time. Implemented into our system, we could combine the position data provided by the neural network with our IR data to get a better estimate of each node’s relative position. However, it may be difficult to port the full RoNIN neural network over to our system. Additionally, it will most likely have a high latency on our Arduino platform. Therefore, we plan to use the RoNIN method as an inspiration basis and explore other differential inertial measurement dead reckoning methodologies that use less resources and operate in real time.  

BLE communication between multiple transmitting and receiving devices is well established and used in many real world applications (6). Additionally, the protocol was designed to minimize energy consumption without introducing significant latency (7). This makes it ideal for our embedded system, which will be powered by batteries and calculating positions in real time. For our system, we will use the ArduinoBLE library to set up our network(8). With this library, each BLE device can simultaneously act as a central and peripheral device, forming a completely connected network. Any two devices in the network can transmit and receive to each other by subscribing and posting to their bulletin boards.  

Citation:

(1) Kirchner, Nathan. Infrared Localisation for Indoor UAVs. Research Gate, 2005, researchgate.net/publication/252825877_Infrared_Localisation_for_Indoor_UAVs. 

(2) Eric Brassart GRACSY. Groupe de Recherche sur l'Analyse et la Commande des SYstèmes, et al. “Localization Using Infrared Beacons.” Robotica, 1 Mar. 2000, dl.acm.org/doi/10.1017/S0263574799001927. 

(3) Raharijaona, Thibaut, et al. “Local Positioning System Using Flickering Infrared LEDs.” MDPI, Multidisciplinary Digital Publishing Institute, 3 Nov. 2017, www.mdpi.com/1424-8220/17/11/2518/htm.

(4) K R Shankar, et al. A Review on IR Beacon Based Mobile Robot Localization - IEEE Conference Publication, IEEE, 2017, ieeexplore.ieee.org/document/7931898.

(5) Lee, Jae-Y, et al. A Real-Time Localization System Based on IR Landmark for Mobile Robot in Indoor Environment. Journal of Institute of Control, 2006, www.researchgate.net/publication/315692392_A_Real-time_Localization_System_Based_on_IR_Landmark_for_Mobile_Robot_in_Indoor_Environment.

(6) Guo, Zonglin, et al. An on-Demand Scatternet Formation and Multi-Hop Routing Protocol for BLE-Based Wireless Sensor Networks. IEEE, 2015, ieeexplore.ieee.org/abstract/document/7127705.

(7) Treurniet, Jan Jaap, et al. Energy Consumption and Latency in BLE Devices under Mutual Interference: An Experimental Study. IEEE, 2015, ieeexplore.ieee.org/abstract/document/7300836.

(8) “ArduinoBLE.” Arduino, www.arduino.cc/en/Reference/ArduinoBLE.

(9) Andreas, Biri. Localizing Mobile Nodes in a Relative Coordinate System. Institute of Information Security, 2017, n.ethz.ch/~abiri/download/Theses/abiri_semester_thesis_1.pdf.

(10) Herath, Sachini, et al. RoNIN: Robust Neural Inertial Navigation in the Wild: Benchmark, Evaluations, & New Methods. IEEE, 2020, ieeexplore.ieee.org/abstract/document/9196860?casa_token=4WmmRORA2QQAAAAA:iOOFTQS93sPSI0iWvwYh9oqWRfsBjfUxqikYEWXALpCwhNK25Lx-uWKcr2RJIAN2YCjV8mT0pwvp.

### Project Timeline
**Week 5:**<br>
*Goals:* Finish Schematic. Order components. BLE communication between devices. Time synchronization using BLE.<br>
*Critical:* All hardware orders must be complete as early in the week as possible.<br>

**Week 6:**<br>
*Goals:* Access IMU. Implement dead reckoning and detection of motion. Implement a 2D location visualization app of some sort on the computer. Assemble and test devices when they arrive. Exchange parts.<br>
*Critical:* BLE Communication between devices must be reliable. Time synchronization must be reliable and extend to multiple devices.<br>

**Week 7:**<br>
*Goals:* Assemble all node devices. Model IR intensity as a function of distance. Test scattering reflector for IR emitter. Verify that the IMU, IR transmitters, and IR receivers work.<br>
*Critical:* Must have assembled hardware.<br>

**Week 8:**<br>
*Goals:* Completed project by the end of this week. Present preliminary results to the class.<br>
*Critical:* Verify that the nodes can track the distance from each other. Write the code that will calculate their relative position from one another.<br>

**Week 9:**<br>
*Goals:* Comment code. Write report. Remove any bugs. Polish the software and interface. Make website look clean. Name for project.<br>
*Critical:* Must have complete project with working hardware.<br>

**Week 10:**<br>
*Goals:* Record and edit demo video.<br>
*Critical:* Demo video. Project website. Github. Cleaned code.<br>

### Limitations

### Future Improvements


