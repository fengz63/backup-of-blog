---
title: codala使用教程
date: 2020-06-26 14:52:27
tags: codalab
---
## codalab使用教程
最近在做九峰医疗举办的胸部X光片异物检测比赛，其中用到了codalab提交实验结果，鉴于网上资源不多，此处简要介绍一下codalab提交结果的过程。
<!--more-->
## 1. 创建worksheet
首先点击右上角的*new worksheet*按钮创建一个新的worksheet。并起好名字
## 2. 权限设置
一般来说，worksheet创建好之后，默认的会有一个权限：you（all），public（read）。可以自己在此设置权限。第一种方式通过界面操作，直接点击对应位置设置；第二种为通过命令来设置
```shell
cl wperm . public r
```
其中wperm表示的是对worksheet设置权限，. 表示当前的worksheet，public为用户名字，r表示read。
## 3. copy验证集数据
对于codalab平台来收，首先需要把自己的代码在验证集上跑通，然后官方会按照验证集上的命令在测试集上测试自己的模型。copy数据的命令如下所示：
```shell
cl add bundle object-CXR-utlis//valid_image
cl add bundle object-CXR-utlis//image_path.csv
```
然后在自己的worksheet空间会出现两个对应名字的bundle，如下图所示：

{% qnimg codalab/fig1.jpg %}
## 4. 上传模型和测试代码
搞定上面之后我们需要上传测试模型和代码。可以在同一个文件夹下面，也可以在不同文件夹。由于codalab上传之后不能修改，而测试代码可能需要修改几次，因此建议模型单独上传，代码放在一个文件夹，这样修改代码之后不需要再上传模型。页面最左上角有一个*Upload*按钮，直接点击后可以选择上传文件或者文件夹。上传完毕之后再worksheet空间中就能看到模型和代码了。
注：此平台的代码运行从命令行获取参数，具体命令如下所示
```python
python src/<path-to-prediction-program> <input-data-csv-filename> <output-classification-prediction-csv-path> <output-localization-prediction-csv-path>
```
其中input-data-csv-file即为上面所述的验证集数据的信息，其中包含文件名和标注信息。最后两个参数为程序结果的输出文件
## 5. 运行程序
现在我们可以在验证集上运行程序了，具体的命令如下所示
```shell
cl run image_path.csv:image_path.csv valid_image:valid_image src:src "python src/<path-to-prediction-program> image_path.csv predictions_classification.csv predictions_localization.csv" -n run-predictions
```
其中codalab需要使用到docker环境。采用默认docker环境则不需要额外添加信息。如果需要使用单独的docker环境，则在上述命令后面加上--request-docker docker name命令即可。若要使用gpu则加入--request-gpus num_gpus命令。

运行结果之后后面会出现一个名为run-predictions的bundle，此bundle里面包含结果和输出信息等。

{% qnimg codalab/fig2.jpg %}

## 6. 提出结果
接下来将预测出来的结果文件单独提取到一个bundle中，命令如下所示(其中MODELNAME为自己模型的名字)：
```shell
cl make run-predictions/predictions_classification.csv  -n predictions-classification-{MODELNAME}
cl make run-predictions/predictions_localization.csv -n predictions-localization-{MODELNAME}
```
然后可以看到两个生成的bundle文件夹

{% qnimg codalab/fig3.jpg %}

## 7. 用验证集计算指标
直接按照官方教程来，命令如下所示：
```shell
cl add bundle object-CXR-utlis//valid_gt.csv
cl add bundle object-CXR-utlis//program
cl run valid_gt.csv:valid_gt.csv run-predictions:run-predictions program:program "python3 program/evaluate_auc.py run-predictions/predictions_classification.csv valid_gt.csv score.txt" -n score_auc --request-docker-image yww211/codalab:foreginobjv2
cl run valid_gt.csv:valid_gt.csv run-predictions:run-predictions program:program "python3 program/evaluate_froc.py run-predictions/predictions_localization.csv valid_gt.csv score.txt" -n score_froc --request-docker-image yww211/codalab:foreginobjv2
```
## 8. 提交
在提交之前，我们将为您的预测提供描述。首先，单击predictions-{MODELNAME}   bundle包，然后修改右侧栏中的“描述”字段。此捆绑包的说明可标识您在排行榜上的提交：请使用以下惯例进行捆绑说明：
```shell
{Name_of_model} (single model / ensemble) (Institution) optional_http_link
```
最后，我们将添加object-CXR-submit标记以将捆绑包标记为可提交。让我们运行以下命令：
```shell
cl edit predictions-classification-{MODELNAME} --tags object-CXR-submit
cl edit predictions-localization-{MODELNAME} --tags object-CXR-submit
```

上述都运行好之后即表明一切都已设置好，等待测试集上的结果就可以了。

### 注：权限问题
提交的时候可能需要设置一些bundle的权限，命令类似于设置上面的worksheet权限，如下(uuid即为budle包的唯一标识符)：
```shell
cl perm uuid public r(read)
```