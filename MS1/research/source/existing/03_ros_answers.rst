ROS-Answers question by arennuit
================================

https://answers.ros.org/question/196954/cartesian-trajectory-description-which-message-type

Feature list
------------
 * Contains accelerations

Features required from hardware / driver
----------------------------------------
 * Control interface

Message definition
------------------
Note: The source itself does not contain a message definition, so this is an interpretation of the
plain text.

.. code-block:: yaml

  [CartesianTrajectoryGoal]
  string reference_frame # could also come through header
    CartesianTrajectoryPoint[] points
      duration time_from_start
      Pose pose
      Twist twist
      Twist acceleration
