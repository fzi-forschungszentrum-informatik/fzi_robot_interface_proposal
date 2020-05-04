Fanuc
=====

.. _manual_collection: http://cncmanual.com/fanuc-robotics/
.. _roboguide_help: http://cncmanual.com/download/4472/
.. _reference_manual: http://cncmanual.com/fanuc-robotics-r-30ia-controller-karel-reference-manual/
.. _manual_slides: http://www.lakos.fs.uni-lj.si/wp-content/uploads/2017/12/Fanuc-robot.pdf
.. _Karel: https://www.tristarcnc.com/News/KarelProgrammingLanguage

Cartesian trajectories for Fanuc robots

  =================================   =======================================
  Teach pendant                       FANUC iPendant touch
  Programming / simulation software   ROBOGUIDE
  Software                            
  User interface                      Fanuc iHMI (Intelligent Human Machine Interface)
  Programming language                FANUC `Karel`_ (derived from Pascal)
  Relevant hardware                   R-30iA or R-J3iC (controller)
  =================================   =======================================
  
  

**Further reading**

   * `manual_collection`_
   * `manual_slides`_
   * `roboguide_help`_
   * `reference_manual`_
   * `Karel`_

   

Trajectory composition
----------------------
Programming is done with move instructions (robot movement types).  (see `manual_slides`_ p. 15-16):

* Linear Cartesian motions
   **LINEAR**: controlled movement of the TCP in a straight line from position A to B

* Circular motions
   **CIRCULAR**: The TCP follows a circular arc from the initial position to the destination
   
* Joint space interpolation
   **JOINT**: basic robot motion with nonlinear toolpath. Tool speed is determined with % of the maximum speed.

   

Waypoint representation
-----------------------
Points are described with position coordinates x,y, z and rotations w, p, r.

.. code-block:: yaml

  x
  y
  z
  w (x-axis rotation)
  p (y-axis rotation)
  r (z-axis rotation)


Trajectory parameterization and execution
-----------------------------------------

(see `reference_manual`_)

Specification of velocity
~~~~~~~~~~~~~~~~~~~~~~~~~

System variables determine the speed of robot motion 

* Speed overrides:

    * `$MCR.$GENOVERRIDE`: general override
    * `$MCR_GRP.$PROGOVERRIDE`: program override

* Manual Motion Speed

    * Joint Speed (in joint units) equals: `$PARAM_GROUP[i].$SPEEDLIMJNT` * `$MCR[].$GENOVERRIDE` / 100  * `$SCR_GRP[i].$JOGLIM_JNT` / 100
    * Cartesian Translational Speed (in mm/sec) equals: $PARAM_GROUP[i].SPEEDLIM * $MCR[].$GENOVERRIDE / 100 * $SCR_GRP[i].$JOGLIM / 100
    * Cartesian Rotational Speed (in mm/sec) equals: ($PARAM_GROUP[i].ROTSPEEDLIM * $MCR[].$GENOVERRIDE / 100 * $SCR_GRP[i].$JOGLIMROT / 100

* Programmed Motion Speed


specification of acceleration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Specification of acceleration can be done via the following variables:

* acceleration time is fixe and proportional to the programmed speed.
* **$USEMAXACCEL**: enables 'fast acceleration' feature


Blending
~~~~~~~~

Taught positions can either be fly-by points, or stop points:

* **FINE**: motion stops robot arm briefly at each way point
* **CNT**: robot approaches to the point with a distance specified by the CNT value without ever actually reaching the point.


Parallel IO operations
~~~~~~~~~~~~~~~~~~~~~~

No information found so far

Online (real-time) trajectory modifications
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Dynamic Path Modifier (DPM)

* dynamic path modification using sensor data, so robot's path can be adapted in real-time
* an external sensor provides postion and orientation offset for the next destination
* applicable to multiple groups
* possible applications:

    * Weave operations
    * Stationary tracking
    * Orientation control
