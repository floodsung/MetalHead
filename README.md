
# MetalHead: Natural Locomotion and Jumping of Quadruped Robot A1 with AMP

The core contribution of this repository is to make the quadruped robot A1 being able to do natural locomotion and jumping like the video below. This repository is based off of Alejandro Escontrela's [AMP_for_hardware](https://github.com/Alescontrela/AMP_for_hardware). All experiments are running on [Isaac Gym](https://developer.nvidia.com/isaac-gym).

[![Watch the video](https://img.youtube.com/vi/IdzfE9rXoqY/maxresdefault.jpg)](https://youtu.be/IdzfE9rXoqY)
Click above image to watch the Video!

## Installation

Please just following [AMP_for_hardware](https://github.com/Alescontrela/AMP_for_hardware)'s installation instruction.


## How to Run
```
python legged_gym/scripts/play.py --task=a1_amp_jump_cmd --num_envs=64 --load_run=example
```
Press w,a,s,d to change the speed, and press Space to make it jump!

## How to Train
```
python legged_gym/scripts/train.py --task=a1_amp_jump_cmd --headless True
```
25000 iterations of training can be enough to show the performance.

## Observation additions
- Added root_h, root_euler[:, :2], flat_local_key_pos to obs, representing the absolute height of the root, the rotation angle of the root, and the relative coordinates of the four foot ends in the root coordinate system, respectively
- Added jump_sig to obs, indicating whether the jump command is triggered
- policy_obs and critic_obs are consistent
- amp_policy_obs removes commands and jump_sig from the policy_obs

## Action changes
- Changed action to position PD control, using the set_dof_position_target_tensor API
- Policy inference frequency is 200 / 6 Hz, with a physical simulation frequency of 200 Hz and action repetition count of 6

## Reward additions
- Added _reward_jump_up for calculating task rewards

## Random initialization
- recovery_init_prob = 0.15, with a 15% probability of random initialization, added _reset_root_states_rec function for random sampling in three Euler angle directions

## Mocap data
- For command-based locomotion+jump, motion capture data includes gallop_forward0, gallop_forward1, jump0, jump1, jump2, trot_forward0, turn_left0, turn_left1, turn_right0, turn_right1, where trot is the same side two legs
- In the JSON file, the weight of the jump data is set to 1.5, and the others are 0.5

## Play camera tracking
- In MOVE_CAMERA mode, the camera follows the robot's root at a fixed angle, and the camera's position and yaw angle relative to the robot remain unchanged

## Some key parameters
- action_scale=0.75, too large or too small cannot achieve command jump
- all_stiffness = 80.0, all_damping=1.0, good PD parameters can facilitate simulation training, and more importantly, have a greater impact on the difficulty of sim2real transfer
- amp_task_reward_lerp = 0.3, controls the weight of task reward and style reward
- disc_grad_penalty = 0.01, smaller penalty is needed for high-dynamic mocap
- resampling_time = 2., episode_length_s=10., command sampling interval and episode length, in recovery_init mode, sampling interval has a greater impact on jump effect
- tracking_ang_vel = 0.1 * 1. / (.005 * 6), too small weight cannot follow angular velocity properly, maybe try heading tracking, which is more convenient in sim
- In random initialization mode, terminate_after_contacts_on is set to empty

## observation 新增
- obs增加`root_h`, `root_euler[:, :2]`, `flat_local_key_pos`, 分别表示root的绝对高度, root的转角以及四个足端在root坐标系下的相对坐标
- obs增加`jump_sig`, 表示是否触发jump command
- policy_obs和critic_obs一致
- amp_policy_obs在policy_obs基础上去掉`commands`以及`jump_sig`

## action 改动
- action改为位置PD控制，使用`set_dof_position_target_tensor` API
- policy inference频率200 / 6 Hz, 其中物理仿真200Hz, action重复次数为6次

## reward 新增
- 新增`_reward_jump_up`, 计算task奖励

## 随机初始化
- `recovery_init_prob = 0.15`, 以15%的概率随机初始化，新增`_reset_root_states_rec`函数，实现三个欧拉角方向上的随机采样

## mocap数据
- 对于command-based locomotion+jump, 动捕数据有gallop_forward0, gallop_forward1, jump0, jump1, jump2, trot_forward0, turn_left0, turn_left1, turn_right0, turn_right1，其中，tort是同侧两条腿
- json文件中，jump数据的weight设置为1.5, 其余0.5

## play 视角跟随
- 在`MOVE_CAMERA`模式下, 摄像头以固定视角跟随机器人的root, 摄像头相对于机器人的position以及yaw角不变

## 一些关键参数
- `action_scale=0.75`, 太大或者太小无法实现command jump
- `all_stiffness = 80.0`, `all_damping=1.0`, 一个好的PD参数能够方便仿真训练，更重要的是对sim2real的迁移难度影响较大
- `amp_task_reward_lerp = 0.3`, 控制task reward和style reward的权重
- `disc_grad_penalty = 0.01`, 在高动态的mocap需要较小的penalty
- `resampling_time = 2.`, `episode_length_s=10.`, command采样间隔以及回合长度, 在recovery_init模式下, 采样间隔对jump效果影响较大
- `tracking_ang_vel = 0.1 * 1. / (.005 * 6)`, 权重太小无法正常跟随角速度，或许可以尝试heading跟随，在sim中比较方便
- 在随机初始化模式下，`terminate_after_contacts_on`设置为空


## Highly Related Git
- [rsl_rl](https://github.com/leggedrobotics/rsl_rl)
- [legged_gym](https://github.com/leggedrobotics/legged_gym)
- [AMP_for_hardware](https://github.com/Alescontrela/AMP_for_hardware)

## Citing

If you use MetalHead in your research please use the following citation:

````
@misc{InspirAI,
  author = {Huiqiao Fu, Yutong Wu, Flood Sung},
  title = {MetalHead: Natural Locomotion and Jumping of Quadruped Robot A1 with AMP},
  year = {2023},
  publisher = {GitHub},
  journal = {GitHub repository},
  howpublished = {\url{https://github.com/inspirai/MetalHead}},
}