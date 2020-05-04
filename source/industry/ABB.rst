ABB
===

.. _manual_rapid: https://library.e.abb.com/public/688894b98123f87bc1257cc50044e809/Technical%20reference%20manual_RAPID_3HAC16581-1_revJ_en.pdf

Cartesian trajectories for ABB robots (IRC5 controllers)

.. table:: Vendor specifics

  =================================   =======================================
  Teach pendant                       FlexPendant
  Programming / simulation software   RobotStudio
  Software                            FlexPendant SDK, Microsoft CE + Visual Studio
  Programming language                RAPID
  Relevant hardware                   Robots of the IRC5 controller
  =================================   =======================================

**Further reading**
  * `manual_rapid`_

Trajectory composition
----------------------
Programming is done with move instructions (motion types) that move the robot
to specified targets.

* Linear Cartesian motions
   **MoveL**
   
   * Move linearly to a specified target.
   * Possible arguments: target point, speed, coordinate system, duration until point (replaces speed), and others

* Circular motions
   **MoveC**
   
   * Build circular, open motion arcs, using a via-point and end point.
   * Possible arguments: point on circle, target point, coordinate systems, duration, and others

* Joint space interpolation
   **MoveJ**
   
   * Move the robot to specified points using joint interpolation. All joints
     will reach their destination at the same time.
   * Possible arguments: Target point, speed, zone, tool, and others. The tool
     center point is the point moved to the destination.

Waypoint representation
-----------------------
Individually taught points (type **robtarget**) have the following field representation:

.. code-block:: yaml

  trans
  rot
  robconf
  extax

Individual fields
~~~~~~~~~~~~~~~~~
* **trans**: x, y, z (position of tcp)
* **rot**: q1, q2, q3, q4 (orientation in quaternion notation)
* **robconf**: cf1, cf4, cf6, cfx (axis configuration of the robot for possibly
  ambiguous axes). Each field (integer) indicates the *configuration
  quadrant* for the numbered axis and is counted in positive or negative
  quarter revolutions of 90° starting from zero:

   .. code-block:: yaml
   
     ...
     -3 = axis is in (-270°, -180°)
     -2 = axis is in (-180°, -90°)
     -1 = axis is in (-90°, -0°)
      0 = axis is in (+0°, +90°)
      1 = axis is in (+90°, +180°)
      2 = axis is in (+180°, +270°)
     ...

* **extax**: [eax_a, eax_b, eax_c, eax_d, eax_e, eax_f] (list of up to six external hardware axes)

Different coordinate systems for point representations are possible, such as
world or various object coordinate systems.


Trajectory parameterization and execution
-----------------------------------------

Specification of velocity
~~~~~~~~~~~~~~~~~~~~~~~~~

   - In form of a speed argument to move instructions during teaching of fly-by points. The
     speed is valid until the next point
   - Path segment specific with **VelSet** (overrides global velocity settings
     until reset). Can be either specified as percentage of the current global
     velocity or can be set to become the new max velocity.
   - Global adjustments with **motsetdata**, affects all points

Specification of acceleration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   - Path segment specific accelerations with **AccSet** (overrides global
     acceleration until reset). Provokes slower acceleration and deceleration
     (percentage) of the global settings.
   - Global adjustments with **motsetdata**, affects all points. This also includes
     adjustment of ramping accelerations etc.

Blending
~~~~~~~~

   - Taught positions can either be fly-by points, or stop points
   - During **MoveL**, fly-by points are automatically blended, leading to
     adjusted *corner paths* (parabolas). Stop points are exactly passed.
   - The blending configuration is handled with *zone data* that specifies how corner paths are realized.
   - A parameterization of different zones allows to design corner paths in
     which tool orientation and Cartesian position can be started and stopped
     Individually.

Parallel IO operations
~~~~~~~~~~~~~~~~~~~~~~

   - **MoveLDO**: Move linearly and trigger an I/O operation at the target's middle corner path
   - **MoveCDO**: Move in a circle and trigger an I/O operation at the target's middle corner path

Online (real-time) trajectory modifications
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   - Offsets to paths can be realized with **CorrWrite** and special correction
     generators. No information on real-time capability found.
