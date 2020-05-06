Summary of vendor interfaces
============================

This section will summarize the different vendor interfaces to explicitly show common patterns that
we could build an interface upon.

Segment interpolation methods
-----------------------------


+--------------+------------+--------------+------------+-----------+
|              | **linear** | **circular** | **spline** | **joint** |
|              | Cartesian  | Cartesian    | Cartesian  |           |
+==============+============+==============+============+===========+
| KUKA         | yes        | yes          | yes        | yes       |
+--------------+------------+--------------+------------+-----------+
| UR           | yes        | yes          | yes        | yes       |
+--------------+------------+--------------+------------+-----------+
| Fanuc        | yes        | yes          | no         | yes       |
+--------------+------------+--------------+------------+-----------+
| Doosan       | yes        | yes          | yes        | yes       |
+--------------+------------+--------------+------------+-----------+
| Franka Emika | yes        | (yes)        | (yes)      | (yes)     |
+--------------+------------+--------------+------------+-----------+
| ABB          | yes        | yes          | no         | yes       |
+--------------+------------+--------------+------------+-----------+
| Yaskawa      | yes        | yes          | yes        | yes       |
+--------------+------------+--------------+------------+-----------+

The investigated interfaces differ in the amount and richness of provided motion types,
such that the table above summarizes the best supported subset.

Apart from Franka Emika, all investigated robot interfaces could natively
execute trajectories composed of `linear`, `circular` and `joint` interpolated motion segments.
A possible Cartesian trajectory definition could therefore build upon these three types.

Franka Emika is a special case here, as they do not offer all functionality in an open programming
language, but they have pre-built solution modules that help users getting their tasks done.
Therefore, such interfaces do exist, but in a different manner than the other brands and should not veto the trajectory definition.


Parameterization of trajectory executions
-----------------------------------------

For each vendor interface, We looked at four parameter categories that will be important for trajectory definitions:
**Velocity**, **Acceleration**, **Blending**, and **Parallel IO** commands.

The table below summarizes the findings to highlight commonly supported configuration by each vendor.
We use our own abbreviations to keep the table clear.

For velocity / accelerations, users can set:

.. code-block:: yaml

   (a) Global target parameter for whole trajectory
   (b) Local target parameter for motion or waypoint
   (c) Global limit for whole trajectory
   (d) Local limit for motion or waypoint
   (e) Percentage of globally defined parameter

For blending, users can set:

.. code-block:: yaml

   (1) Radius per waypoint
   (2) Radius per motion
   (3) Global radius for all motions or waypoints
   (4) Advanced radii definitions



+--------------+-----------------+-----------------+-----------------------+-------------------------------------------------------------------------------+
|              | Velocity        | Acceleration    | Blending              | Parallel IO                                                                   |
+==============+=================+=================+=======================+===============================================================================+
| KUKA         |      \(a) \(e)  |      \(e)       |      \(1) \(2) \(4)   | ??                                                                            |
+--------------+-----------------+-----------------+-----------------------+-------------------------------------------------------------------------------+
| UR           | \(b) \(c) \(d)  |      \(b)       |         \(1) \(3)     | Synchronized with waypoints                                                   |
+--------------+-----------------+-----------------+-----------------------+-------------------------------------------------------------------------------+
| Fanuc        |      \(a) \(e)  |      \(c)       |         \(1)          | ??                                                                            |
+--------------+-----------------+-----------------+-----------------------+-------------------------------------------------------------------------------+
| Doosan       | \(a) \(b) \(e)  |      \(c) \(d)  |         \(1)          | In parallel with asynchronous motions                                         |
+--------------+-----------------+-----------------+-----------------------+-------------------------------------------------------------------------------+
| Franka Emika |      \(e)       |      \(e)       |   \(2)  \(4)          | User-custom with apps                                                         |
+--------------+-----------------+-----------------+-----------------------+-------------------------------------------------------------------------------+
| ABB          |      \(a) \(b)  |  \(a) \(c) \(e) |  \(1)   \(4)          | Separate motion commands with IOs triggered in the middle                     |
+--------------+-----------------+-----------------+-----------------------+-------------------------------------------------------------------------------+
| Yaskawa      |    \(a) \(b)    |      \(e)       |     \(1)              | Directly before or after motion commands                                      |
+--------------+-----------------+-----------------+-----------------------+-------------------------------------------------------------------------------+

Most interfaces provide similar parameterization of velocity and accelerations.
However, blending is differently implemented by the vendors. The (4) = *Advanced radii definition* mentioned in the column *Blending* refers to blending for Cartesian motions for Franka Emika, blending through detailed corner paths for ABB and blending between joint and Cartesian motion for KUKA.

The double ?? indicate that we didn't find sufficient information on this subject.


.. Write this conclusion once we know how that would work in our trajectory definition

Specification of waypoints
--------------------------

+--------------+----------------+-----------------------+
|              | Waypoint       | Posture               |
|              | Representation | definition            |
+==============+================+=======================+
| KUKA         | xyz-rpy        | 3 bit configuration,  |
|              |                | 6 bit turn directions |
+--------------+----------------+-----------------------+
| UR           | xyz-rpy        | qnear                 |
+--------------+----------------+-----------------------+
| Fanuc        | xyz-rpy        | 3 bit configuration   |
+--------------+----------------+-----------------------+
| Doosan       | xyz-rpy (zyz)  | 3 bit configuration   |
+--------------+----------------+-----------------------+
| Franka Emika | user's choice  | user's choice         |
+--------------+----------------+-----------------------+
| ABB          | xyz-quat       | quadrants for axes    |
+--------------+----------------+-----------------------+
| Yaskawa      | xyz-rpy        | 3 bit configuration   |
+--------------+----------------+-----------------------+

Franka's waypoint representation for the app-based approach was not obvious during this review.
However, their fully exposed interface with low-level access to joint drivers for the FCI-based approach
offers users to implement specific representations themselves.
We used `user's choice` to indicate this.

Both KUKA and ABB offer more detailed posture definitions with respect to the other vendor interfaces.
A least denominator of 3 bit posture configurations is therefore meaningful for a common interface
with UR being an exception, as they use a notation of a nearby joint configuration.

Posture definition is discussed in section :ref:`Conclusion and Proposed Interface`. We suggest to follow the
definition there, as it should be possible to map them to the vendors' representations.

ABB stands out with their quaternion representation for orientation, avoiding potential gimbal locks.
Due to the unique mapping from angle-sequence representations to quaternions,
however, all observed vendor interfaces will work nicely with a roll-pitch-yaw
notation.

Proposed interface
~~~~~~~~~~~~~~~~~~

To be determined....

- Integration into / combination with REP-I0003?

  - Open questions?

    - How to integrate into ROS control?

