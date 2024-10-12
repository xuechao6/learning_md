# c++应用

主要来记录实际应用时，总结的解决方式

## 1.ENU和NED坐标系转化：

![image-20240314205430024](picture/image-20240314205430024.png)

![image-20240314205453733](/home/bit/xc_github/picture/image-20240314205453733.png)





## 2.matplotlib学习

1.想要显示出随时间变化的效果，轨迹不断在生长

思路：不断的在容器中添加新的数据，然后清除之前的画布，再次plot，然后延时

```c++
for(int i=1; i<n; i++) {
    x.push_back(i);
    y.push_back(sin(2*M_PI*i/360.0));
    z.push_back(log(i));
    //不断往容器中压数据，才能产生这个一直走的效果
    if (i % 10 == 0) {
        plt::clf(); //清空之前的绘图，
        //两种绘图方式
        //方式一：
        plt::plot(x, y);
        //方式二：附带图例
        plt::named_plot("log(x)", x, y);
        plt::xlim(0, n*n);          //设置x-axis的取值范围
        plt::title("Sample figure");//添加标题
        plt::legend();              //使得可以标注
        plt::axis("equal") //坐标轴等比例
        //最重要的一步：延时，只有加上延时，才能在每一次循环结束后输出
        plt::pause(0.01);
    }
}
```

2.plot添加图例

```c++
plt::plot(x,y,{{"label","y=x+4"}}); //在变量后面加入一个map的消息，key为"label"，value为自定义的字符串。有两个花括号
plt::legend(); //加上这个legend后才能显示图例
```



## 3.枚举类实现switch

```c++
enum day = {mon , the , wed , thur , fri}
day today = mon;
switch(today)
{
    case mon:
        condition;
        break;
    case the:
        condition;
        break;
    case wed:
        consition;
        break;
    default:
        break;
}
```



## 4.调用M_PI

```c++
#define _USE_MATH_DEFINES
include <cmath>
```



## 5.点的旋转

平面坐标系中的任何一个点A（x1，y1），连接该点和原点组成一条向量，将这个向量按照a的旋转角度（逆时针旋转为正，顺时针旋转为负）旋转，旋转后的点B（x2，y2），二者之间的关系：

`[x2, y2]T = R* [x1, y1]T `，其中R是旋转矩阵，`R = [cos(a) , -sin(a) ; sin(a) , cos(a) ]` 顺逆时针只影响a的正负，不影响表达式

```c++
Cartesian WayPoint::rotate(Cartesian p1, double angle_rad)
{
    Cartesian p2;
    p2.x = cos(angle_rad)* p1.x - sin(angle_rad)* p1.y;
    p2.y = sin(angle_rad)* p1.x + cos(angle_rad)* p1.y;
    return p2;
}
```

推导过程可以考虑直线的极坐标表示



## 6.矩形的坐标变化

思路：先计算最好计算的情况（矩形中心店和原点重合，边长和坐标轴平行），然后进行旋转+平移，得到四个点的坐标

虚拟矩形中心定在坐标原点，长平行x轴，宽平行y轴

真实矩形中心坐标A为（x1 , y1），长边和点A与原点连线组成的向量垂直

- 计算中心点A的和x正半轴的夹角a，0-2π范围，均为正值
- 输入四个顶点坐标，输入问题5中的旋转角度为**`（a-π/2）`**，严格推导过，四个象限都一样
- 在四个顶点坐标x和y的基础上增加A的横纵坐标

```c++
//旋转矩阵，输入的旋转角度angle_rad为正时，绕着原点逆时针旋转，为负数时，顺时针旋转
Cartesian rotate(Cartesian p1, double angle_rad)
{
    Cartesian p2;
    p2.x = cos(angle_rad)* p1.x - sin(angle_rad)* p1.y;
    p2.y = sin(angle_rad)* p1.x + cos(angle_rad)* p1.y;
    return p2;
}
```



```c++
double angle_rad = atan2(centor.y,centor.x);
if(angle_rad < 0){
angle_rad += 2* M_PI;
}
left_down_rotate = rotate(left_down, angle_rad-M_PI/2);
right_down_rotate = rotate(right_down, angle_rad-M_PI/2);
right_up_rotate = rotate(right_up, angle_rad-M_PI/2);
left_up_rotate = rotate(left_up, angle_rad-M_PI/2);

left_down_trans = transform(left_down_rotate, centor.x, centor.y);
right_down_trans = transform(right_down_rotate, centor.x, centor.y);
right_up_trans = transform(right_up_rotate, centor.x, centor.y);
left_up_trans = transform(left_up_rotate, centor.x, centor.y);
```



# Eigen库使用

## 1.向量

定义：VectorXd，类似c++里面的容器

## 2.矩阵

定义：matrixXd，可以动态扩展

**构造函数**：

```c++
Eigen::MatrixXd matrix; //空的动态矩阵

Eigen::MatrixXd matrix(rows, cols); //默认都是0
Eigen::MatrixXd matrix(rows, cols).setConstant(num); //常数num填满

Eigen::MatrixXd matrix = Eigen::MatrixXd::Identity(rows, cols); //单位矩阵
```

**函数**：

```c++
.setIdentity() //单位化
.rows() //总行
.cols() //总列
.Zero() //0矩阵
.block(i,j,row,col) = (MatrixXd)... //从i,j的位置开始替换,替换多少行(row),替换多少列(rol)，注意右边必须是一个矩阵，VectorXd不可以充当等式右值
```

