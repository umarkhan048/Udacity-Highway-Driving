# CarND-Path-Planning-Project

## Reflection
   
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

After the lane for each vehicle object provided by the sensor fusion is determined, the situation around the ego vehicle is updated. If a vehicle is in the same lane as ego vehicle, it is checked that if the detected vehicle object is in front of the ego vehicle and if the distance between the detected vehicle object in ego lane and the ego vehicle is less than the parameter SAFE_DIST. The SAFE_DIST parameter is a tuning parameter. It defines the minimum distance between ego vehicle and any other vehicle which must be there before performing any lane change maneuver. If the conditions hold true, the vehicle_front flag is set to true. This information is later used to decide which lane to change to. Similar checks are applied for left and right lanes and the corresponding flags are set. The implementation can be found in between lines 144 to 163 of main.cpp.

After looping over all the sensor fusion objects and analysing the situation as explained above, it is clear for the current cycle, which lanes are free and which lanes have other vehicles inside or outside of the safe distance. After this, based on the information available, it is decided if the ego vehicle should simply slow down because there is a vehicle in front and changing lanes is unsafe, or if the ego vehicle should change to left or right lane. The implementation is shown below:

```
if (vehicle_front) {
  if (!vehicle_left && lane > 0) {
    lane--;
  }
  else if (!vehicle_right && lane != 2) {
    lane++;
  }
  else {
    speed_diff -= MAX_ACC;
  }
}
else {
  if (ref_vel < MAX_VEL) {
    speed_diff += MAX_ACC;
  }
}
```

## Limitations

The following limitations are recognized in this implementation:

1. If there is a vehicle in the ego lane and vehicle objects at the left and right too, which makes it unsafe to change lanes, the ego vehicle accelerates and decelerates constantly, until the lane changes becomes safe. This can be improved by simply reduce the reference velocity of the ego vehicle to that of the vehicle in front.

2. Currently, the implementation is such that that when there is a vehicle in front of the ego vehicle and there is another vehicle in the left lane just outside the safe distance, even if the right lane is completely free, the ego vehicle changes lane in the left direction, although a better candidate is the right lane. However, as a rule, if overtaking from right side is not allowed, then this behavior is correct.
