# CSS的两种盒模型

> （1）有两种， IE 盒子模型、W3C 盒子模型；
>  （2）盒模型： 内容(content)、填充(padding)、边界(margin)、 边框(border)；
>  （3）区  别： IE的content部分把 border 和 padding计算了进去;

> margin(外边距) - 清除边框外的区域，外边距是透明的。
>  border(边框) - 围绕在内边距和内容外的边框。
>  padding(内边距) - 清除内容周围的区域，内边距是透明的。
>  content(内容) - 盒子的内容，显示文本和图像。

# 1. W3C的标准盒模型

```
在标准的盒子模型中，width指content部分的宽度
```

![img](https:////upload-images.jianshu.io/upload_images/11116807-7e1ae3a538b54ab9.png?imageMogr2/auto-orient/strip|imageView2/2/w/728/format/webp)



# 2. IE的盒模型

```
在IE盒子模型中，width表示content+padding+border这三个部分的宽度
```

![img](https:////upload-images.jianshu.io/upload_images/11116807-69203b8a808b5f1b.png?imageMogr2/auto-orient/strip|imageView2/2/w/791/format/webp)



# 3. box-sizing的使用

如果想要切换盒模型也很简单，这里需要借助css3的box-sizing属性

> box-sizing: content-box 是W3C盒子模型
>  box-sizing: border-box 是IE盒子模型

box-sizing的默认属性是content-box

