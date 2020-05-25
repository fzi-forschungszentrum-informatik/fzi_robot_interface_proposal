Introduction
============

This document serves as a design documentation to create a Cartesian trajectory definition for ROS.
As there have been many proposals in the past, but there is no standardized interface, this
document tries to incorporate as many suggestions as possible to derive a common definition useful
to as many people as possible.

Target of this project
----------------------
This is part of a larger project that tries to achieve Cartesian trajectory execution on Universal
Robots robotic arms. Expected outcome of this project is to provide a Cartesian equivalent of the
`control_msgs/FollowJointTrajectory.action
<http://docs.ros.org/api/control_msgs/html/action/FollowJointTrajectory.html>`_.

Current ROS-based approaches often use motion planing to interpolate between individual Cartesian poses
(e.g. `MoveIt!
<https://moveit.ros.org/>`_, `Descartes <http://wiki.ros.org/descartes>`_) or implement Cartesian pose tracking for dynamic targets
(e.g. `cartesian_controllers
<https://github.com/fzi-forschungszentrum-informatik/cartesian_controllers>`_).

While these approaches work fine for many applications, there are also use cases that need a Cartesian trajectory approach. Especially
in the industrial context, such as for welding or gluing applications, users classically define a tool path in
Cartesian space that should be followed precisely.

As most robot vendors offer programming interfaces to define robot motions in Cartesian space it
does make sense to also support these interfaces from ROS instead of always taking the detour
of joint-based control. Cartesian control on the other hand introduces additional aspects that need consideration, such as resolving ambiguities in
joint space that arise for obtaining identical Cartesian poses. A Cartesian trajectory interface needs to cover this as well.

Contents of this document
-------------------------

This document will start with a summary of existing suggestions for Cartesian interfaces known to
us. A conclusion chapter will form a proposed interface taking those interfaces into account.

Additional to that we will have a look at native robot interfaces to get an overview how industrial
vendors interface their robots.

Contribution
------------

We would like to generate an interface suitable for
as many people as possible. Therefore, any input is highly welcome! This project is hosted at `FZI's
github orgnanization <https://github.com/fzi-forschungszentrum-informatik/fzi_robot_interface_proposal>`_


Please get in touch with us and enter the discussion. Either open a new issue if you want to commit
on something or even write a Pull Request with a suggestion.

Requirements
------------

From the motivation above and the shown possible use cases the following requirements are defined
for the developed interface

* **Similar to control_msgs/FollowJointTrajectory.action**

  With this proposal we aim to offer Cartesian trajectory execution in terms of trajectories
  consisting of multiple waypoints, where motion between waypoints is interpolated in Cartesian
  space. This interface should be similar in use to the standardized joint trajectory interface.
  Therefore, not only a trajectory representation shall be developed, but also an action interface
  around it.

* **Include posture definitions**

  As mentioned above, when defining Cartesian poses there might be ambiguities in joint space for
  that pose. There should be a methodology included that helps resolving these ambiguities. This
  gives users control over repeatable robot motion.

* **Composable structure**

  Many use cases require tool activation / modification during trajectory execution, for example
  activating adhesive extrusion or a welding torch. With this in mind, the proposed trajectory
  interface should be extendible to introduce different aspects of trajectory execution
  such as adding IO commands to the trajectory. Example:

  .. code-block:: yaml

    #Trajectory
    TrajectoryPoint[] points
    IOCommand[] io_commands

* **Transparent error codes**

  When trajectory execution fails users should know the reason for that. With Cartesian motions
  additional error sources such as an IK solver not finding a solution are relevant and should
  therefore be included into the trajectory action definition. This has to be further investigated.


Project limitations
-------------------

While this document proposes an interface for executing Cartesian trajectories there are a couple of
aspects not being discussed inside this design document:

* **Trajectory-IO synchronization**

  While being mentioned earlier IO synchronization is not explicitly covered inside this document.
  As written, the interface should be designed in a way that it could easily be extended with such a
  feature, though.

* **Actual trajectory execution / interpolation**

  There are multiple steps involved between a Cartesian trajectory interface and actual motion
  execution. There are different strategies that can be implemented, where selection of such a
  strategy highly depends on the actual use case. This will not be part of this interface
  definition.

