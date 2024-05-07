# Weekly Summary 2024-5-7
We tried more tests. We found: 

In "CTBRcontroller.py"-"update", we use the quaternion to change the coordinate system to the drone itself to calculate torque and collective thrust. And finally, we use matrix inv_A to change the coordinate system to Isaac Gym to set force and torque on the rigid body.

There is no issue with this code when the drone is in a horizontal position. But when the drone is tilted, the collective thrust should be decomposed into three forces in the x-y-z direction, not only 1 force in z direction. In the current code, we only have an upward thrust when we set "apply_rigid_body_force_tensors", so we assume it is difficult for the drone to handle horizontal tasks such as flying forward and right.

To test our hypotheses:

In the image below, the drone's task is to fly from (0,0,1) to (1,0,1)
<img src="https://github.com/zerojuhao/record/blob/main/image/24-5-7-1.gif" style="width: 600px; height: auto;">

In the image below, the drone's task is to fly from (0,0,1) to (0,0,1.5)
<img src="https://github.com/zerojuhao/record/blob/main/image/24-5-7-2.gif" style="width: 600px; height: auto;">
