Franka Emika
============

.. _franka ros: https://github.com/frankaemika/franka_ros
.. _documentation: https://frankaemika.github.io/docs/

Cartesian trajectories for Franka Panda

.. table:: Vendor specifics

  =================================   =======================================
  Programming / simulation software   Franka Desk, ROS
  Software                            Franka Control Interface (FCI)
  User interface                      Franka Desk
  Programming language                C++
  Relevant hardware                   Panda with libfranka 0.7.1
  =================================   =======================================
  
**Further reading**
  * `franka ros`_
  * `documentation`_
  
Trajectory composition
----------------------
Franka has a dual way of programming its panda robot:
First, there's the graphical-based programming using a browser interface (Desk) in which
users compose tasks with different apps. The apps range from simple to complex
and can be customized and shared with a community. Some apps are payed,
limiting this overview to the default available functionality.

* Linear Cartesian motions
   **Cartesian Motion**

   * Move linearly along a list of points, allowing to set blending, velocity and acceleration.

* Additional
   **Relative Motion**
 
   * Move relative to the current pose in a direction specified as vector.
  
   **Line**

   * Move along a line with specified velocity for a certain duration.  The direction vector is taught with two points.
  
   **Lissajous Figures**

   * Realize lissajous figures in a plane. Allows to specify motion and amplitude in both directions.
  
   **Spiral**

   * Move in a spiral pattern in a plane. Provides configuration parameters, such as duration, width, etc.

The second way to realize Cartesian trajectories is directly over the Franka Control
Interface (FCI) (Research) that provides full control of the robot in form of
motion executors

* **joint position**
* **joint velocity**
* **Cartesian pose**
* **Cartesian velocity**

and controllers

* **External controller**: Users can command torques directly to the robot
* **Internal joint impedance**: Implicitly handled without user commands
* **Internal Cartesian impedance**: Implicitly handled without user commands

Motion executors and controllers can be combined to realize hybrid behavior.

Additionally, the robot has a ROS-control interface that uses FCI and
provides users with a base for own ROS controller developments.
According to the hardware interface conventions of ROS-control, Franka offers:

Joint-based interfaces for reading and writing

* **hardware_interface::VelocityJointInterface**
* **hardware_interface::PositionJointInterface**
* **hardware_interface::EffortJointInterface**

and custom Cartesian interfaces for reading and writing with

* **franka_hw::FrankaPoseCartesianInterface**
* **franka_hw::FrankaVelocityCartesianInterface**

All interfaces operate on a 1kHz cycle.

When using these research interfaces, users have the freedom (and necessity) to
implement motion types (e.g. with ROS controllers) on top of raw target and feedback signals
from the robot.

Waypoint representation
-----------------------
For users not having access to Franka's Desk environment, it's not straight forward to identify the waypoint representation.
However, at least on the research interface, a full robot state is available.
Subsets of this state could potentially be saved as waypoints when writing own motion commands.

Apart from the last commanded values, the state contains

.. code-block:: yaml

  joint level signals:
     - motor angles
     - motor angle derivatives
     - joint angles
     - joint angle derivatives
     - joint torque
     - joint torque derivatives
     - estimated external torque
     - joint collisions/contacts

  Cartesian level signals:
     - Cartesian pose
     - load parameters
     - external wrench
     - Cartesian collision


Trajectory parameterization and execution
-----------------------------------------
Since Franka's FCI allows users to implement any desired behavior themselves,
the following list is limited to the possible configurations available for the
apps-based approach.

Specification of velocity
~~~~~~~~~~~~~~~~~~~~~~~~~

   - In form of a percentage of the robot's maximal velocity. Is done when
     configuring instances of motion types

Specification of acceleration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   - Also in form of a percentage of the maximal values. Is parameterized
     during setup of the motion types

Blending
~~~~~~~~

   - Can be configured in the **Cartesian Motion** app

Parallel IO operations
~~~~~~~~~~~~~~~~~~~~~~

   - Specific apps trigger operations, such as **Modbus Wait**, **Modbus Out** and **Modbus Pulse**

Online (real-time) trajectory modifications
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   - Can be achieved implicitly through active impedance control and additional forces set with **Apply Force**
   - External forces and commanded forces can overlay Cartesian motion types and alter the trajectories
