# Star Arm 102 ACT 方块放置模型

这是使用 LeRobot 训练的 Star Arm 102 方块放置策略。模型输入包含机械臂状态，以及两路 640 × 480 相机画面：`up`（上方视角）和 `front`（正面视角）。训练完成于 100,000 步。

仓库中的 [客户运行指南](docs/Star_Arm_102_ACT_客户运行指南.docx) 说明环境准备、模型放置位置、设备参数和单组验证步骤。完整模型作为本仓库的 [v1.0.0 Release 附件](https://github.com/VincentHSS/star-arm-102-act-model/releases/tag/v1.0.0) 提供。

## 下载和放置模型

在 Ubuntu 电脑上下载 [`stararm102_pick_act_torch271.tar.gz`](https://github.com/VincentHSS/star-arm-102-act-model/releases/download/v1.0.0/stararm102_pick_act_torch271.tar.gz)，然后执行：

```bash
mkdir -p ~/models/stararm102_pick_act_torch271
tar -xzf stararm102_pick_act_torch271.tar.gz -C ~/models/stararm102_pick_act_torch271
```

模型路径应为：

```text
~/models/stararm102_pick_act_torch271/pretrained_model
```

运行命令中的 `--policy.path` 要指向这个目录。请保留其中所有配置、权重和处理器文件。

## 运行前准备

- Ubuntu、Python 3.10、LeRobot 0.4.1，以及适配电脑 GPU 的 PyTorch。
- 安装 Star Arm 102 的 LeRobot 从臂驱动，完成机械臂校准。
- 接好两台相机，并确认 `up` 和 `front` 分别对应训练时的上方视角和正面视角。
- 将桌面、相机位置、光照和方块放置区域调整到与训练示范相近的状态。

具体安装与检查步骤见客户运行指南。首次运行先测试 1 组，并准备好硬件急停。

## 单组验证示例

先用 `lerobot-find-cameras opencv` 和设备枚举确认实际相机与串口，再修改下面命令中的设备路径：

```bash
lerobot-record \
  --robot.type=lerobot_robot_stararm102 \
  --robot.port=/dev/ttyUSB1 \
  --robot.id=customer_stararm102_follower \
  --robot.cameras='{up: {type: opencv, index_or_path: /dev/video0, width: 640, height: 480, fps: 30}, front: {type: opencv, index_or_path: /dev/video1, width: 640, height: 480, fps: 30}}' \
  --policy.path="$HOME/models/stararm102_pick_act_torch271/pretrained_model" \
  --display_data=true \
  --dataset.repo_id=customer/eval_stararm102_pick_run1 \
  --dataset.root="$HOME/lerobot_data/eval_stararm102_pick_run1" \
  --dataset.num_episodes=1 \
  --dataset.episode_time_s=30 \
  --dataset.reset_time_s=10 \
  --dataset.single_task="将方块放入中心" \
  --dataset.push_to_hub=false
```

该命令会驱动从臂并保存一次本地评估记录。测试下一组时，给 `--dataset.repo_id` 和 `--dataset.root` 换一个未使用过的名称。

## 模型信息

| 项目 | 值 |
| --- | --- |
| 策略 | ACT |
| 任务 | 将方块放入中心 |
| 训练步数 | 100,000 |
| 输入 | 7 维机械臂状态；`up` 和 `front` 两路 RGB 画面 |
| 输出 | 7 维动作 |
| 相机规格 | 640 × 480，30 FPS |
| 模型包 | `stararm102_pick_act_torch271.tar.gz`，191,108,326 字节 |

模型压缩包的 SHA-256 校验值见 [SHA256SUMS.txt](SHA256SUMS.txt)。模型适用于训练示范覆盖的任务和工作范围；部署前请完成实机验证。

## 相关资料

- [Star Arm 102 LeRobot 参考文档](https://github.com/servodevelop/Star-Arm-102/blob/0896306e40891c3ee4c97228e85dd708d61326de/Lerobot/stararm102.m)
- [PyTorch 官方安装页面](https://pytorch.org/get-started/locally/)
