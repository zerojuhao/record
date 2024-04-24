# Modification of force on the drone

**In these tests, the drone needs to fly from (0,0,1) to (2,0,1) on x-axis**

## test 1
initial version

```
self.friction[:, 0, :] = -0.02*torch.sign(self.controller.body_drone_linvels)*self.controller. body_drone_linvels**2
self.forces[:,0,2] = common_thrust
self.forces[:,0,:] += self.friction[:,0,:]
```

<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/drone_linvel_1.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/linvel_1.png" style="width: 400px; height: auto;">
</div>

<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/friction_1.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/force_1.png" style="width: 400px; height: auto;">
</div>


## test 2
remove the minus sign in self.friction

```
self.friction[:, 0, :] = 0.02*torch.sign(self.controller.body_drone_linvels)*self.controller. body_drone_linvels**2
self.forces[:,0,2] = common_thrust
self.forces[:,0,:] += self.friction[:,0,:]
```

<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/drone_linvel_2.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/linvel_2.png" style="width: 400px; height: auto;">
</div>

<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/friction_2.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/force_2.png" style="width: 400px; height: auto;">
</div>

## test 3
change 0.02 to 0.001

```
self.friction[:, 0, :] = 0.001*torch.sign(self.controller.body_drone_linvels)*self.controller. body_drone_linvels**2
self.forces[:,0,2] = common_thrust
self.forces[:,0,:] += self.friction[:,0,:]
```
<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/drone_linvel_3.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/linvel_3.png" style="width: 400px; height: auto;">
</div>

<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/friction_3.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/force_3.png" style="width: 400px; height: auto;">
</div>

## test 4
For the drone, we only have 2 setting from CTBR and NN:
1. the total_torque on x,y,z
2. the common_thrust on z

Due to the presence of air, the friction on x,y,z is considered.The friction has the opposite direction to the thrust and it also changes with time.

When setting the force on the drone, the force on x,y should be the friction and the force on z should be the common_thrust.

**So I guess the force on x,y should not be able to accumulate, because it is friction which should not be accumulated.**

In previouse calculation, self.forces is initialized only once at the beginning of the program. If we use this method to calculate the force.

```
self.forces[:,0,:] += self.friction[:,0,:]
```

In fact, this will cause the force on x,y to accumulate in subsequent calculation and finally become pretty large to make the drone out of control. Thus, I revise the calculation of self.forces.

```
self.friction[:, 0, :] = 0.02*torch.sign(self.controller.body_drone_linvels)*self.controller. body_drone_linvels**2
self.forces = self.friction.clone()
self.forces[:,0,2] = common_thrust
```

Now, the force on x,y is friction, and on z common_thrust.

<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/drone_linvel_4.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/linvel_4.png" style="width: 400px; height: auto;">
</div>

<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/friction_4.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/force_4.png" style="width: 400px; height: auto;">
</div>

## test 5
The force on x,y is friction, and on z common_thrust + friction
```
self.friction[:, 0, :] = 0.02*torch.sign(self.controller.body_drone_linvels)*self.controller. body_drone_linvels**2
self.forces = self.friction.clone()
self.forces[:,0,2] += common_thrust
```
<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/drone_linvel_5.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/linvel_5.png" style="width: 400px; height: auto;">
</div>

<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/friction_5.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/force_5.png" style="width: 400px; height: auto;">
</div>

## test 6
use self.root_linvels not self.controller.body_drone_linvels
```
self.friction[:, 0, :] = -0.02 * torch.sign(self.root_linvels) * self.root_linvels ** 2
self.forces = self.friction.clone()
self.forces[:,0,2] += common_thrust
```
<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/drone_linvel_6.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/linvel_6.png" style="width: 400px; height: auto;">
</div>

<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/friction_6.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/force_6.png" style="width: 400px; height: auto;">
</div>

## test 7
I guess the friction should be limited to a reasonable range.
```
self.friction = torch.clamp(self.friction, -0.02, 0.02)
self.forces = self.friction.clone()
self.forces[:,0,2] += common_thrust
```
<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/drone_linvel_7.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/linvel_7.png" style="width: 400px; height: auto;">
</div>

<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/friction_7.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/force_7.png" style="width: 400px; height: auto;">
</div>