Universal Robots (UR)
=====================
* Description: Cartesian trajectories for Universal Robots (CB3 / e-Series)
* Vendor specifics 
   * Programming / simulation software:     UR+ / URcaps
   * User interface: 			            PolyScope
   * Programming language: 		            UR Script language (based on Python)

* Version of the user manual:
   * UR5e: 5.0.2
   * UR10e: 5.0.2
   * UR5: 3.4.5
   * UR10:  3.1.0
* Link to manual:
   * e-Series
      * https://s3-eu-west-1.amazonaws.com/ur-support-site/40974/UR5e_User_Manual_en_US.pdf
      * https://s3-eu-west-1.amazonaws.com/ur-support-site/41240/UR10e_User_Manual_en_US.pdf
   * CB3
      * https://courses.ideate.cmu.edu/60-428/s2018/wp-content/uploads/2017/12/UR5_User_Manual_en_US-3.4.5.pdf
      * https://automationdistribution.com/content/Universal-Robots-UR10-User-Manual.pdf


Trajectory composition
----------------------
Programming is done with move instructions (movement types) that move the robot to specified targets.

* **MoveJ**: tool moves in a curved path. Movements are calculated in the joint space. Each joint reaches location simultaneously. Preferred motion if a high TCP speed is desired. Possible arguments: maximum joint speed and joint acceleration.
* **MoveL**: tool moves in a straight line. To keep moving linearly between waypoints each joint performs a more complicated motion. Possible arguments: desired tool speed, tool acceleration, feature.
* **MoveP**: tool moves linearly with constant speed with circular blends. Command can be extendend by a Circle move consisting of two waypoints. Possible arguments: size of blend radius



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

* Specification of velocity

   * The robot's speed is defined in form of an argument by the move command.
   
      * For *MoveJ* in deg/s:  **maximum joint speed**
      * For *MoveL* in mm/s:  **desired tool speed**
      
   * Global specification of velocity are done via two safety-related functions:
      
     ===========  ===========================     ===========
     Function     Description                     Limit
     ===========  ===========================     ===========
     Joint speed  Max. angular joint speed        30 ◦/s
     TCP speed    Max. speed of the robot TCP     250 mm/s
     ===========  ===========================     ===========

   
* Specification of acceleration

    * The acceleration of the robot's motions is defined in form of an argument by the move command. Depending on the chosen movement type either the joints' or TCP's acceleration is definable.

      * For *MoveJ* in :math:`deg/s^2`:  **joint acceleration**
      * For *MoveL* in :math:`mm/s^2`:  **tool acceleration**

      
* Blending

    * Circular blending is part of **MoveP**. The blend radius' size is by default a shared value between all the waypoints. A smaller blend radius leads to sharper and a biger radius to smoother paths.
    * Belnding can also be done by defining a blend radius for way points. In this case the trajectory blends around the waypoint, allowing the robot arm not to stop at the point.


* Parallel IO operations
    * can be triggered at certain points in the robot’s path

 
* Online (real-time) trajectory modifications
    * realized by Real-Time Data Exchange (RTDE) interface

    
Features required from hardware
-------------------------------
* Applicable to robots 
    
    * CB3 (UR3, UR5, UR10)
    * e-Series (UR3e, UR5e, UR10e)
    
* Are there requirements that other vendors' robots might not meet?
    
    * Cartesian position and velocity control interfaces on the robots.
    * Built-in, tool-centric force/torque sensor (e-Series)




    
    
    


















