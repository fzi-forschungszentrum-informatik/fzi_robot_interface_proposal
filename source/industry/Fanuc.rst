Fanuc
=====

.. _manual_collection: http://cncmanual.com/fanuc/
.. _manual_slides: http://www.lakos.fs.uni-lj.si/wp-content/uploads/2017/12/Fanuc-robot.pdf
.. _Karel: https://www.tristarcnc.com/News/KarelProgrammingLanguage


* Textual description
* Vendor specifics 
   * Teach pendant: 				FANUC iPendant touch
   * Programming / simulation software: 	ROBOGUIDE 
   * User interface: 				Fanuc iHMI (Intelligent Human Machine Interface)
   * Programming language: 	
      * FANUC `Karel`_ (derived from Pascal)
      * FANUC’s TP
   * unsorted
      * FANUC Dynamic Path Modifier (DPM) 
      * FOCAS   
      * FANUC LADDER-III 


* Version of the user manual
* Source (link)
* etc ...

Trajectory composition
----------------------
Programming is done with move instructions (robot movement types).  (see `manual_slides`_ p. 15-16):
* **J-Joint motion**: basic robot motion with nonlinear toolpath. Tool speed is determined with % of the maximum speed. 
* **L-Linear motion**: controlled movement of the TCP in a straight line from position A to B.




Waypoint representation
-----------------------
How are Cartesian points specified? (e.g. x,y,z, roll pitch, yaw vs quaternions)


Trajectory parameterization and execution
-----------------------------------------
Describe if and how the following aspects are handled:

* Specification of velocity can be done via the following variables:
   * **$SCR.$RUNOVLIM**: controls speed globally in any program
   * **$SCR.$JOGLIM**: max speed for jogging (in % form 250mm/sec)
   * **$SCR.$JOGOVLIM**: 
   * **$SCR.$SFJOGOVLIM**: safety jog speed limit
   * **$SCR.$SFRUNOVLIM**: program run override limit
   * **$SCR.$COLDOVRD**: speed is downscaled when robot is rebooted (default 10% speed)
   * **$MCR.$GENOVERRIDE**: speed controlled by  +% and -% buttons
   * **$MCR_GRP.$PROGOVERRIDE**: program-settable override (default to 100) affecting program motion (multiplied with $MCR.$GENOVERRIDE)


* Specification of acceleration can be done via the following variables:

   * **$USEMAXACCEL**: enables 'fast acceleration' feature

   
* Blending

   *  Taught positions can either be fly-by points, or stop points
      * **FINE**: robot reaches the point exactly, stops briefly and moves to the next point.
      * **CNT**: robot approaches to the point with a distance specified by the CNT value without ever actually reaching the point. 

* Parallel IO operations:
   * bb

* Online (real-time) trajectory modifications:
   * bb



Features required from hardware
-------------------------------
* Applicable to which robots of the vendor?
* Are there requirements that other vendors' robots might not meet?


