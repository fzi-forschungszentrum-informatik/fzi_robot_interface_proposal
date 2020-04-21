Doosan
======
* Description: Cartesian trajectories for Doosan robots
* Graphical programming interface: Teach Pendant + DART platform on user PC
* Vendor specific programming language: DRL
* Version of the user manual: Programming Guide Manual Version 1.6
* Link to manual: Available after free registration: https://robotlab.doosanrobotics.com/en/Join/normalSignUp

Trajectory composition
----------------------
The teaching of waypoints is done either in jog-operation or hand-guiding operation.
Doosan programming features a combination of basic instructions with a skill-based task composition.


* **movel**: Move linearly to a specified target. Possible arguments: point, velocity, acceleration, time, blending radius, and others

* **movec**: Move in an arc via a point to a target point. Possible arguments: point, point, same as **movel**

* **movesx**: Move along a spline curve from the current point to the target via waypoints. Possible arguments: List of points, velocity, acceleration, time, and others.

* **move_spiral**: Motion along a spiral trajectory on a plane, which is perpendicular to a specified axis. Possible arguments: Revolutions, final spiral radius, and others

* **move_periodic**: Sine-based motion per axis. Possible arguments: Amplitude, period, and others

Waypoint representation
-----------------------
Individually taught points (type **posx**) have the following field representation:

.. code-block:: yaml

  x
  y
  z
  w (z-direction rotation of reference coordinate system)
  p (y-direction rotation of w rotated coordinate system)
  r (z-direction rotation of w and p rotated coordinate system)

Individual points can be saved in various reference frames.


Trajectory parameterization and execution
-----------------------------------------

* Specification of velocity
   - In form of a speed argument to move instructions. The speed is valid until
     the next point
   - Global adjustments of task space velocity with the **set_velx** function.
     This value will be used as default for **movel, movec** and **movesx** if
     nothing is specified.
   - Global, trajectory-wide setting with **change_operation_speed** as a
     percentage of the current speed setting

* Specification of acceleration
   - In form of a max acceleration argument to move instructions.
   - Global adjustments of task space acceleration with **set_accx**. This is also
     taken as default for the move commands **movel, movec** and **movesx**.

* Blending
   - Can be started and stopped with **begin_blend** and **end_blend**,
     respectively. Upon activation, all points get blended during execution.
   - Alternatively, segments can be executed with **moveb**, which is a constant velocity
     blending motion for a path of given move segments.

* Parallel IO operations
   - I/O operations are managed independently of trajectory execution

* Online (real-time) trajectory modifications
   - Supports compliant trajectory execution, in which preference is given to
     force control over motion control for in-contact tasks
   - Trajectories can be modified with the threaded **alter_motion** function
     with a cycle time of 100ms.

Features required from hardware
-------------------------------
* Applicable to robots M0609, M1509, M1013 and M0617.
* Cartesian position and velocity control interfaces on the robots.
* Cartesian force-torque meassurements and online-adaptation of motion commands

