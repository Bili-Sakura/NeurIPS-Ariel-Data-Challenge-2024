# 数据集描述

对系外行星化学的特征描述是天文学中一个非常活跃的研究项目。欧洲空间局的大气遥感红外系外行星大规模巡天（ARIEL）任务将通过观测系外行星在其母恒星前方经过时，收集大约 1000 颗系外行星的数据。即使使用 ARIEL 上的强大仪器，所得数据也会基于有限数量的光子并包含相当数量的噪声。在此竞赛中，你的挑战是使用模拟的 ARIEL 数据提取系外行星大气的化学光谱。

本次比赛使用一个隐藏的测试集。当你提交的 notebook 进行评分时，实际的测试数据（包括一个完整的样本提交）将会提供给你的 notebook。预计在隐藏的测试集中会有大约 800 颗系外行星。

测试集中的一些系外行星模拟直接基于真实的系外行星。但所有这些情况都将在评分时被忽略。

### 文件信息

- **文件数量**：8094 个文件
- **大小**：174.33 GB
- **类型**：parquet, csv
- **许可证**：[Attribution-NonCommercial 4.0](https://creativecommons.org/licenses/by-nc/4.0/)

## 元数据文件

- **[train/test]\_adc_info.csv**：包含模数转换（ADC）参数（增益和偏移），用于恢复数据的原始动态范围。还包括一个恒星列，用于标识用于该行星模拟的恒星。
- **train_labels.csv**：包含地面真实光谱。
- **axis_info.parquet**：两个仪器的轴信息。
- **wavelength.csv**：数据集中每个地面真实光谱的波长网格。

## 信号文件

数据集由两个独立仪器的时间序列图像以及校准数据组成。Ariel 包含多个光学仪器，每个仪器专门用于不同的光谱波段和观测模式。FGS1 是 Ariel 精细制导系统（FGS）的第一个通道。精细制导系统的主要任务是实现卫星的中心定位、聚焦和引导，但它还将在可见光谱中提供高精度的目标恒星光度测量，其灵敏度介于 0.60 至 0.80 μm 之间。AIRS-CH0 是 Ariel 红外光谱仪（AIRS）的第一个通道（CH0），是一种红外光谱仪，灵敏度介于 1.95 至 3.90 μm 之间，具有约 R=100 的分辨率。有关 Ariel 的更多信息，请访问 [Ariel 红皮书](https://sci.esa.int/web/ariel/-/ariel-definition-study-report-red-book)。

查看来自任一仪器的帧时，你会注意到通量水平在高低计数之间来回波动。这是设计观测模式时的特意设计。望远镜检测器中的每个像素都充当充电累加器。过程从记录累加前的初始电荷水平的子曝光开始。在设定的曝光时间后，另一个子曝光捕获新的电荷水平。然后重置像素，循环重复，直至观察结束。这使得可以捕获详细的时间信息，尤其适用于观察快速变化，例如行星凌日过程中的变化。在这次观测中，该过程为 FGS1 生成了 135,000 帧，为 AIRS-CH0 生成了 11,250 帧。

- **[train/test]/[planet_id]/AIRS-CH0_signal.parquet**：AIRS-CH0 仪器的信号数据。每个文件包含 11,250 行图像，这些图像在 axis_info.parquet 文件中记录的恒定时间步长下捕获。每个 32 x 356 图像已展平为 11,392 列。你可以使用 `numpy.reshape(11250, 32, 356)` 将数据重新塑形为原始形状。仪器生成的数据为 uint16。要恢复完整的动态范围，必须将数据乘以 **[train/test]\_adc_info.csv** 中的匹配“增益”值，然后加上 **[train/test]\_adc_info.csv** 中的“偏移”值。
- **[train/test]/[planet_id]/FGS1_signal.parquet**：FGS1 仪器的信号数据。每个文件包含 135,000 行图像，时间步长为 0.1 秒。每个 32x32 图像被展平为 1,024 列。与 AIRS-CH0 类似，数据以 uint16 形式生成。要恢复其原始动态范围，必须将数据乘以 **[train/test]\_adc_info.csv** 中的匹配增益值，然后加上 **[train/test]\_adc_info.csv** 中的偏移值。

## 校准文件

校准文件记录传感器的电子特性，并用作图像后处理中用于图像信噪比的“支持帧”。[这个网站](https://practicalastrophotography.com/a-brief-guide-to-calibration-frames/)包含了关于校准帧概念的简要介绍。[这个 notebook 也可能有用](https://www.kaggle.com/code/gordonyip/calibrating-and-time-binning-astronomical-data)。校准文件在训练集和测试集中是重复的，但测试集中的数据会有所变化。

- **[train/test]/[planet_id]/[AIRS-CH0/FGS1]\_calibration/dark.parquet**：暗帧是在快门关闭时拍摄的曝光，记录传感器的热噪声和偏置电平，用于从科学图像中减去暗电流。
- **[train/test]/[planet_id]/[AIRS-CH0/FGS1]\_calibration/dead.parquet**：识别传感器上的坏死像素或热像素。坏死像素不响应光线，而热像素无论有无入射光都会始终产生高信号电平。
- **[train/test]/[planet_id]/[AIRS-CH0/FGS1]\_calibration/flat.parquet**：平场帧是通过对均匀照明表面成像创建的，用于校正像素间灵敏度和光学系统不规则性。
- **[train/test]/[planet_id]/[AIRS-CH0/FGS1]\_calibration/linear_corr.parquet**：传感器线性校正信息。当探测器中的像素充满电子时，其响应会变得不那么线性，接近饱和点，此时像素无法再收集额外的电子，其对光的响应变得平坦。为了准确估计信号，校准了仪器响应与接收到的电荷之间的关系，校正是使用 n 次多项式计算的。这个多项式允许将像素收集/测量到的电子数转换为探测器在线性响应下生成的电子数。
- **[train/test]/[planet_id]/[AIRS-CH0/FGS1]\_calibration/read.parquet**：读出噪声帧捕获传感器读出过程中的电子噪声。这种噪声即使在没有光照射探测器时也会存在。
