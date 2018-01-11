# CarND-Controls-MPC
Model Predictive Control Project for Udacity Self-Driving Car Engineer Nanodegree Program

---

## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1(mac, linux), 3.81(Windows)
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
    Some function signatures have changed in v0.14.x. See [this PR](https://github.com/udacity/CarND-MPC-Project/pull/3) for more details.

* **Ipopt and CppAD:** Please refer to [this document](https://github.com/udacity/CarND-MPC-Project/blob/master/install_Ipopt_CppAD.md) for installation instructions.
* [Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page). This is already part of the repo so you shouldn't have to worry about it.
* Simulator. You can download these from the [releases tab](https://github.com/udacity/self-driving-car-sim/releases).
* Not a dependency but read the [DATA.md](./DATA.md) for a description of the data sent back from the simulator.


## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./mpc`.

## Project Files

The cpp files modified for this project are `main.cpp` and `MPC.cpp`.  The first runs the web server that connects to the simulator and calls the latter, to contain and perform the model predictive controller.

## MPC Controller C++ Code

Most of the code needed for this project was easily adaptable from the MPC quiz in the prior lesson.  Some of the equations needed to take more into account and therefore were more advanced and needed to more closely reflect the given equations for an MPC.

## Reflections

One of the largest improvements of the performance of the MPC came from the added weights to the cost calculations in the `operator()` function of `MPC.cpp`.  Once I had some added weight to the various parts of the CTE I started to see more predictable behavior in the simulator.

I didn't experiment too much with latency, I never changed from the default 100 latency value, because I didn't want to spend too much time working on getting a good quality MPC that didn't satisfy the project requirements. Response latency was primarily dealt with by reducing the N and increasing the dt hyperparameters to 10 and 0.1 respectively.  The simulator ran much better than the starting values I had of 25 and 0.05 from the lesson.  Bringing these values into play made the car get successfully around the track although immediately after one lap it would veer off course.  For this I visited the forums to see if anyone had a similar issue.

Upon researching issues other people were having I found a discussion about the CTE calculations and whether it contained a typo or not.  I tried several different equation modifications proposed and found that one of the recommendations solved my issue and further reduced wobble and sway.  Changing the equation from this:

    `epsi1 - ((psi0 - psides0) + v0 * delta0 / Lf * dt);`

    to this:

    `epsi1 - ((psides0 - psi0) + v0 * delta0 / Lf * dt);`

was all it took.

I also limited the speed to 50 just to keep it consistently on the track, but had experimented with it as high as 80 with some success.  Will continue to tweak the project working towards getting to 100mph.  I may need to come up with a way to decrease throttle in preparation for sharp turns by taking the line curvature into consideration more heavily.
