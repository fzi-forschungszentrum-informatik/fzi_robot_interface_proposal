KUKA
====

.. _manual_collection: http://cncmanual.com/kuka-robotics/
.. _manual_slides: http://media.ee.ntu.edu.tw/personal/pcwu/tutorials/kuka_user_manual.pdf
.. _KRL_guide: http://robot.zaab.org/wp-content/uploads/2014/04/KRL-Reference-Guide-v4_1.pdf
.. _Angerer: https://opus.bibliothek.uni-augsburg.de/opus4/frontdoor/deliver/index/docId/3064/file/Dissertation_Angerer.pdf
.. _Vistein: https://opus.bibliothek.uni-augsburg.de/opus4/frontdoor/deliver/index/docId/3271/file/Vistein_Dissertation.pdf
.. _RobotSensorInterface: http://supportwop.com/IntegrationRobot/content/6-Syst%C3%A8mes_int%C3%A9grations/RobotSensorInterface/KST_RSI_31_en.pdf
.. _manual_advanced: http://www.wtech.com.tw/public/download/manual/kuka/krc2ed05/Operating%20and%20Programming.pdf
.. _kuka_system_software: http://www.wtech.com.tw/public/download/manual/kuka/krc4/KUKA%20KSS-8.3-Programming-Manual-for-SI.pdf
.. _Cooper: https://www.aimspress.com/fileOther/PDF/ElectrEng/ElectronEng-03-03-274.pdf

Cartesian trajectories for the KUKA robots (KRC/KRL).

.. table:: Vendor specifics

  =================================   =======================================
  Teach pendant                       “KCP” (KUKA Control Panel) or smartPAD
  Programming / simulation software   OrangeEdit editor / KUKA simulator Sim Pro
  Software                            KUKA System Software (KSS)
  User interface                      KUKA smartHMI (smart Human-Machine Interface)
  Programming language                KRL (KUKA Robot Language)
  Relevant hardware                   KR C2 / KR C3 / KR C4 and probably others
  =================================   =======================================

**Further reading**
   * `manual_collection`_
   * `manual_slides`_
   * `manual_advanced`_



Trajectory composition
----------------------
Cartesian trajectories can be composed in three ways (see `manual_slides`_ p. 23-32).:

* Linear Cartesian motions
   **LIN**

	.. code-block:: yaml

	  [LIN]
	  $VEL.CP = 0.5
	  PTP Start point
	  LIN End point

* Circular motions
   **CIRC**

	.. code-block:: yaml

	  [CIRC]
	  $VEL.CP = 0.5
	  PTP Start point
	  CIRC Auxiliary point , Endpoint, CA Angle

* Joint space interpolation
   **PTP**

   * joint space movement to a given goal, which can be specified in joint space or in Cartesian space.
   * controller calculates the necessary angle differences for each axis
   * Preferred motion if a high TCP speed is wanted and the interpolation between both waypoints doesn't have to follow a predefined path.

	.. code-block:: yaml

	  [PTP]
	  $VEL.CP = 0.5
	  PTP Start point
	  PTP Auxiliary point C_PTP
	  PTP End point

* Additional
   **SLIN**

   * The *Spline Linear* motion uses splines between linear motions

   **SCIRC**

   * The *Spline Circular* motion uses splines between circular motions

   **SPTP**

   * The *Spline Point to Point* motion is similar to PTP but it allows continuous spline motions.
	



Waypoint representation
-----------------------

