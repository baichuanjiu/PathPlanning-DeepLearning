# 目录

- [目录](#目录)
- [写在前面](#写在前面)
- [D-LinkNet](#d-linknet)
- [运行需知](#运行需知)
  - [环境](#环境)
  - [训练、测试](#训练测试)
  - [提取成便于部署的ONNX模型](#提取成便于部署的onnx模型)
- [数据集](#数据集)
  - [原数据集](#原数据集)
  - [迁移学习所用数据集](#迁移学习所用数据集)
- [预览](#预览)
  - [训练日志](#训练日志)
  - [道路识别](#道路识别)
- [训练好的模型](#训练好的模型)


# 写在前面

PathPlanning项目是某比赛的参赛项目。  
简而言之，该项目实现了一套通过深度学习模型将道路信息从卫星遥感图像中提取出来，再通过图像处理进一步标识出道路关键节点（道路起点、交叉点、终点）、提取出道路网结构，最后由用户选定必经点后，经过算法计算出一条可靠路径（尽可能短、且沿道路中线）的路径规划系统。  
  
项目划分为了三个模块：前端（Web、移动端）、后端、深度学习。
- 前端，负责用户交互与实现路径规划算法
- 后端，部署深度学习模型实现道路信息的提取与通过OpenCV完成道路网结构的提取
- 深度学习，负责训练从卫星遥感图像中提取道路信息的模型  

本仓库为PathPlanning项目深度学习方面的仓库，同时也是PathPlanning项目的主体仓库。若要查看PathPlanning项目下的其它代码仓库，可参阅该仓库目录：  
- [PathPlanning-PathPlanningWeb 网页端](https://github.com/baichuanjiu/PathPlanning-PathPlanningWeb)
- [PathPlanning-PathPlanningApp 移动端](https://github.com/baichuanjiu/PathPlanning-PathPlanningApp)
- [PathPlanning-PathPlanningServer 后端](https://github.com/baichuanjiu/PathPlanning-PathPlanningServer)


# D-LinkNet

用于提取道路信息的模型为D-LinkNet模型，由Lichen Zhou, Chuang Zhang, Ming Wu在CVPR 2018中提出，并凭借此模型一举斩获Road Extraction赛道第一的成绩，相关论文链接可[点击查看](https://openaccess.thecvf.com/content_cvpr_2018_workshops/w4/html/Zhou_D-LinkNet_LinkNet_With_CVPR_2018_paper.html)。  
  
本仓库下所用代码也经由上述团队提供的开源代码修改而成，[点击查看](https://github.com/zlckanata/DeepGlobe-Road-Extraction-Challenge)原代码仓库。

其中该仓库下Original（原始留档）文件夹与ControlGroup（对照组）文件夹下为经过修改的代码，主要修改部分为：
- 将Python 2语法改为Python 3语法
- 将Pytorch 0.2.0语法改为Pytorch 1.12.0语法

Transfer（迁移）文件夹下为用于迁移学习的代码，在ControlGroup的基础上，对模型在新数据集下进行迁移学习。  
Transfer_LoRA文件夹下为在ControlGroup的基础上，为原模型添加了LoRA层后在新数据集下进行迁移学习的代码。有关LoRA的相关信息，可查阅[该仓库](https://github.com/microsoft/LoRA)。

# 运行需知

## 环境

- python 3.10.9
- torch 1.12.0+cu116
- 其它库根据提示信息自己配置即可

## 训练、测试

1. 删去所有文件夹下的placeholder.txt（用于占位）。
2. 确保数据集已正确放入相应文件夹中（dataset/{相应文件夹}）。
3. 运行train.py或test.py。

## 提取成便于部署的ONNX模型

1. 从数据集中找一份数据放入dataset/forONNX文件夹下。
2. 打开test.py，进入编辑模式。
3. 将如下代码
```python
source = 'dataset/test/'
#source = 'dataset/valid/'
#source = 'dataset/forONNX/'
...
target = 'submits/log01_dink34/'
#target = 'submits/forONNX/'
```
修改为
```python
#source = 'dataset/test/'
#source = 'dataset/valid/'
source = 'dataset/forONNX/'
...
#target = 'submits/log01_dink34/'
target = 'submits/forONNX/'
```
3. 解除如下代码的注释
```python
# x = torch.randn(4,3,1024,1024,device='cuda')

# print('begin ONNX')

# with torch.no_grad():
#     torch.onnx.export(
#         solver.net.module,
#         x,
#         "dlinknet.onnx",
#         opset_version=15,
#         input_names=['input'],
#         output_names=['output'])
    
# print('output ONNX')
```
4. 运行test.py。

# 数据集

## 原数据集

原数据集为DeepGlobe Road Extraction Dataset公开数据集，你可以在DeepGlobe Road Extraction比赛官网中向官方申请使用该数据集或通过[kaggle](https://www.kaggle.com/datasets/balraj98/deepglobe-road-extraction-dataset)、[百度飞桨](https://aistudio.baidu.com/aistudio/datasetdetail/55682)获取。

## 迁移学习所用数据集

该数据集由我们自行采集数据并标注，自觉质量一般故不公开，若需要可联系我以获取。

# 预览

## 训练日志
在完成训练后，你会得到一份与下图相似的日志。  
![训练日志.png](https://github.com/baichuanjiu/ReadMeImages/blob/main/PathPlanning/%E8%AE%AD%E7%BB%83%E6%97%A5%E5%BF%97.png?raw=true)

## 道路识别
道路识别效果如下图所示。  
![道路识别（原图）.png](https://github.com/baichuanjiu/ReadMeImages/blob/main/PathPlanning/%E9%81%93%E8%B7%AF%E8%AF%86%E5%88%AB%EF%BC%88%E5%8E%9F%E5%9B%BE%EF%BC%89.png?raw=true)  
![道路识别（结果）.png](https://github.com/baichuanjiu/ReadMeImages/blob/main/PathPlanning/%E9%81%93%E8%B7%AF%E8%AF%86%E5%88%AB%EF%BC%88%E7%BB%93%E6%9E%9C%EF%BC%89.png?raw=true)

# 训练好的模型
由于训练好的D-LinkNet模型与提取出的ONNX模型文件过大（大于100M）无法上传至github，故请自行训练或联系我以获取。