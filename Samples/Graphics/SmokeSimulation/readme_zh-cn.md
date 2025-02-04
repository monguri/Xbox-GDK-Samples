![](./media/image1.png)

# 渲染模拟

*此示例可用于 Microsoft 游戏开发工具包 (2022 年 3 月)*

# 说明

此时李演示如何使用计算着色器 6.0 和 3D 纹理实现基本的 3D 纳维-斯托克斯流量模拟。 该示例还演示如何使用简单的光线匹配算法呈现测量体积的数据。

![](./media/image3.png)

# 生成示例

如果使用 Xbox One 开发工具包，请将活动解决方案平台设置为 `Gaming.Xbox.XboxOne.x64`。

如果使用 Xbox Series X|S 开发工具包，请将活动解决方案平台设置为 `Gaming.Xbox.Scarlett.x64`。

*有关详细信息，请参阅* __运行示例__，*在 GDK 文档中。*

# 使用示例

此示例使用以下控件。

| 操作 | 游戏板 |
|---|---|
| 退出示例。 | 选择 |
| 旋转相机 | 右摇杆 |
| 旋转发射器 | 左摇杆 |
| 移动发射器 | 左/右扳机键 |
| 重置发射器方向 | X 按钮 |
| 切换模拟暂停 | A 按钮 |
| 重置模拟 | B 按钮 |

# 实现说明

**模拟技术**

有关此示例中使用的模拟技术的详细信息，请参阅 GPU Gems 第 38 章 GPU 上的快速流体动力学模拟。 虽然本文介绍模拟技术并将其应用于 2D 流体，但本示例将其扩展到 3D 数据。 在此示例中，模拟在 128x128x128 网格上运行，该网格的状态存储在 3D 纹理中。

使用计算着色器 5.0，我们可以一次性直接操作 3D 纹理的所有切片。 这比采用几何着色器（可用于指定要更新的 3D 纹理切片）的替代策略更高效。

**绘制技术**

模拟的结果是一个 3D 速度字段，它表示流体在某个模拟步骤中的状态。 由于我们无法直接看到速度，因此我们需要有跟踪粒子，可以看到这些粒子由速度字段携带。 因此，除了模拟本身所需的所有 3D 纹理以外，我们还有一个存储这些粒子的 3D 纹理。 此 3D 纹理是我们在绘制阶段可视化的。

绘制时，我们将绘制多维数据集的前面。 在像素着色器中，我们将光线从眼睛位置强制转换为多维数据集上的当前点，并以固定的时间间隔沿光线采样最后一段中提到的 3D 纹理，同时累积颜色和不透明度。

该示例还演示了用于光线转换算法的简单而有效的加速技术。 在模拟期间，使用并行缩减生成 1/8 大小的 3D 纹理。 绘制时，我们首先对这个 1/8 大小的 3D 纹理进行采样，如果采样的值小于阈值，则跳过空间中的块，而不是以固定间隔直接采样原始 3D 纹理，并且可能会浪费大量时间采样空白空间。 当 3D 纹理中存储的数据稀疏时，此技术尤其有效，就像我们在这里渲染一样。

# 更新历史记录

2019 年 4 月 -- 从旧版 Xbox 示例框架移植到新模板。

# 隐私声明

在编译和运行示例时，将向 Microsoft 发送示例可执行文件的文件名以帮助跟踪示例使用情况。 若要选择退出此数据收集，你可以删除 Main.cpp 中标记为&ldquo;示例使用遥测&rdquo;的代码块。

有关 Microsoft 的一般隐私策略的详细信息，请参阅 [Microsoft 隐私声明](https://privacy.microsoft.com/en-us/privacystatement/)。


