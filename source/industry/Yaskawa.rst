Yaskawa
=======

.. _operator's manual (YRC1000): https://www.motoman.com/getmedia/C92FC6B8-A188-496A-89B1-7B9B76B9A903/178645-1cd.pdf.aspx?ext=.pdf
.. _inform language (YRC1000): https://www.motoman.com/getmedia/346F8450-7888-448E-A145-6BAA3B894B74/178649-1CD.pdf.aspx?ext=.pdf
.. _on blending: https://www.youtube.com/watch?v=OY0ABhVj1dQ
.. _on circles and splines: https://www.youtube.com/watch?v=XEN7DoR-CG0

Cartesian trajectories for the Yaskawa Motoman robots

.. table:: Vendor specifics

  =================================   =======================================
  Teach pendant                       Teach panel (programming pendant)
  Programming / simulation software   Partly PLC support
  Software                            Customization of teach pendant: C++ and C#
  Programming language                INFORM
  Relevant hardware                   Controllers YRC1000, DX100, NX100 and probably others.
  =================================   =======================================

**Further reading**
  * `operator's manual (YRC1000)`_
  * `inform language (YRC1000)`_
  * `on blending`_
  * `on circles and splines`_

Trajectory composition
----------------------
Teaching trajectories is done on the basis of a point-2-point approach with different motion (interpolation) types:

* Linear Cartesian motions
   **MOVL**
   
   * For linear motion to the point

* Circular motions
   **MOVC**
   
   * For circular motion with three points for each arc

* Joint space interpolation
   **MOVJ**
   
   * For joint-interpolated motion to the point

* Additional
   **MOVS**
   
   * For spline motion with parabolic interpolation between three points

   **IMOV**
   
   * For incremental linear motion, starting from a point

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

Specification of velocity
~~~~~~~~~~~~~~~~~~~~~~~~~

   - Point-level: Execution speeds are taught on a per-point basis, applied from
     the previous to the current point, respectively. As an example, the speed
     taught at point P2 is applied from point P1 until P2.  Users can also
     adjust speed as a percentage to the trajectory-global play speed.
   - Trajectory-global: Users can choose and adjust different execution speeds
     on a discrete scala with the **SPEED** instruction

Specification of acceleration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   - Point-level: Users can adjust acceleration **ACC** and deceleration **DEC** as
     *adjustment ratios* in the range of 20% - 100% for each point

Blending
~~~~~~~~

   - Is done with setting a position level (PL) incrementally for **MOVL**
     points, with PL from 0 = no bending radius to 8 = max. bending radius

Parallel IO operations
~~~~~~~~~~~~~~~~~~~~~~

   - Due to synchronous execution, users apply IO operations immediately before/after move instructions.

Online (real-time) trajectory modifications
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   - No information found so far
