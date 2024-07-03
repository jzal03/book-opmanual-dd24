# Reference frames

Different reference frames are used by ROS and BetaFlight (i.e. by the software running on the Raspberry Pi and the one on the flight controller respectively).

## Betaflight

The reference frame used by the BetaFlight firmware is as following[^px4-docs]:

![Betaflight Reference Frame](../_images/software-architecture/px4-betaflight-ref-frame.png)

## ROS

ROS uses the reference frames conventions defined in [REP 105](https://www.ros.org/reps/rep-0105.html). Specifically for mobile robots (and thus the Duckiedrone) the _preferred_ axis orientation is defined in [REP 103](https://www.ros.org/reps/rep-0103.html) as having the _body axis_ as:

- `x` forward
- `y` left
- `z` up.

In the image below [^px4-docs] you can see a comparison between the two reference frame conventions.

![Comparison between the reference frames used in ROS and px4/betaflight](../_images/software-architecture/px4-vs-ros-ref-frames.png)

[^px4-docs]: [Source px4-docs](https://docs.px4.io)