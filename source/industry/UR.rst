Universal Robots (UR)
=====================
* Textual description
* Vendor specifics 
   * Teach pendant: 
   * Programming / simulation software:
   * User interface: 			PolyScope
   * Programming language: 		UR Script language (based on Python)

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
How are Cartesian points specified? (e.g. x,y,z, roll pitch, yaw vs quaternions)

Trajectory parameterization and execution
-----------------------------------------
Describe if and how the following aspects are handled:

* Specification of velocity

   * The robot's speed is defined in form of an argument by the move command.
   
      * For *MoveJ* in :math:`deg/s`:  **maximum joint speed**
      * For *MoveL* in :math:`mm/s`:  **desired tool speed**
      
   * Global specification of velocity are done via two safety-related functions:
      
     ===========  ===========================     ===========
     Function     Description                     Limit
     ===========  ===========================     ===========
     Joint speed  Max. angular joint speed        30 ◦/s
     TCP speed    Max. speed of the robot TCP     250 mm/s
     ===========  ===========================     ===========

   
* specification of acceleration

    * The acceleration of the robot's motions is defined in form of an argument by the move command. Depending on the chosen movement type either the joints' or TCP's acceleration is definable.

      * For *MoveJ* in :math:`deg/s^2`:  **joint acceleration**
      * For *MoveL* in :math:`mm/s^2`:  **tool acceleration**

      
* Blending
* Parallel IO operations
* Online (real-time) trajectory modifications

Features required from hardware
-------------------------------
* Applicable to which robots of the vendor?
* Are there requirements that other vendors' robots might not meet?




The Joint Limits subtab consists of joint speed and joint position limits. The
joint speed limits define the maximum angular velocity of individual joints and
serve to further limit the speed of the robot arm. The joint position limits define
the allowed position range of individual joints (in joint space). For further
details, see 15.10

Maximum Speed This option defines the maximum angular velocity for each
joint. This is done by tapping the corresponding text field and entering the new
value. The highest accepted value is listed in the column titled Maximum. None of
the values can be set below the tolerance value.

range for angular velocity: -363 - 363 im normal mode


