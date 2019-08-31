# CarND-Path-Planning-Project
Self-Driving Car Engineer Nanodegree Program
   
The simulator provides the host vehicle odometry data such as lateral and longitudinal positions in x and y coordinates as well as in Frenet coordinates (s and d). Additionally, the yaw rate and longitudinal speed of the ego vehicle is also provided.

The sensor fusion module provides information about other vehicles on the same side of the road. This information contains lateral and longitudinal velocities of the vehicle objects as well as the Frenet coodinates.

On reception of each message from the simulator, the sensor fusion is looped over and for each object in the sensor fusion data, it is determined if the vehicle object is in left, middle or right lane. This is done as follows:

```
// left lane
if (d < 4 && d > 0) {
  vehicle_lane = 0;
}
// middle lane
else if (d < 8 && d > 4) {
  vehicle_lane = 1;
}
// right lane
else if (d < 12 && d > 8) {
  vehicle_lane = 2;
}
else {
  continue;
}
```
