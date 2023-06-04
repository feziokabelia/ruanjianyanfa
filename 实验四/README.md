## 基于TensorFlow Lite实现的Android花卉识别应用
### 实验内容
本教程将在Android设备上使用TensorFlow Lite运行图像识别模型，具体包括：

1. 按照教程构建基于TensorFlow Lite的Android花卉识别应用。
2. 查看该应用的代码框架，特别注意CameraX 库(AndroidX.camera.*)和数据视图模型的使用。
3. 上传完成既定功能的代码至Github，并撰写详细的Readme文档。
### 预备工作  
创建工作目录，使用
```html
git clone https://github.com/hoitab/TFLClassify.git
```
![](/实验四/pic/屏幕截图%202023-05-16%20222733.png)  
### 运行初始代码  
1. 打开Android Studio，选择“Open an Existing Project”  
![](/实验四/pic/屏幕截图%202023-05-16%20222524.png)  
2. 选择TFLClassify/build.gradle生成整个项目。项目包含两个module：finish 和 start，finish模块是已经完成的项目，start则是本项目实践的模块  
3. 第一次编译项目时，将下载相应的gradle wrapper 。  
![](/实验四/pic/屏幕截图%202023-05-16%20223205.png)  
4. 项目所使用的Android Gradle plugin版本太低，需要升级。  
![](/实验四/pic/屏幕截图%202023-05-17%20012531.png)  
5. 手机通过USB接口连接开发平台，并设置手机开发者选项允许调试。
6. 选择真实物理机（而不是模拟器）运行start模块  
![](/实验四/pic/屏幕截图%202023-05-17%20015939.png)  
7. 允许应用获取手机摄像头的权限，得到下述效果图，界面利用随机数表示虚拟的识别结果。  
8. 解决了一系列问题后，得到运行结果：  
 ![](/实验四/pic/Screenshot_2023-05-17-14-35-47-014_org.tensorflow.jpg)   
 ### 向应用中添加TensorFlow Lite
 1. 右键“start”模块，或者选择File，然后New>Other>TensorFlow Lite Model  
 ![](/实验四/pic/屏幕截图%202023-05-17%20015939.png)   
 2. 选择已经下载的自定义的训练模型。本教程模型训练任务以后完成，这里选择finish模块中ml文件下的FlowerModel.tflite  
 ![](/实验四/pic/屏幕截图%202023-05-17%20145403.png)  
### 检查代码中的TODO项  
本项目初始代码中包括了若干的TODO项，以导航项目中未完成之处。  
 ![](/实验四/pic/屏幕截图%202023-05-17%20162338.png)  
### 添加代码重新运行APP  
1. 定位“start”模块MainActivity.kt文件的TODO 1，添加初始化训练模型的代码  
```java
private class ImageAnalyzer(ctx: Context, private val listener: RecognitionListener) :
        ImageAnalysis.Analyzer {

  ...
  // TODO 1: Add class variable TensorFlow Lite Model
  private val flowerModel = FlowerModel.newInstance(ctx)

  ...
}
```
2. 在CameraX的analyze方法内部，需要将摄像头的输入ImageProxy转化为Bitmap对象，并进一步转化为TensorImage 对象  
```java
override fun analyze(imageProxy: ImageProxy) {
  ...
  // TODO 2: Convert Image to Bitmap then to TensorImage
  val tfImage = TensorImage.fromBitmap(toBitmap(imageProxy))
  ...
}
```
3. 对图像进行处理并生成结果，主要包含下述操作：  
  - 按照属性score对识别结果按照概率从高到低排序
  - 列出最高k种可能的结果，k的结果由常量MAX_RESULT_DISPLAY定义
```java
override fun analyze(imageProxy: ImageProxy) {
...
// TODO 3: Process the image using the trained model, sort and pick out the top results
val outputs = flowerModel.process(tfImage)
    .probabilityAsCategoryList.apply {
        sortByDescending { it.score } // Sort with highest confidence first
    }.take(MAX_RESULT_DISPLAY) // take the top results

...
}
```
4. 将识别的结果加入数据对象Recognition 中，包含label和score两个元素。后续将用于RecyclerView的数据显示  
```java 
override fun analyze(imageProxy: ImageProxy) {
  ...
  // TODO 4: Converting the top probability items into a list of recognitions
  for (output in outputs) {
      items.add(Recognition(output.label, output.score))
  }
  ...
}
```
5. 将原先用于虚拟显示识别结果的代码注释掉或者删除
```java

            // START - Placeholder code at the start of the codelab. Comment this block of code out.
//            for (i in 0 until MAX_RESULT_DISPLAY){
//                items.add(Recognition("Fake label $i", Random.nextFloat()))
//            }
```
6. 以物理设备重新运行start模块  
### 运行效果  
![](/实验四/pic/屏幕截图%202023-05-17%20162338.png)  






   
