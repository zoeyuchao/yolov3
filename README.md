# yolov3训练代码

这是yolov3的训练代码，需要用到GPU，所以在使用这个代码之前，需要检查自己电脑的显卡驱动和cuda是不是装好了，检查方法是在终端输入：

```
nvidia-smi
```

如果有驱动版本显示和cuda版本显示，那么一切正常，可以向下进行了。

## 1.安装

```
conda create -n yolov3 python=3.7
conda activate yolov3

pip install opencv-python numpy tqdm matplotlib
# https://pytorch.org/get-started/locally/ 从这个网站上查一查自己的cuda对应版本
conda install pytorch torchvision cudatoolkit=10.1 -c pytorch

git clone https://github.com/zoeyuchao/yolov3.git
cd weights
bash ./download_yolov3_weights.sh
```

## 2.训练

```
python train.py --data data/ball.data --cfg cfg/yolov3.cfg --epochs 100 --batch-size 8
```

- 如果出现out of memory，修改batch-size的大小。

- 如果出现cv2找不到的情况，在python文件里加上

  ```
  sys.path.remove('/opt/ros/kinetic/lib/python2.7/dist-packages')
  ```

- 假如你要训练自己的模型，需要修改以下内容：

  - 网络结构：**yolo**层的class种类和yolo层**之前**的filter大小，class=x，每一个网格预测3个anchor结果，所以filter =(3 *(x + 5)),注意三层yolo都要修改。
  
  - 在data目录下新建ball.data，配置训练的数据，内容如下:
  
    ```
    classes= 4
    train = data/train.txt
    valid = data/test.txt
    names = data/ball.names
    backup = backup/
    eval = coco
    ```
  
    新建ball.names,内容如下：
  
    ```
    basketball
    football
    volleyball
    balloon
    ```
  
  - 准备数据集：
  
    - 将数据集Annotations、JPEGImages拷贝至data文件夹下；
  
    - 新建3个文件夹，分别命名为ImageSets、labels、images，将JPEGImages中的图片全部复制到images文件夹中；
  
    - 运行根目录下makeTxt.py，得到ImageSets存放的train与test文件；
    - 运行根目录下voc_label.py，得到labels的具体内容以及data目录下的train.txt，test.txt，val.txt，这里的train.txt与之前的区别在于不仅仅得到文件名还有文件的具体路径。

## 3.评估

```
python test.py  --data data/ball.data --cfg cfg/yolov3.cfg  --weights weights/best.pt
```

在 yolov3的目录下会生成一张test_batch0.jpg。

## 4.可视化

在训练阶段会在yolov3目录下生成一个result.txt文件，可以运行这句话：

```
python -c "from utils import utils; utils.plot_results()"
```

会在yolov3目录下生成一张result.png。

## 5.测试

在data路径下建一个samples的文件夹，将测试图片放入samples文件夹中，然后执行：

```
python detect.py --data data/ball.data --cfg cfg/yolov3.cfg --weights weights/best.pt  
```

ball.pt下载链接：https://pan.baidu.com/s/1YBy26Mx4IOmGjHOIClOxRA  提取码：qth5 

fire.pt下载链接：链接：https://pan.baidu.com/s/1vnxqklnuik1iXrjurP3ZoQ 提取码：0kod 

