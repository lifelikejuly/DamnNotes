## 灰度化
灰度图像与黑白图像不同，在计算机图像领域中黑白图像只有黑白两种颜色，灰度图像在黑色与白色之间还有许多级的颜色深度。
* 浮点算法：Gray=R*0.3+G*0.59+B*0.11
* 整数方法：Gray=(R*30+G*59+B*11)/100
* 移位方法：Gray =(R*77+G*151+B*28)>>8;
* 平均值法：Gray=（R+G+B）/3;
### 实现代码
#### 方法1
```
                int width = bm.getWidth();//原图像宽度
                int height = bm.getHeight();//原图高度
                int color;//用来存储某个像素点的颜色值
                int r, g, b, a;//红，绿，蓝，透明度
                int gray;//用来存储计算得到的灰度值
                int lowDigit = 0;//用于存储用户在对话框中输入的下限值
                int highDigit = 255;//用于存储用户在对话框中输入的上限值
                //创建空白图像，宽度等于原图宽度，高度等于原图高度，用ARGB_8888渲染，这个不用了解，这样写就行了
                Bitmap bmp = Bitmap.createBitmap(width, height
                        , Bitmap.Config.ARGB_8888);
 
                int[] oldPx = new int[width * height];//用来存储原图每个像素点的颜色信息
                int[] newPx = new int[width * height];//用来处理处理之后的每个像素点的颜色信息
                /**
                 * 第一个参数oldPix[]:用来接收（存储）bm这个图像中像素点颜色信息的数组//The array to receive the bitmap’s colors
                 * 第二个参数offset:oldPix[]数组中第一个接收颜色信息的下标值// The first index to write into pixels[]
                 * 第三个参数width:在行之间跳过像素的条目数，必须大于等于图像每行的像素数//The number of entries in pixels[] to skip between rows (must be >= bitmap’s width). Can be negative.
                 * 第四个参数x:从图像bm中读取的第一个像素的横坐标 The x coordinate of the first pixel to read from the bitmap
                 * 第五个参数y:从图像bm中读取的第一个像素的纵坐标The y coordinate of the first pixel to read from the bitmap
                 * 第六个参数width:每行需要读取的像素个数The number of pixels to read from each row
                 * 第七个参数height:需要读取的行总数The number of rows to read
                 */
                bm.getPixels(oldPx, 0, width, 0, 0, width, height);
                for (int j = 0; j < width * height; j++) {//循环处理图像中每个像素点的颜色值
                    color = oldPx[j];//取得某个点的像素值
                    r = Color.red(color);//取得此像素点的r(红色)分量
                    g = Color.green(color);//取得此像素点的g(绿色)分量
                    b = Color.blue(color);//取得此像素点的b(蓝色分量)
                    a = Color.alpha(color);//取得此像素点的a通道值
                    //此公式将r,g,b运算获得灰度值，经验公式不需要理解
                    gray = (int)((float)r*0.3+(float)g*0.59+(float)b*0.11);
 
                    if(gray < lowDigit) {//如果某点灰度值小于下限值
                        gray = 0;//将此点灰度值置为0
                    } else if(gray > highDigit){//如果某点灰度值大于上限值
                        gray = 255;//将此点灰度值置为255
                    }
                    //如果某点灰度值位于上限值与下限值之间则不改变
                    newPx[j] = Color.argb(a,gray,gray,gray);//将处理后的透明度（没变），r,g,b分量重新合成颜色值并将其存储在数组中
                }

```

#### 方法2
```
public Bitmap lineGrey(Bitmap image) {
  //得到图像的宽度和长度 
  int width = image.getWidth(); 
  int height = image.getHeight(); 
  //创建线性拉升灰度图像 
  Bitmap linegray = null; 
  linegray = image.copy(Config.ARGB_8888, true); 
  //依次循环对图像的像素进行处理 
  for (int i = 0; i < width; i++) { 
    for (int j = 0; j < height; j++) { 
      //得到每点的像素值 
      int col = image.getPixel(i, j); 
      int alpha = col & 0xFF000000; 
      int red = (col & 0x00FF0000) >> 16; 
      int green = (col & 0x0000FF00) >> 8; 
      int blue = (col & 0x000000FF); 
      // 增加了图像的亮度 
      red = (int) (1.1 * red + 30); 
      green = (int) (1.1 * green + 30); 
      blue = (int) (1.1 * blue + 30); 
      //对图像像素越界进行处理 
      if (red >= 255)  
      { 
        red = 255; 
      } 
 
      if (green >= 255) { 
        green = 255; 
      } 
 
      if (blue >= 255) { 
        blue = 255; 
      } 
      // 新的ARGB 
      int newColor = alpha | (red << 16) | (green << 8) | blue; 
      //设置新图像的RGB值 
      linegray.setPixel(i, j, newColor); 
    } 
  } 
  return linegray; 
} 
```

## 二值化
二值化（英语：Thresholding）是图像分割的一种最简单的方法。二值化可以把灰度图像转换成二值图像。把大于某个临界灰度值的像素灰度设为灰度极大值，把小于这个值的像素灰度设为灰度极小值，从而实现二值化，简单的说就是非黑即白。
```
public Bitmap gray2Binary(Bitmap graymap) { 
  //得到图形的宽度和长度 
  int width = graymap.getWidth(); 
  int height = graymap.getHeight(); 
  //创建二值化图像 
  Bitmap binarymap = null; 
  binarymap = graymap.copy(Config.ARGB_8888, true); 
  //依次循环，对图像的像素进行处理 
  for (int i = 0; i < width; i++) { 
    for (int j = 0; j < height; j++) { 
      //得到当前像素的值 
      int col = binarymap.getPixel(i, j); 
      //得到alpha通道的值 
      int alpha = col & 0xFF000000; 
      //得到图像的像素RGB的值 
      int red = (col & 0x00FF0000) >> 16; 
      int green = (col & 0x0000FF00) >> 8; 
      int blue = (col & 0x000000FF); 
      // 用公式X = 0.3×R+0.59×G+0.11×B计算出X代替原来的RGB 
      int gray = (int) ((float) red * 0.3 + (float) green * 0.59 + (float) blue * 0.11); 
      //对图像进行二值化处理 
      if (gray <= 95) { 
        gray = 0; 
      } else { 
        gray = 255; 
      } 
      // 新的ARGB 
      int newColor = alpha | (gray << 16) | (gray << 8) | gray; 
      //设置新图像的当前像素值 
      binarymap.setPixel(i, j, newColor); 
    } 
  } 
  return binarymap; 
}
```

## LSB
LSB 的全称是 Least Significant Bits，即通过图形中最不重要的一些信息位来储存我们隐藏信息（水印）。LSB 方法是最简单的嵌入水印的方法，事实上，任何一幅图片都具备一定的容噪性，这表现在像素数据的最低有效位 (Least Significant Bit，LSB) 对人眼的视觉影响很小，秘密信息就隐藏在图像每一个像素的最低位或次低位，实现其不可见性。
