---
layout: post
title:  "一点 ROS2 & rclnodejs 初步尝试"
date:   2023-07-01 09:41:44 +0800
categories: ROS2 rclnodejs 
---

# 背景

我也不是谦虚，我一个没摸过机器人的怎么就去搞 ROS 了呢？  

我们这届拿到了做基于 ROS 的巡检机器人的小项目，虽然因为都是新手、疫情干扰、和另外的负责人对接效率不高等各种问题最后拿出来的东西并不算好，或者说是很糟，至于「产品」更是还有十万八千里，但是姑且有了一些经验。最近试图慢慢迁移到 ROS2 上（虽然已经和项目无关了），算是一点记录吧。

## 现在有的

现在的成果大概主要就是 [mobile_manipulator_simulated](https://github.com/Steve-Mr/mobile_manipulator_simulated), [scout_simulator_with_robosense](https://github.com/Steve-Mr/scout_simulator_with_robosense), [cartographer_with_rslidar_and_scout](https://github.com/Steve-Mr/cartographer_with_rslidar_and_scout) 这三个仓库，分别是最初跟着教程、基于教程魔改和尝试 cartographer 建图算法搞的。如果有一些经验大概就能发现其实我搞了这么长时间还是搭积木，几乎没有什么自己的东西，所以可以说这东西成为了我的焦虑源泉。

### rosnodejs

和这篇文章强相关的是 [ros_nodejs_backend](https://github.com/Steve-Mr/ros_nodejs_backend) 这个仓库，原本的计划是根据项目甲方那边之前使用的系统做一个兼容的 API 出来，不过一方面没有完全实现、实现的部分也很糟糕说实话，另一方面似乎他们现在也没有在用这套东西的打算，所以这东西基本上就放在这里了。  

这个仓库主要依赖的是 [rosnodejs](https://github.com/RethinkRobotics-opensource/rosnodejs) 这个库来与 ROS 节点通信，虽然我现在其实也没完全用明白，但是他提供的一些示例代码基本上覆盖了我的需求，还是不错的。

## 现在目标

迁移到 ROS2。  

### rclnodejs

这部分就是 [ros_nodejs_backend](https://github.com/Steve-Mr/ros_nodejs_backend) 的 humble 分支了，因为需要适配 ROS2 所以换用了 [rclnodejs](https://github.com/RobotWebTools/rclnodejs) 库，其当前支持的是 LTS 和 Rolling 版本的 ROS2，所以选择了 Humble 版本。  

rclnodejs 也是非常不错的项目，但是由于我事实上跳过了很多需要学习的东西，所以踩了一些坑，大概就是记录一下一些基础功能的实现。

# 文档

首先提这个是因为 rclnodejs 的文档目前托管在 [robotwebtools](robotwebtools.org/rclnodejs/docs/index.html) 这里，但是这个站目前无法访问，所以需要克隆到本地手动使用 `npm run docs` 来在本地生成文档。

# 订阅

订阅部分参考官方的[示例](https://github.com/RobotWebTools/rclnodejs/blob/17a0a7f001bc59c2e85417001f0348490893aa08/example/subscription-qos-example.js)其实就足够，需要注意的只有一点小问题。

1. 实例化 Node
    示例代码中使用的 `rclnodejs.createNode()` 方法已经弃用了，现在需要使用 `new rclnodejs.Node()`

2. Latched Topic
    这部分实际上是 ROS2 的改动，同时需要注意的是这部分为个人理解，很有可能包含事实性错误。  
    
    ROS1 中的 Latched Topic 主要是在信息发送后会将最后一条消息保留，当有新的订阅者订阅话题时就可以立即获得信息而不需要等待话题更新。  

    在 ROS2 中这部分被更加复杂的 [QoS 控制](https://docs.ros.org/en/humble/Concepts/Intermediate/About-Quality-of-Service-Settings.html)替代了，具体的说明参见文档，这里只讨论实现 latching 效果的 QoS，这部分要求发送者和订阅者的 QoS 均设置 Reliability 为 RELIABLE，Durability 为 TRANSIENT_LOCAL。

    更具体到 rclnodejs 中可以考虑以下方式的实现

    ```javascript
    let qos_map = new rclnodejs.QoS
          qos_map.depth = 10
          qos_map.history = rclnodejs.QoS.HistoryPolicy.RMW_QOS_POLICY_HISTORY_SYSTEM_DEFAULT
          qos_map.durability = rclnodejs.QoS.DurabilityPolicy.RMW_QOS_POLICY_DURABILITY_TRANSIENT_LOCAL
          qos_map.reliability = rclnodejs.QoS.ReliabilityPolicy.RMW_QOS_POLICY_RELIABILITY_RELIABLE

          const subscriber_map = mapInfoNode.createSubscription(
            'nav_msgs/msg/OccupancyGrid',
            'map', {qos: qos_map},
            (map) => {
              console.log(map.info)
              mapInfoNode.destroySubscription(subscriber_map);
              mapInfoNode.destroy();
            }
          )
    ```

# Action Client

这部分的主要作用是导航目标的发送，nav2 相比 navigation_stack 看起来能处理更多的情况了，不过这里还是只考虑最简单的单个目标点的情况。官方仓库里的[示例](https://github.com/RobotWebTools/rclnodejs/blob/17a0a7f001bc59c2e85417001f0348490893aa08/example/action-client-example.js)已经非常完善，但是其使用的是费波纳契数目标，对于我的需求有点对不上。

插一句这里的问题是我对于 nav2 的不了解导致无从下手，确实是我跳过太多内容的问题，只能说该补的课还是得补。

1. Action Client 定义
    `typeClass`: `nav2_msgs/action/NavigateToPose`
    `actionName`: `/navigate_to_pose`
2. waitForResult()
    这里主要需要等待结果的返回使用 await 方法或者直接处理 promise： `const result = await goalHandle.getResult();`，但是这里返回的 `result` 实际上为空信息所以仍然需要 `ClientGoalHandle` 类的 `isSucceeded()`, `isCanceled()`, `isAborted()` 等方法来判断具体的状态。

目前的代码如下

```javascript
class MoveBaseActionClient {
    constructor(node) {
        this._node = node;

        this._actionClient = new rclnodejs.ActionClient(
            node,
            'nav2_msgs/action/NavigateToPose',
            '/navigate_to_pose'
        );

        this.goalPaused = false;
    }

    async sendGoal(goal) {
        this._node.getLogger().info('Waiting for action server...');
        await this._actionClient.waitForServer();

        this._node.getLogger().info('Sending goal request...');

        try {
            const goalHandle = await this._actionClient.sendGoal(goal);
            this._goalHandle = goalHandle;

            if (!goalHandle.isAccepted()) {
                this._node.getLogger().info('Goal rejected');
                return STATE_ERROR;
            }

            this._node.getLogger().info('Goal accepted');

            // Wait for the goal to be completed or canceled
            const result = await goalHandle.getResult();

            // Check the final status of the goal
            if (goalHandle.isSucceeded()) {
                return STATE_SUCCEEDED
            } else {
                if (goalHandle.isCanceled()) {
                    if (this.goalPaused) {
                        this.goalPaused = false
                        return STATE_PAUSED
                    } else {
                        return STATE_CANCELLED
                    }
                }
                if (goalHandle.isAborted()) {
                    return STATE_ERROR
                }
            }
        } catch (error) {
            // Handle possible exceptions
            this._node.getLogger().error(error.message);
            return STATE_ERROR;
        }
    }

    async cancelGoal(paused = false) {
        this._node.getLogger().info('Canceling goal');
        this.goalPaused = paused
        const response = await this._goalHandle.cancelGoal();

        if (response.goals_canceling.length > 0) {
            this._node.getLogger().info('Goal successfully canceled');
            return true
        } else {
            this._node.getLogger().info('Goal failed to cancel');
            this.goalPaused = false
            return false
        }
    }
}
```