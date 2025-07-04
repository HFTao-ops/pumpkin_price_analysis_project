# pumpkin_price_analysis_project
## 数据预处理
![image](https://github.com/user-attachments/assets/ade89680-5d7c-4d05-87fb-10e0c130e631)数据集里面有26个特征，比较繁杂，为了加深对数据的了解，我先决定统计描述，和对空值探索

![image](https://github.com/user-attachments/assets/a66af0c2-55d7-4c15-8a3f-0258bd928948)
可以看到有一些列根本没有任何数据，所以直接把空值超过1600的列全部删掉，在处理完全空的列之后，我们发现四个价格列的数据成高度线性相关甚至相同。所以为了简化数据分布后续的建模分析我决定把Mostly Low	Mostly High删掉，然后对Low Price	High Price取平均值进行平滑处理。并把日期转化。

最终清晰后的数据集如下：
![image](https://github.com/user-attachments/assets/0f68e11d-a980-4a86-bfca-700cf3db523f)

## 数据探索
在这之后我决定对每一列的特征进行分析。
1.
![image](https://github.com/user-attachments/assets/bfd83526-8523-4637-a68c-15d5ae8b88c1)


从图中可以看到城市与价格之间有较为明显波动，所以我认为，他对价格的影响比较大，将这个特征列为重要特征。

2.![image](https://github.com/user-attachments/assets/cced4ee6-0a80-48e1-b832-499c95e62cc7)
3.![image](https://github.com/user-attachments/assets/698765a4-c405-4bca-bb5d-c92146b6c33a)
4.![image](https://github.com/user-attachments/assets/3326b614-550d-4ecb-80dc-df1fb1976b25)
5.![image](https://github.com/user-attachments/assets/dba1ef1a-0bd7-447c-a0f2-3ac51c2fac03)
6.![image](https://github.com/user-attachments/assets/f8860ce6-9372-49c2-944b-dda33e473506)
7.![image](https://github.com/user-attachments/assets/8e1fc57c-9aec-4a24-a2a7-7f3c3c9b9466)
以上这些特征通过图片都可以清晰看到他们与价格之间明显的波动。

在这个之后我还对时间序列进行了价格的分析，发现并不纯在明显的相关性。结果如下：
![image](https://github.com/user-attachments/assets/5e82a29c-5d1e-4a65-a56e-c509a58b7be4)
为了简化追求模型的泛化能力，在后续建模的过程中，我打算省略时间特征。
## 建模分析

在这个建模过程中，我的主要目标是使用决策树回归模型预测某个目标变量（例如 mean_price）。以下是我完成这个任务的思路和步骤：

数据预处理：

删除无关的列：首先，我删除了数据中的Date列，因为这列在回归分析中并不直接相关。
处理分类特征：数据中有多个分类特征，例如City Name、Package、Variety等。我填充了这些分类特征的缺失值，使用了LabelEncoder对这些类别进行编码，将它们转换成数值形式，以便于机器学习模型处理。
分离特征和目标变量：

我从数据中分离出了特征（X）和目标变量（y），目标变量是我们想要预测的值——mean_price。
数据集划分：

使用train_test_split将数据划分为训练集和测试集，其中80%的数据用于训练，20%的数据用于测试，确保模型在测试集上能够进行评估。
超参数调优：

为了选择最佳的决策树模型，我设置了一个参数网格（param_grid），包含决策树的不同超参数，如max_depth（树的最大深度）、min_samples_split（划分的最小样本数）、min_samples_leaf（叶子节点的最小样本数）和max_features（每次划分时考虑的最大特征数）。这些超参数会影响模型的复杂度和表现。
交叉验证与网格搜索：

我使用了五折交叉验证（KFold）来评估模型的表现，这有助于减少模型对特定数据集划分的依赖性，保证评估结果的稳定性。
使用GridSearchCV进行网格搜索，自动在设置的参数网格中寻找最佳组合。这一步的目的是找到最优的超参数，确保模型在训练集上的表现最佳。
模型训练和评估：

在网格搜索完成后，我得到了最优的参数，并使用这些参数重新训练了决策树模型。
训练完成后，我将模型应用于测试集，并评估了模型的表现。通过计算均方误差（MSE）、均方根误差（RMSE）和决定系数（R²），我可以衡量模型的预测准确性和拟合效果。

## 结果分析
![image](https://github.com/user-attachments/assets/fc532efa-5845-4065-89c3-5a009ef17056)
性能分析：

极高预测能力：R²=0.95表明模型捕捉了95%的价格变化模式
实际误差水平：RMSE=19.4说明：
多数预测值与实际价格偏差在±19.4以内
考虑到样本价格范围（0.24-440），该误差在合理范围内
泛化能力优秀：训练集（交叉验证）与测试集表现一致，无过拟合迹象

## 重要特征分析

![image](https://github.com/user-attachments/assets/b1ea2265-24de-4b0f-8478-44e6ca49e158)
特征重要性排序:
Package      0.647039
Variety      0.104937
City Name    0.084772
Item Size    0.080535
Origin       0.026763
Color        0.023415
Month        0.016510
Day          0.013300
Year         0.002728
Repack       0.000000
dtype: float64

从图片和数据可得知价格波动与package最明显，特征值达到0.647，与我们现实知识一致，尺寸大的南瓜重量也更大，这也就导致南瓜的单价更高。
