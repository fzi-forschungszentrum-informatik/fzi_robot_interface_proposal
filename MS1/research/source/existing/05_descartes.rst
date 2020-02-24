Descartes Trajectory
====================

http://wiki.ros.org/descartes_trajectory
https://github.com/ros-industrial-consortium/descartes/tree/melodic-devel/descartes_trajectory/include/descartes_trajectory


Feature list
------------
 * Contains reference implementations for path sepcification and interface for more customized methods
 *

Features required from hardware / driver
----------------------------------------
 
 

Message definition
------------------

.. code-block:: yaml

  [JointTrajectoryPt]
  joint position
  TolerancedJointValue
  TimingConstraint


  [CartTrajectoryPt]
  TolerancedJointValue
  step size


  [AxialSymmetricPt]