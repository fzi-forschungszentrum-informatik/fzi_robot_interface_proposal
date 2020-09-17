.. _Doosan:

Doosan
======

.. _manuals after free registration: https://robotlab.doosanrobotics.com/en/Join/normalSignUp
.. _ros_github: https://github.com/doosan-robotics/doosan-robot
.. _ros_manual: http://wiki.ros.org/doosan-robotics?action=AttachFile&do=get&target=Doosan_Robotics_ROS_Manual_ver0.971_20200218A%28EN.%29.pdf

Cartesian trajectories for Doosan robots

.. table:: Vendor specifics

  =================================   =======================================
  Teach pendant                       Teach Pendant
  Programming / simulation software   Teach Pendant + DART platform on user PC
  Software                            FlexPendant SDK, Microsoft CE + Visual Studio
  Programming language                DRL
  Relevant hardware                   Robots M0609, M1509, M1013 and M0617
  =================================   =======================================

Doosan robots support a ROS interface and wrap many of the DRL functionalities with ROS services.

**Further reading**
  * `ros_github`_
  * `ros_manual`_
  * `manuals after free registration`_

Trajectory composition
----------------------
The teaching of waypoints is done either in jog-operation or hand-guiding operation.
Doosan programming features a combination of basic instructions with a skill-based task composition.


* Linear Cartesian motions
   **movel**
   
   * Move tool linearly to a specified target.
   * Possible arguments: point, velocity, acceleration, time, blending radius, and others

* Circular motions
   **movec**
   
   * Move in an arc via a point to a target point.
   * Possible arguments: point, point, velocity, acceleration, time, blending radius, and others

* Joint space interpolation
   **movej**
   
   * Move to the specified joint position.
   * Possible arguments: target joint angles, velocity, acceleration, time, and others. Note that this command uses the different target type **posj**.

   **movejx**
   
   * Move to the specified point with joint interpolation. Similar to
     **movel**, but without the guarantee of a linear motion result in
     Cartesian space.
   * Possible arguments: point, velocity, acceleration, time, radius for
     blending, and others. Additionally, users specify the solution space with
     a three-bit flag, representing shoulder (lefty vs righty), elbow (below vs
     above) and wrist (flip vs no flip).

   **movesj**
   
   * Move along a spline curve path with joint interpolation, connecting
     various joint-based waypoints.
   * Possible arguments: list of joint positions, velocity, acceleration, time,
     and others.

* Additional
   **movesx**
   
   * Move along a spline curve from the current point to the target via waypoints.
   * Possible arguments: List of points, velocity, acceleration, time, and others.

   **moveb**
   
   * Move along a list of path segments (lines, circles) with constant
     velocity. Segments are blended.
   * Possible arguments: list of points, velocity, acceleration, time, and
     others.

   **move_spiral**
   
   * Motion along a spiral trajectory on a plane, which is perpendicular to a specified axis.
   * Possible arguments: Revolutions, final spiral radius, and others

   **move_periodic**

   * Sine-based motion per axis.
   * Possible arguments: Amplitude, period, and others

All move commands have an asynchronous variant, e.g. **amovel** corresponds to
**movel**, that allows the user to run other commands in parallel, i.e. the
main thread continues executing instructions. The blending parameter is not
available for these asynchronous move commands. Triggering concurrent motion
commands is caught with errors.

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

Specification of velocity
~~~~~~~~~~~~~~~~~~~~~~~~~

   - In form of a speed argument to move instructions. The speed is valid until
     the next point
   - Global adjustments of task space velocity with the **set_velx** function.
     This value will be used as default for **movel, movec** and **movesx** if
     nothing is specified.
   - Global, trajectory-wide setting with **change_operation_speed** as a
     percentage of the current speed setting

Specification of acceleration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   - In form of a max acceleration argument to move instructions.
   - Global adjustments of task space acceleration with **set_accx**. This is also
     taken as default for the move commands **movel, movec** and **movesx**.

Blending
~~~~~~~~

   - Can be started and stopped with **begin_blend** and **end_blend**,
     respectively. Upon activation, all points get blended during execution.
   - Alternatively, segments can be executed with **moveb**, which is a constant velocity
     blending motion for a path of given move segments.

Parallel IO operations
~~~~~~~~~~~~~~~~~~~~~~

   - I/O operations are managed independently of trajectory execution
   - Users can trigger them e.g. with the asynchronous move instructions for individual segments.

Online (real-time) trajectory modifications
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   - Supports compliant trajectory execution, in which preference is given to
     force control over motion control for in-contact tasks
   - Trajectories can be modified with the threaded **alter_motion** function
     with a cycle time of 100ms.
