以下是将上述代码和逻辑转换为 GitHub Markdown 格式的内容：

```markdown
# 实现5秒无伤后每秒回复生命值的 Buff

该文档展示了如何实现一个在过去5秒内未受到伤害或敌方英雄技能命中时，每秒回复自身一定比例已损失生命值的 **Buff**。该机制可以用于类似 MOBA 游戏的角色技能或被动效果。

## 1. 关键逻辑步骤

1. **检测伤害和技能命中**：每当英雄受到伤害或被敌方技能命中时，重置一个计时器记录该事件。
2. **条件满足时启动生命恢复**：当计时器超过5秒，且期间没有受到任何伤害或技能命中时，启动每秒的生命恢复。
3. **基于损失生命值进行回复**：每秒恢复1.3%（或随等级成长的数值）的已损失生命值。

## 2. 实现步骤

### a) 定义基础变量

```python
lastHitTime = 0  # 最近一次受到伤害的时间
buffActive = False  # 是否激活生命恢复Buff
timeSinceLastHit = 0  # 从最近一次受伤到当前的时间差
healPerSecond = 1.3 / 100  # 每秒恢复的已损失生命值百分比
```

### b) 受到伤害或技能命中时的处理逻辑

每当英雄受到伤害或敌方技能命中时，重置 `lastHitTime`。

```python
def on_receive_damage_or_skill_hit():
    global lastHitTime, buffActive
    # 记录当前时间为最近一次受伤时间
    lastHitTime = current_time
    # 停止生命回复
    buffActive = False
```

### c) 5秒无伤检测

定时每帧检查是否已经超过5秒没有受到伤害或技能命中。如果是，则启用生命恢复 Buff。

```python
def update():
    global buffActive
    timeSinceLastHit = current_time - lastHitTime
    
    # 检查是否超过5秒没有受伤
    if timeSinceLastHit >= 5:
        buffActive = True  # 激活Buff
    else:
        buffActive = False  # 没有满足条件，保持Buff关闭
```

### d) 每秒回复生命值

当 `buffActive` 为真时，开始每秒恢复一定比例的已损失生命值。恢复量与已损失生命值有关。

```python
def apply_healing():
    global current_health
    if buffActive:
        # 计算已损失生命值
        missing_health = max_health - current_health
        # 每秒恢复1.3%的已损失生命值
        heal_amount = missing_health * healPerSecond
        # 更新当前生命值
        current_health = min(current_health + heal_amount, max_health)
```

### e) 随等级成长的回复比例

恢复比例可以随着英雄等级增长，每级提升0.1%。

```python
def calculate_heal_rate(level):
    # 每升一级，恢复率增加0.1%
    base_heal_rate = 1.3 / 100
    additional_rate = 0.1 / 100 * (level - 1)
    return base_heal_rate + additional_rate
```

## 3. 综合逻辑实现

以下是一个完整的 Hero 类实现，包含了所有上述逻辑：

```python
class Hero:
    def __init__(self, max_health, level):
        self.max_health = max_health
        self.current_health = max_health
        self.lastHitTime = -5  # 初始化为-5秒，以免一开始就触发Buff
        self.buffActive = False
        self.level = level
        self.heal_rate = self.calculate_heal_rate()

    def on_receive_damage_or_skill_hit(self):
        self.lastHitTime = current_time
        self.buffActive = False

    def update(self):
        timeSinceLastHit = current_time - self.lastHitTime
        if timeSinceLastHit >= 5:
            self.buffActive = True
        else:
            self.buffActive = False

        if self.buffActive:
            self.apply_healing()

    def apply_healing(self):
        missing_health = self.max_health - self.current_health
        heal_amount = missing_health * self.heal_rate
        self.current_health = min(self.current_health + heal_amount, self.max_health)

    def calculate_heal_rate(self):
        base_heal_rate = 1.3 / 100  # 转化为0.013
        additional_rate = 0.1 / 100 * (self.level - 1)
        return base_heal_rate + additional_rate
```

## 4. 解释与总结

- **受到伤害/技能命中检测**：每当受到伤害或者被敌方技能命中，记录时间并停止生命恢复效果。
- **时间检测**：不断检查时间是否已超过5秒，如果超过5秒且没有再受伤，则激活生命恢复Buff。
- **基于损失生命值的回复**：每秒按照英雄的已损失生命值按比例恢复一定量，回复速度随等级成长变化。

该逻辑可以为游戏角色提供一个强大的恢复机制，提高英雄的续航能力。
```

### 使用说明

- 复制上述内容粘贴到 GitHub 仓库的 `.md` 文件中即可。
- 代码片段使用了 GitHub Markdown 的 ` ```python ` 代码块格式，确保语法高亮。
