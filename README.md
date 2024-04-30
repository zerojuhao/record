# Modification of force on the drone

**In these tests, the drone needs to fly from (0,0,1) to (2,0,1) on x-axis**

## test 1
initial version

```
self.friction[:, 0, :] = -0.02*torch.sign(self.controller.body_drone_linvels)*self.controller. body_drone_linvels**2
self.forces[:,0,2] = common_thrust
self.forces[:,0,:] += self.friction[:,0,:]
```
reward function

```
# position
target_dist = torch.sqrt(torch.square(target_root_positions - root_positions).sum(-1))
target_dist_next = torch.sqrt(torch.square(target_next_positions - root_positions).sum(-1))
target_dist_next_next = torch.sqrt(torch.square(target_next_next_positions - root_positions).sum(-1))
d = 0.4
pos_reward = 1 / (1 + target_dist * target_dist) + d / (1 + target_dist_next * target_dist_next) + d*d / (1 + target_dist_next_next * target_dist_next_next)

# access
access = target_dist.clone()
access[target_dist>=0.01] = 0
access[target_dist<0.01] = 10

# velocity
velocity = torch.norm(root_linvels, dim=-1)
velocity_reward = velocity.clone()
# velocity_reward[velocity>=1] = 0
# velocity_reward[velocity<=0.2] = 0

reward = pos_reward + access + 0.1*velocity_reward
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
self.forces[:,0,2] = common_thrust
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

<img src="https://github.com/zerojuhao/record/blob/main/image/4.gif" style="width: 400px; height: auto;">

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

<img src="https://github.com/zerojuhao/record/blob/main/image/5.gif" style="width: 400px; height: auto;">

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

<img src="https://github.com/zerojuhao/record/blob/main/image/6.gif" style="width: 400px; height: auto;">

## test 7
I guess the friction should be limited to a reasonable range.
```
self.friction[:, 0, :] = -0.02 * torch.sign(self.root_linvels) * self.root_linvels ** 2
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

<img src="https://github.com/zerojuhao/record/blob/main/image/7.gif" style="width: 400px; height: auto;">

## test 8
calculation of force and friction
```
self.friction[:, 0, :] = -0.001*torch.sign(self.root_linvels)*self.root_linvels**2
self.friction = torch.clamp(self.friction, -0.005, 0.005)
self.forces = self.friction.clone()
self.forces[:,0,2] += common_thrust
```

NEW reward function

```
# Drone is only rewarded when flying towards a target point
d = 0.4
pos_reward = target_dist.clone()
pos_reward[(last_target_dist-target_dist)<=0] = 0
pos_reward[(last_target_dist-target_dist)>0] = 0.8/(1+10*target_dist[(last_target_dist-target_dist)>0])+d/(1+10*target_dist_next[(last_target_dist-target_dist)>0])+d*d/(1+10*target_dist_next_next[(last_target_dist-target_dist)>0])

velocity = torch.norm(root_linvels, dim=-1)
velocity_reward = velocity.clone()
velocity_reward[velocity>=1] = 0

reward = pos_reward + access
```
<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/drone_linvel_8.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/linvel_8.png" style="width: 400px; height: auto;">
</div>

<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/friction_8.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/force_8.png" style="width: 400px; height: auto;">
</div>

# Weekly Summary


