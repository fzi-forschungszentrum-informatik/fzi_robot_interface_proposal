Yaskawa
=======
* Description: Cartesian trajectories for the Yaskawa Motoman robots
* Graphical programming interface: Teach panel (programming pendant)
* Vendor specific programming language: INFORM
* Newest controllers, such as the YRC1000 have PLC (Rockwell Automation Programmable Logic Controllers) support
* For customization of the teach pendant: C++ and C# 
* Version of the user manual: Operator's manual (YRC1000):
* Link to manual: https://www.motoman.com/getmedia/C92FC6B8-A188-496A-89B1-7B9B76B9A903/178645-1cd.pdf.aspx?ext=.pdf

.. The Yaskawa motoman family has various controllers:
.. https://www.motoman.com/en-us/products/controllers

.. MOVC and MOVS explained:
.. https://www.motoman.com/en-us/about/blog/5-ways-to-program-a-robot
.. https://www.youtube.com/watch?v=XEN7DoR-CG0

.. Blending explained:
.. https://www.youtube.com/watch?v=OY0ABhVj1dQ

Trajectory composition
----------------------
Teaching trajectories is done on the basis of a point-2-point approach with different motion (interpolation) types:

* **MOVL** for linear motion to the point
* **MOVC** for circles (builds circles with three points for each arc)
* **MOVS** for spline motion (does parabolic interpolation between three points)
* **IMOV** for incremental linear motion, starting from a point
* **MOVJ** for joint-interpolated motion to the point

There are also application specific routines that help in the process of trajectory programming while teaching points.
These routines may include additional sensors, such as force-torque sensors for approaching surfaces or contour following.
The final obtained trajectories, however, are still built with basic motion types.

Waypoint representation
-----------------------
Individually taught points have the following representation:

.. code-block:: yaml

  X
  Y
  Z
  Rx (roll)
  Ry (pitch)
  Rz (yaw)

Different coordinate systems for point representations are possible.

Trajectory parameterization and execution
-----------------------------------------
* Specification of velocity
   - Point-level: Execution speeds are taught on a per-point basis, applied from
     the previous to the current point, respectively. As an example, the speed
     taught at point P2 is applied from point P1 until P2.  Users can also
     adjust speed as a percentage to the trajectory-global play speed.
   - Trajectory-global: Users can choose and adjust different execution speeds
     on a discrete scala with the **SPEED** instruction

* Specification of acceleration
   - Point-level: Users can adjust acceleration **ACC** and deceleration **DEC** as
     *adjustment ratios* in the range of 20% - 100% for each point

* Blending
   - Is done with setting a position level (PL) incrementally for **MOVL**
     points, with PL from 0 = no bending radius to 8 = max. bending radius

* Parallel IO operations:
   - Due to synchronous execution, users apply IO operations immediately before/after move instructions.

* Online (real-time) trajectory modifications:
   - No information found so far

Features required from hardware
-------------------------------
* Applicable to Yaskawa Motoman robots of the controllers YRC1000, DX100, NX100 and probably others.
* Cartesian position and velocity control interfaces on the robots.
