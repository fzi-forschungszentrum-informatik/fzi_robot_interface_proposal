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

As motions are initiated and controlled in TP the user can only adapt the robot's motion speed with TP. System configurations and overrides influence the velocity additionally.


specification of acceleration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Specification of acceleration can be done via the following variables:

* acceleration time is fixe and proportional to the programmed speed.
* **$USEMAXACCEL**: enables 'fast acceleration' feature


Blending
~~~~~~~~

Taught positions can either be fly-by points, or stop points:

* **FINE**: motion stops robot arm briefly at each way point
* **CNT** (continuous): robot approaches to the point with a distance specified by the CNT value without ever actually reaching the point, so the robots arm moves in a continuous trajectory
* **CR** (corner radius): like CNT, but specifying a radius for corner rounding allows to precisely define the shape of the blended motion


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
    
J519  (Stream Motion)     
    * external protocol for:
        * path trajectory planning
        * near-real time streaming of the path trajectory to the robot 
        * enabling highly flexible and dynamic applications
    
R912 (Remote Motion Interface)       
    * drip-feed for TP programs
    
