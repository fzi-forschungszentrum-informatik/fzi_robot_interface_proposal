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

Contents of this document
-------------------------

This document will start with a summary of existing suggestions for Cartesian interfaces known to
us. A conclusion chapter will form a proposed interface taking those interfaces into account.

Additional to that we will have a look at native robot interfaces to get an overview how industrial
vendors interface their robots.

Contribution
------------

We would like to generate an interface suitable for
as many people, as possible. Therefore, any input is welcome. This project is hosted at `FZI's
github orgnanization <https://github.com/fzi-forschungszentrum-informatik/fzi_robot_interface_proposal>`_


Please get in touch with us and enter the discussion. Either open a new issue if you want to commit
on something or even write a Pull Request with a suggestion.

Requirements
------------

The following requirements are defined for the developed interface

Similar to control_msgs/FollowJointTrajectory.action
  The interface should be similar in use to the standardized joint trajectory interface. Therefore,
  not only a trajectory representation shall be developed, but also an action interface around it.

Include posture definitions
  When defining Cartesian poses there might be ambiguities in joint space for that pose. There
  should be a methodology included that helps resolving these ambiguities.

Composable structure
  The action should be composed of multiple message types. This way, it could be easily extended
  such as adding IO commands to the trajectory. Example:

  .. code-block:: yaml

    #Trajectory
    TrajectoryPoint[] points
    IOCommand[] io_commands

Transparent error codes
  The action result should cover additional cases relevant only for Cartesian executions such as an
  IK solver not finding a solution. This has to be further investigated.

