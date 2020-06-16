Universal Robots (UR)
=====================

.. _manual_collection: https://www.universal-robots.com/articles/ur-articles/urscript-dynamic-force-control/
.. _conveyor_tracking: https://www.universal-robots.com/articles/ur-articles/conveyor-tracking-guide/
.. _dynamic_force_control: https://www.universal-robots.com/articles/ur-articles/urscript-dynamic-force-control/


Cartesian trajectories for Universal Robots (CB3 / e-Series).

.. table:: Vendor specifics

  =================================   =======================================
  Teach pendant                       UR+ / URcaps
  Programming / simulation software   
  Software                            
  User interface                      PolyScope
  Programming language                UR Script (similar to Python)
  Relevant hardware                   CB3 (UR3, UR5, UR10), e-Series (UR3e, UR5e, UR10e, UR16e)
  =================================   =======================================
  

**Further reading**
  * `manual_collection`_
  * `conveyor_tracking`_
  * `dynamic_force_control`_
  

Trajectory composition
----------------------
Programming is done with move instructions (movement types) that move the robot to specified targets.

* Linear Cartesian motions
   **MoveL**
   
   * tool moves in a straight line. To keep moving linearly between waypoints each joint performs a more complicated motion. 
   * parameters (pose, a=1.2, v=0.25, t=0, r=0):
   
      * pose: target pose
      * a: tool acceleration in m/s\ :sup:`2`
      * v: tool speed in m/s
      * t: time in s
      * r: blend radius in m

* Circular motions
   **MoveC**
   
   * tool moves on a circular arc segment to from current pose to target pose. Path point `pose_via` defines the arch's shape
   * parameters (pose_via, pose_to, a=1.2, v=0.25, r=0, mode=0):
   
      * pose_via: path point
      * pose_to: target pose
      * a: tool acceleration in m/s\ :sup:`2`
      * v: tool speed in m/s
      * r: blend radius (of target pose) in m
      * mode: (0: Unconstrained / 1: Fixed mode)

      
* Joint space interpolation
   **MoveJ**
   
   * tool moves in a curved path interpolated in joint space. Each joint reaches location simultaneously. Preferred motion if a high TCP speed is desired. 
   * parameters (q, a=1.4, v=1.05, t=0, r=0):
   
      * q: joint positions
      * a: joint acceleration of leading axis in rad/s\ :sup:`2`
      * v: joint speed of leading axisin rad/s
      * t: time in s
      * r: blend radius in m

* Additional
   **MoveP** 
   
   * tool moves linearly with constant speed with circular blends. Command can be extendend by a Circle move consisting of two waypoints. 
   * parameters (pose, a=1.2, v=0.25, r=0):
   
      * pose: target pose
      * a: tool acceleration in m/s\ :sup:`2`
      * v: tool speed in m/s
      * r: blend radius in m




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
  

Trajectory parameterization and execution
-----------------------------------------

Specification of velocity
~~~~~~~~~~~~~~~~~~~~~~~~~

The robot's speed is defined in form of an argument by the move command.
   
    * For *MoveJ* in deg/s:  **maximum joint speed**
    * For *MoveL* in mm/s:  **desired tool speed**
      
Global specification of velocity are done via two safety-related functions:
      
     ===========  ===========================     ===========
     Function     Description                     Limit
     ===========  ===========================     ===========
     Joint speed  Max. angular joint speed        30 ◦/s
     TCP speed    Max. speed of the robot TCP     5000 mm/s
     ===========  ===========================     ===========

   
Specification of acceleration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The acceleration of the robot's motions is defined in form of an argument by the move command. Depending on the chosen movement type either the joints' or TCP's acceleration is definable.

    * For *MoveJ* in deg/s\ :sup:`2`\:  **joint acceleration**
    * For *MoveL* in mm/s\ :sup:`2`\:  **tool acceleration**

      
Blending
~~~~~~~~

* Circular blending is part of **MoveP**. The blend radius' size is by default a shared value between all the waypoints. A smaller blend radius leads to sharper and a biger radius to smoother paths.
* Blending can also be done by defining a blend radius for waypoints. In this case the trajectory blends around the waypoint, allowing the robot arm not to stop at the point.


Parallel IO operations
~~~~~~~~~~~~~~~~~~~~~~

Can be triggered at certain points in the robot’s path

 
Online (real-time) trajectory modifications
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* path offset

    * a  robot motion can be superimposed with a Cartesian offset
    * Cartesian path offset is specified by the script function `path_offset_set(offset, type)`
    
        * offset: Pose specifying the translational and rotational offset
        * type: Specifies which coordinates to apply (`BASE`,`TCP`, `MOTION`, `BASE_INVERTED`)
    * possible applications:
    
        * imposing a weaving motion onto a welding task
        * compensating for moving the robot base while following a trajectory
            
* dynamic force control (see `dynamic_force_control`_)

    * provides control of the force parameters dynamically at runtime 
    * function to set robot to force mode: `force_mode(task_frame, selection_vector, wrench, type, limits)`
        
* conveyor tracking (see `conveyor_tracking`_)

    * adjusts a robot's trajectory to a moving conveyor
    * available for linear and circular conveyors
    * CB3 and e-Series controller can decode signals at up to 40kHz
