KUKA
====

.. _manual_collection: http://cncmanual.com/kuka-robotics/
.. _manual_slides: http://media.ee.ntu.edu.tw/personal/pcwu/tutorials/kuka_user_manual.pdf
.. _manual_advanced: http://www.wtech.com.tw/public/download/manual/kuka/krc2ed05/Operating%20and%20Programming.pdf

* Description: Cartesian trajectories for the Yaskawa Motoman robots
* Vendor specifics 
   * Teach pendant: “KCP” (KUKA Control Panel) or smartPAD
   * Software: KUKA System Software (KSS)
   * Programming environment / user interface: KUKA smartHMI (smart Human-Machine Interface)
   * Programming language: KRL (KUKA Robot Language)
* Version of the user manual:
* Link to manuals:
   * `manual_collection`_
   * `manual_slides`_
   * `manual_advanced`_



Trajectory composition
----------------------
Cartesian trajectories can be composed in three ways (see `manual_slides`_ p. 23-32).:

* Linear
   * straight line from the current position to target position
   * Message defintion:

	.. code-block:: yaml

	  [LIN]
	  $VEL.CP = 0.5
	  PTP Start point
	  LIN End point

* Circular
   * To define a circle or arc in space unambiguously
   * Message defintion:

	.. code-block:: yaml

	  [CIRC]
	  $VEL.CP = 0.5
	  PTP Start point
	  CIRC Auxiliary point , Endpoint, CA Angle

* Point 2 Point
   * controller calculates the necessary angle differences for each axis
   * to increase velocity, points for which exact positioning is not necessary can be approximated
   * Message defintion:

	.. code-block:: yaml

	  [PTP]
	  $VEL.CP = 0.5
	  PTP Start point
	  PTP Auxiliary point C_PTP
	  PTP End point

* Halt

Waypoint representation
-----------------------
* Motion Types
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

* Angles of rotation of the robot coordinate systems

=====  =============
Angle  rotation axis 
=====  =============
A  	Z  
B	Y  
C	X      
=====  =============

Trajectory parameterization and execution
-----------------------------------------
Describe if and how the following aspects are handled:

* Specification of velocity
* specification of acceleration
* Blending
* Parallel IO operations
* Online (real-time) trajectory modifications

Features required from hardware
-------------------------------
* Applicable to KR C2 / KR C3
* Are there requirements that other vendors' robots might not meet?


