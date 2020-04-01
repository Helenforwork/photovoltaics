# 光伏发电功率预测
## 1、任务：
通过学习历史一段时间内的数值天气预测数据和对应的光伏发电功率训练模型，结合未来某时间点的数值天气预测数据，预测该时间点的光伏发电功率。
### 2、数据：
已上传至Data文件夹中，包括10个电站的历史数据，分为训练集与测试集，其具体数据描述参考：[数据集描述](https://www.dcjingsai.com/common/cmpt/%E5%9B%BD%E8%83%BD%E6%97%A5%E6%96%B0%E7%AC%AC%E4%BA%8C%E5%B1%8A%E5%85%89%E4%BC%8F%E5%8A%9F%E7%8E%87%E9%A2%84%E6%B5%8B%E8%B5%9B_%E7%AB%9E%E8%B5%9B%E4%BF%A1%E6%81%AF.html)
### 3、备注：
因2020年3月20日起，DC竞赛平台评分未配置计算资源无法将进行评分。__因此项目中止，未获得有有效名次。__
### 4、文件描述：
#### 0: 外部运行数据处理、建模和格式化输出的程序；  
#### 1：数据探索；  
#### 2：利用R语言中OCE函数包计算太阳辐照特征；
#### 3：数据处理；
#### 4：模型建立与结果输出。
### 5、运行方法：
#### [1] 单独运行文件2；  
#### [2] 使用文件0在外部依次运行文件3和4；  
#### [3] 使用文件0进行格式化输出。  
### 6、算法描述：
#### [1]	评分方法并不计入低于发电功率阈值的数据点。根据辐照度与发电功率阈值的关系，可以建立两类模型：包含辐照度为-1和不包括辐照度为-1的数据集。并将预测中所有低于阈值结果调整至阈值，以图减小误差；  
#### [2]	依据辐射度变化规律，推测日出时间，进而将十个电厂的地理位置归为以下四处：海西、哈密、西宁、乌鲁木齐。利用R语言包OCE，将数据集中的时间、地理位置输入，得到太阳高度、赤纬角、预测辐照度等太阳功率的特征，加入到数据集中；  
#### [3]	光伏发电功率是一个存在日周期性和季度特征变化的变量，可以将其视为时间序列处理。利用Facebook开发的fbprophet时间序列工具对训练集中的时间和功率进行拟合，构造出功率的预测值、预测值上限、预测值下限三个特征，加入到数据集中；  
#### [4]	鉴于数据集中发电功率存在大量不可有气象数据解释的功率异常点，本算法结合实际辐照度和功率之间的应呈线性正比的特殊关系，进行了数据筛选。但此过程中发现实际辐照度已存在众多异常点，因此建立每月固定时间点的功率应符合正态分布的假设，对数据进行了[5%,95%]分位的筛选，异常的数据可以由中位数填充。  
#### [5]	构造了前一时间点、与月平均值差值、季度等新特征，并应用sklearn中的多项式特征、标准化、PCA等函数进行了数据处理；  
#### [6]	在建模方面，采用XgBoost、Lasso、ElasticNet、SVR、DNN、LSTM建立了单独的模型，并集合Grid_Search进行了初步的参数调优。因数据集中存在较多的缺失时间段，故放弃LSTM模型。XgBoost、SVR和DNN的训练时间较长， 线性模型的Lasso和ElasticNet训练较快。  
#### [7]	在训练集拟合的模型评分与平台评估的模型评分具有较大差异（5%以上）。推测其原因：一部分是树模型Xgboost对离群点较为敏感；测试集中亦有可能存在较多无法预测的异常点数据，给预测结果带来了较大偏差；部分数据集（train_10）等电站数据存在大量不可用现有数据解释的样本，模型偏差较大。  
