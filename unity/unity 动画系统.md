unity 的动画系统基于动画剪辑的概念，动画剪辑包含某些对象如何随时间改变其位置，旋转或其他属性的相关信息
每个动画可视为单个线性录制的，来自外部的动画剪辑由美术师或动画师使用第三方工具创建而成，或者来自自由动作捕捉室或其他来源

然后动画剪辑将编入一个 成为 animator controller 的一个类似流程图的结构化系统中, animator controller 充当状态机，负责跟踪当前应该播放哪个剪辑以及动画应该何时或混合在一起

一个非常简单的 Animator Controller 可能只包含一个或两个剪辑，例如，使用此剪辑来控制能量块旋转和弹跳，或设置正确时间开门和关门的动画。一个更高级的 Animator Controller 可包含用于主角所有动作的几十段人形动画，并可能同时在多个剪辑之间进行混合，从而当玩家在场景中移动时提供流畅的动作。

Unity 的动画系统还具有用于处理人形角色的许多特殊功能。这些功能可让人形动画从任何来源（例如：动作捕捉、Asset Store 或某个其他第三方动画库）重定向到您的角色模型中，并可调整肌肉定义。这些特殊功能由 Unity 的替身系统启用；在此系统中，人形角色会被映射到一种通用的内部格式中。

所有这些部分（动画剪辑、Animator Controller 和 Avatar）都通过 Animator 组件一起附加到某个游戏对象上。该组件引用了Animator Controller，并（在必需时）引用此模型的 Avatar。Animator Controller又进一步包含所使用的动画剪辑的引用。

### 根运动

动画在特定层级视图中移动和旋转所有游戏对象，这些移动和旋转大多数都是相对于父对象完成的，但是层级视图的父游戏对象没有父项，因此他们的移动不是相对的，此父游戏对象也可以成为跟，因此其移动成为根运动



