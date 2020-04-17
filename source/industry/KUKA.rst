KUKA
====

.. _manual_collection: http://cncmanual.com/kuka-robotics/
.. _manual_slides: http://media.ee.ntu.edu.tw/personal/pcwu/tutorials/kuka_user_manual.pdf

* Textual description
* Name of vendor's programming environment:
   * KUKA smartHMI (smart Human-Machine Interface)
* Vendor specific programming language:
   * KRL (KUKA Robot Language)
* Version of the user manual:
* Link to manual:
   * `manual_collection`_
   * `manual_slides`_



Trajectory composition
----------------------
List of different motion primitives that users can use to compose/program
Cartesian trajectories (see `manual_slides`_ p. 23-32).
Explain parameters if necessary.

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
How are Cartesian points specified? (e.g. x,y,z, roll pitch, yaw vs quaternions)

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
* Applicable to which robots of the vendor?
* Are there requirements that other vendors' robots might not meet?


