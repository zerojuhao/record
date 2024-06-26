# Weekly Summary 2024-5-7
We tried more tests. We found: 

In "CTBRcontroller.py"-"update", we use the quaternion to change the coordinate system to the drone itself to calculate torque and collective thrust. And finally, we use matrix inv_A to change the coordinate system to Isaac Gym to set force and torque on the rigid body.

If the coordinate system is Isaac Gym space, when the drone is tilted, the collective thrust should be decomposed into three forces in the x-y-z direction, not only 1 force in z direction. But in the current code, we only have an upward thrust when we set "apply_rigid_body_force_tensors".

## test 1
```
total_torque, common_thrust = self.controller.update(actions, 
                                                        self.root_quats, 
                                                        self.root_linvels, 
                                                        self.root_angvels)
self.friction[:, 0, :] = -0.005*torch.sign(self.root_linvels)*self.root_linvels**2
self.friction = torch.clamp(self.friction, -0.005, 0.005)
self.forces = self.friction.clone()
self.forces[:,0,2] += common_thrust
# force on x: friction
# force on y: friction
# force on z: friction + collective_thrust
```

<img src="https://github.com/zerojuhao/record/blob/main/image/24-5-7-1.gif" style="width: 600px; height: auto;">
In the image above, the drone's task is to fly from (0,0,1) to (1,0,1)

<img src="https://github.com/zerojuhao/record/blob/main/image/24-5-7-2.gif" style="width: 600px; height: auto;">
In the image above, the drone's task is to fly from (0,0,1) to (0,0,1.5)

Obviously, neither task was well done. We're guessing this is because the drone lacks the component force from the collective thrust in the x,y direction, so the drone had to fly using unregulated friction.
