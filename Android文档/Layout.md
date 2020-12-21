##### 安卓样式布局

七大布局分别为：线性布局（LinearLayout）、相对布局（RelativeLayout）、帧布局(FrameLayout)、表格布局(TableLayout)、绝对布局(absoluteLayout)、网格布局(GridLayout)、约束布局(ConstrainLayout)

#### **1.线性布局（LInearLayout）**

主要属性：

1.1 orientation设置布局管理器内组件的排列方式，可以设置为horizontal（横向）、vertical（纵向）两个值之一

1.2 gravity设置布局管理器内组件的对齐方式，layout_gravity控制自己在父元素的位置。

![img](https:////upload-images.jianshu.io/upload_images/13690186-2e2bb18a3a42fce3.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/608/format/webp)

1.3 layout_weight设置权重，推荐layout_width="0dp"或layout_height="0dp"。

#### **2.相对布局（RelativeLayout）** 

主要属性：

![img](https:////upload-images.jianshu.io/upload_images/13690186-ec2130be2e44c4aa.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/691/format/webp)

#### **3.帧布局（FrameLayout）**

继承自ViewGroup组件，很少使用这个布局，主要可以使布局叠加。FrameLayout为每个加入其中的组件创建一个空白的区域(称为一帧)，每个子组件占据一帧，这些帧会根据gravity属性执行自动对齐。

#### **4.表格布局（TableLayout）**

TableLayout包裹TableRow（行数），

TableRow包裹view（列数）。

shrinkColumns属性：当TableRow里边的空间布满布局的时候，指定列自动延伸以填充可用部分。当TableRow里边的控件还没有布满布局时，不起作用。 

tretchColumns属性：设置可伸展的列。该列可以向行方向伸展，最多可占据一整行。

collapseColumns属性：设置要隐藏的列。

#### **5.绝对布局（absoluteLayout）** 

绝对布局是需要指定子元素的 xy 精确坐标的布局。绝对布局缺乏灵活性，在没有绝对定位的情况下相比其他类型的布局更难维护，不建议使用。

#### **6.网格布局（GridLayout）**

Android4.0（API Level 14）新引入的网格矩阵形式的布局控件。

使用的时候需要注意兼容：

compile 'com.android.support:gridlayout-v7:22.+'

#### **7.约束布局（ConstraintLayout）**

Android Studio 2.2推出的新布局，并从Android Studio 2.3开始成为默认布局。

它的出现是为了解决复杂布局时，布局嵌套过多的问题（嵌套布局会增加绘制界面所需的时间）

相关属性

![img](https:////upload-images.jianshu.io/upload_images/13690186-3a4f3f7db4f363ab.png?imageMogr2/auto-orient/strip|imageView2/2/w/719/format/webp)





