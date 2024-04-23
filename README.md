# Record for friction 




## test 1
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
```
self.friction[:, 0, :] = 0.02*torch.sign(self.controller.body_drone_linvels)*self.controller. body_drone_linvels**2
self.forces = self.friction.clone()
self.forces[:,0,2] = common_thrust
```
<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/drone_linvel_4.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/linvel_4.png" style="width: 400px; height: auto;">
</div>

<div style="display: flex;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/friction_4.png" style="width: 400px; height: auto;">
  <img src="https://github.com/zerojuhao/record/blob/main/image/force_4.png" style="width: 400px; height: auto;">
</div>

## test 5
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