---
title: "Humble与Jazzy导航的差异"
---

## 参考的官方 Jazzy 示例

- `/opt/ros/jazzy/share/nav2_bringup/params/nav2_params.yaml`
- `/opt/ros/jazzy/share/nav2_bt_navigator/behavior_trees/navigate_to_pose_w_replanning_and_recovery.xml`
- `/opt/ros/jazzy/share/nav2_bt_navigator/behavior_trees/navigate_through_poses_w_replanning_and_recovery.xml`

## 修改内容

可以复制一个humble的功能包并命名为 `mycar_navigation2_jazzy`,然后拿这个功能包进行改动.

1. 将功能包名保持为 `mycar_navigation2_jazzy`，并在相关位置补充修改边界注释。
   - `package.xml`
   - `CMakeLists.txt`

2. 将 `_jazzy` 包内 launch 文件的包路径查询保持为 `mycar_navigation2_jazzy`，并补充修改边界注释。
   - `launch/nav2.launch.py`
   - `launch/bringup.launch.py`
   - `launch/auto_slam.launch.py`

3. 对照 Jazzy 官方 `bt_navigator` 示例更新 `params/bt.yaml`。
   - 删除 Humble 时代列出的 Nav2 内置 BT 插件列表。
   - Jazzy 会自动加载 Nav2 内置 BT 插件，`plugin_lib_names` 只保留自定义 BT 插件。
   - 没有自定义 BT 插件时不传 `plugin_lib_names`，避免空列表被解析成无类型参数。
   - 新增 `navigators`、`navigate_to_pose`、`navigate_through_poses`。
   - 新增 `error_code_names`，用于配合 Jazzy 官方行为树里的 error code blackboard 变量。

4. 对照 Jazzy 官方 pluginlib 类名更新 planner 和 behavior 插件。
   - `nav2_navfn_planner/NavfnPlanner` 改为 `nav2_navfn_planner::NavfnPlanner`
   - `nav2_behaviors/Spin` 等改为 `nav2_behaviors::Spin` 等

5. 对照 Jazzy 官方行为树更新自定义 BT XML。
   - 添加 `BTCPP_format="4"`。
   - 添加 `PlannerSelector` 和 `ControllerSelector`。
   - `ComputePathToPose`、`ComputePathThroughPoses`、`FollowPath` 添加 `error_code_id`。
   - 添加 `WouldAPlannerRecoveryHelp` 和 `WouldAControllerRecoveryHelp`。

6. 对照 Jazzy 官方 controller 参数名更新进度检查器参数。
   - `progress_checker_plugin` 改为 `progress_checker_plugins: ["progress_checker"]`

7. 整理 planner/controller 参数文件的 YAML 结构。
   - 将重复的 `/**` 顶层键合并成一个。
   - `global_costmap` 和 `local_costmap` 挂在同一个 `/**` 根键下，避免参数解析时覆盖 server 参数。

## 主要修复的问题

启动时报错：

```text
Failed to create navigator id navigate_to_pose. Exception: ID [ComputePathToPose] already registered
```

原因是 Humble 配置在 `plugin_lib_names` 中手动列出了 Nav2 内置 BT 插件，而 Jazzy 已经自动加载这些内置插件，导致 `ComputePathToPose` 被重复注册。

## 使用方式

重新构建并 source：

```bash
colcon build --symlink-install --packages-select mycar_navigation2_jazzy
source install/setup.bash
```

启动 Jazzy 版本：

```bash
ros2 launch mycar_navigation2_jazzy bringup.launch.py use_sim_time:=True
```

## 已验证

已执行：

```bash
colcon build --symlink-install --packages-select mycar_navigation2_jazzy
ros2 launch mycar_navigation2_jazzy nav2.launch.py --show-args
ros2 launch mycar_navigation2_jazzy bringup.launch.py --show-args
```

另外用 `timeout` 做过短启动检查：

```bash
ros2 launch mycar_navigation2_jazzy bringup.launch.py use_sim_time:=True
```

结果：

- `bt_navigator` 成功创建 `navigate_to_pose` 和 `navigate_through_poses`。
- `planner_server` 成功加载 `nav2_navfn_planner::NavfnPlanner`。
- `controller_server` 成功加载 DWB 和所有 critics。
- `behavior_server` 成功加载 `nav2_behaviors::...` 插件。
- 在没有 Gazebo/robot_state_publisher/TF 的短启动环境里，最后停在等待 `base_link -> map` TF，这是预期的运行环境缺失，不是配置 fatal。