(see `kuka_system_software`_ and `manual_slides`_)

   * Linear

	.. code-block:: yaml

	  [LIN]
	  X 1000.00
	  Y 0.00
	  Z 1000.00
	  A 90.00
	  B 0.00
	  C 90.00

   * Circular

	.. code-block:: yaml

	  [CIRC]
	  P1[] 
	    X 1000.00
	    Y 1.00
	    Z 1000.00
	    A 90.00
	    B 0.00
	    C 90.00
	  P2[] 
	    X 1000.00
	    Y -1.00
	    Z 1000.00
	    A 90.00
	    B 0.00
	    C 90.00
	  CA 180
	    

   * Point 2 Point

	.. code-block:: yaml

	  [PTP]
	  POS [] 
	    X 1000.00
	    Y 0.00
	    Z 1000.00
	    A 90.00
	    B 0.00
	    C 90.00
	    S 6
	    T 50
    
	  
	.. code-block:: yaml

	  [PTP]
	  AXIS [] 
	    A1 0
	    A2 -90
	    A3 90
	    A4 90
	    A5 0
	    A6 -180

   * Spline

	.. code-block:: yaml

	  [SPLINE]
	  SPL 
	    X 102
	    Y 1
	  SPL 
	    X 104
	    Y 0
	  SPL 
	    X 204
	    Y 0

* Angles of rotation of the robot coordinate systems
* S and T specify a robot's position unambiguously if more than one axis position is possible for
  the same point in space (because of kinematic singularities). This is often written in integer
  form, thus the values above.

  * **S (status):** 3-bit binary value describing the robot's configuration with predefined criteria

  * **T (turn):** direction of a turn.
    6-bit binary value, containing flip bits for each axis (0 when axis >= 0 deg, 1 when axis <  0
    deg)

=====  =============
Angle  rotation axis 
=====  =============
A  	   Z  
B	   Y  
C	   X      
=====  =============


Trajectory parameterization and execution 
-----------------------------------------

(see `manual_advanced`_)

Specification of velocity
~~~~~~~~~~~~~~~~~~~~~~~~~

    * Speed of TCP can be set within a move instructions in % by the 'vel' argument.
    * For Continuous path motions ([LIN], [CIRC]) the velocity is constant from start to end.
    * Realtive Joint Velocity can be set by: *setJointVelocityRel(0.3)*
    * KUKA operation mode influence velocity
      
      ====   =======================   ==============
      Mode   description               velocity
      ====   =======================   ==============
      T1     Manual Reduced Velocity   max of 250mm/s
      T2     Manual High Velocity      as programmed 
      AUT    Automatic                 as programmed 
      EXT    Automatic external        as programmed 
      CPR    Safe Operation            max of 250mm/s 
      ====   =======================   ==============

specification of acceleration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Relative Joint Acceleration can be set by: *setJointAccelerationRel(0.5)*

Blending
~~~~~~~~

(source `Angerer`_ and `Vistein`)

    * Blending is enabled by the *advance run mechanism* enabling planning the next motion while executing a motion.
    * To activate blending a motion needs to be marked as blendable by adding a keyword to the motion instruction. `C PTP`for PTP motions and `C_DIS`, `C_VEL` or `C_ORI` for motions in operation space.
    * Blending between all motion types is supported. It is even possible to blend a PTP (joint space) into a LIN (Cartesian space) and vice versa.
    * Blending can be done by defining a blend radius 

        * as a relative value:  *IMotion.setBlendingRel(0.2)*
        * in millimeters:        *IMotion.setBlendingCart(20)*

Parallel IO operations
~~~~~~~~~~~~~~~~~~~~~~

No information found so far

Online (real-time) trajectory modifications
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Robot Sensor Interface (RSI)  (see `RobotSensorInterface`_)

  * supported since KRC-4 controller
  * influence the position of the robot by external sensors.
  * robot position can be influenced by external sensors through overlaying a programmed motion with external control, like position correction from a sensor-based system
  * default 4 ms cycle time for accepting set point, hence external controller requires hard real-time
  * usually correction data is provided in relative values and applied directly to the running program. However, as absolute values are possible, the robot can be controlled externally while a KRL  program only providing a fixed start position runs in the background.
  * communication between KUKA and external controller via UDP/IP on a dedicated network segment
  * *RSI context* is a library with RSI objects for configuration of the signal flow
  * *RSI monitor* offers online a visualization of the RSI signals.

