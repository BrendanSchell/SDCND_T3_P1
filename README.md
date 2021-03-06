# CarND-Path-Planning-Project
Self-Driving Car Engineer Nanodegree Program

## Reflection

### Use of Frenet Coordinates

The car in this project will exactly follow the x and y coordinates that are
passed to it. Frenet coordinates are used throughout the path planner because
they are easier to work with. The s and d coordinates are calculated using
helper functions and then converted back to x and y coordinates after for use in the simulator.

The car is able to stay within specific lanes using the d coordinate (width across
the road). The s coordinate is used to be able to determine the distance along
the road and is useful for determining distance between the ego vehicle and
other vehicles.

### Path Planning

The vehicle looks through sensor fusion data of all vehicles on the right-hand
side of the road and produces a cost for each lane based on the following:

* Distance from right lane (right lane is desired)
* Distance from current lane (changing lanes discouraged unless necessary)
* Distance to nearest vehicle in lane (further vehicle reduces cost)
* Average speed of vehicles ahead of current vehicle in lane (further from reference velocity is worse)

Once the cost is calculated for each lane, the lane with the smallest cost
is set to the desired lane. The lane is then assessed for other vehicles in
close proximity to determine if it is safe. If it is safe, the vehicle will
switch in the direction of the desired lane. In order to reduce jerk if 
switching across two lanes, the vehicle waits at least 10 iterations before
switching two lanes. In order to avoid the vehicle switching lanes without
a safe velocity, lane changes are only performed above 30mph.

Finite states of keep lane, perform lane changes, and prepare for lane changes
are used in this path planner. They are simplified in that they do not involve 
target velocities with acceleration / deceleration in this iteration. 

### Path Smoothing

In order to produce a smooth trajectory, points from the previous path that 
have not yet been visited are included. Equally spaced points in the s horizon 
are used to determine the trajectory. A spline is then fit to these future 
points to smooth the trajectory. Points along this spline are chosen 
that will be within safe acceleration limits and finally converted to x, y
coordinates for use in the simulator.


### Results

The car is able to successfully navigate along the highway without colliding
or exceeding jerk requirements. In testing, the path planner is able to run
for more than 30 minutes without incidents. Though it performs well, the path planner
is not always optimal and is often overcautious with changing lanes. There can of course
be unanticipated simulator behaviour which results in near-misses.

### Future Improvements

The following can be done to improve the path planner further:

* Following target velocities of different vehicles for reduced acceleration changes
* Using polynomial fitting for jerk minimizing paths
* Predicting other vehicles' intentions using a classification algorithm such as Naive Bayes
* More precise definitions of finite states which might result in smoother and less overtly cautious lane changing.


   
### Simulator. You can download the Term3 Simulator BETA which contains the Path Planning Project from the [releases tab](https://github.com/udacity/self-driving-car-sim/releases).

In this project your goal is to safely navigate around a virtual highway with other traffic that is driving +-10 MPH of the 50 MPH speed limit. You will be provided the car's localization and sensor fusion data, there is also a sparse map list of waypoints around the highway. The car should try to go as close as possible to the 50 MPH speed limit, which means passing slower traffic when possible, note that other cars will try to change lanes too. The car should avoid hitting other cars at all cost as well as driving inside of the marked road lanes at all times, unless going from one lane to another. The car should be able to make one complete loop around the 6946m highway. Since the car is trying to go 50 MPH, it should take a little over 5 minutes to complete 1 loop. Also the car should not experience total acceleration over 10 m/s^2 and jerk that is greater than 50 m/s^3.

#### The map of the highway is in data/highway_map.txt
Each waypoint in the list contains  [x,y,s,dx,dy] values. x and y are the waypoint's map coordinate position, the s value is the distance along the road to get to that waypoint in meters, the dx and dy values define the unit normal vector pointing outward of the highway loop.

The highway's waypoints loop around so the frenet s value, distance along the road, goes  from 0 to 6945.554.

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./path_planning`.

Here is the data provided from the Simulator to the C++ Program

#### Main car's localization Data (No Noise)

["x"] The car's x position in map coordinates

["y"] The car's y position in map coordinates

["s"] The car's s position in frenet coordinates

["d"] The car's d position in frenet coordinates

["yaw"] The car's yaw angle in the map

["speed"] The car's speed in MPH

#### Previous path data given to the Planner

//Note: Return the previous list but with processed points removed, can be a nice tool to show how far along
the path has processed since last time. 

["previous_path_x"] The previous list of x points previously given to the simulator

["previous_path_y"] The previous list of y points previously given to the simulator

#### Previous path's end s and d values 

["end_path_s"] The previous list's last point's frenet s value

["end_path_d"] The previous list's last point's frenet d value

#### Sensor Fusion Data, a list of all other car's attributes on the same side of the road. (No Noise)

["sensor_fusion"] A 2d vector of cars and then that car's [car's unique ID, car's x position in map coordinates, car's y position in map coordinates, car's x velocity in m/s, car's y velocity in m/s, car's s position in frenet coordinates, car's d position in frenet coordinates. 

## Details

1. The car uses a perfect controller and will visit every (x,y) point it recieves in the list every .02 seconds. The units for the (x,y) points are in meters and the spacing of the points determines the speed of the car. The vector going from a point to the next point in the list dictates the angle of the car. Acceleration both in the tangential and normal directions is measured along with the jerk, the rate of change of total Acceleration. The (x,y) point paths that the planner recieves should not have a total acceleration that goes over 10 m/s^2, also the jerk should not go over 50 m/s^3. (NOTE: As this is BETA, these requirements might change. Also currently jerk is over a .02 second interval, it would probably be better to average total acceleration over 1 second and measure jerk from that.

2. There will be some latency between the simulator running and the path planner returning a path, with optimized code usually its not very long maybe just 1-3 time steps. During this delay the simulator will continue using points that it was last given, because of this its a good idea to store the last points you have used so you can have a smooth transition. previous_path_x, and previous_path_y can be helpful for this transition since they show the last points given to the simulator controller with the processed points already removed. You would either return a path that extends this previous path or make sure to create a new path that has a smooth transition with this last path.


## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets)
  * Run either `install-mac.sh` or `install-ubuntu.sh`.
  * If you install from source, checkout to commit `e94b6e1`, i.e.
    ```
    git clone https://github.com/uWebSockets/uWebSockets 
    cd uWebSockets
    git checkout e94b6e1
    ```

