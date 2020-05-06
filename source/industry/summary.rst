Summary of vendor interfaces
============================

This section will summarize the different vendor interfaces to explicitly show common patterns that
we could build an interface upon.

Segment interpolation methods
-----------------------------


+--------------+------------+--------------+------------+-----------+
|              | **linear** | **circular** | **spline** | **joint** |
|              | Cartesian  | Cartesian    | Cartesian  | joint     |
+==============+============+==============+============+===========+
| KUKA         | yes        | yes          | yes        | yes       |
+--------------+------------+--------------+------------+-----------+
| UR           | yes        | yes          | yes        | yes       |
+--------------+------------+--------------+------------+-----------+
| Fanuc        | yes        | yes          |            | yes       |
+--------------+------------+--------------+------------+-----------+
| Doosan       | yes        | yes          | yes        | yes       |
+--------------+------------+--------------+------------+-----------+
| Franka Emika | yes        |              |            |           |
+--------------+------------+--------------+------------+-----------+
| ABB          | yes        | yes          |            | yes       |
+--------------+------------+--------------+------------+-----------+
| Yasakawa     | yes        | yes          | yes        | yes       |
+--------------+------------+--------------+------------+-----------+

