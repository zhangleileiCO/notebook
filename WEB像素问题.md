本文主要整理问题

- 移动端和PC端的物理像素和逻辑像素的区别
- 物理分辨率逻辑分辨率的区别

### 基本概率

##### 物理像素

手机或者电脑的实际像素，出厂时就已经决定。

##### PPI

屏幕像素密度

每英寸多少像素数，放到显示器上说的是每英寸多少物理像素及显示器设备的点距。

##### DPI

每英寸多少点

当用于描述显示器设备时ppi与dpi是同一个概念

计算：

##### dpr

设备像素比、屏幕像素比

和ppi的关系：一般是ppi/160的整数倍

##### 逻辑像素

和dpr的关系：物理像素=dpr*逻辑像素

|       设备类别       | 物理分辨率(pt) | **屏幕尺寸 (inch)** | DPI             | DPR         | 逻辑分辨率(px) |
| :------------------: | :------------: | :-----------------: | --------------- | ----------- | -------------- |
|  Android(代表机型1)  |    240x320     |                     | 120             | 0.75 、ldpi |                |
|  Android(代表机型2)  |    320x480     |                     | 160             | 1、mdpi     |                |
|  Android(代表机型3)  |    480x800     |                     | 240             | 1.5、hdpi   |                |
|  Android(代表机型4)  |    720x1280    |                     | 320             | 2、xhdpi    |                |
|  Android(代表机型5)  |   1080x1920    |                     | 480             | 3、xxhdpi   |                |
|     iPhone I3GS      |    320x480     |         3.5         | 164.825 、1*160 | @1x         | 320x480        |
|     iPhone 4(s)      |    640x960     |         3.5         | 2*160           | @2x         | 320x480        |
|    iPhone 5(s/se)    |    640x1136    |          4          | 2*160           | @2x         | 320x568        |
|   iPhone 6(s)/7/8    |    750x1334    |         4.7         | 2*160           | @2x         | 375x667        |
| iPhone 6(s)/7/8 Plus |   1242x2208    |         5.5         | 3*160           | @3x         | 414x736        |
|       iPhone X       |   1125x2436    |         5.8         | 3*160           | @3x         | 375x812        |
|          PC          |                |                     |                 |             |                |

### PC端适配

为设置系统缩放比例时，一般1物理像素就是1屏幕像素

适配方案：

PC浏览器适配一般是固定最小宽度min-width

### 移动端适配

px (pixel,像素)：是一个虚拟长度单位
如果px要换算成物理长度，需要指定精度DPI(Dots Per Inch，每英寸像素数)

我们下面讨论的是CSS pixels
同样是相对长度单位，相对的是设备像素(device pixel)

- 在同样一个设备上，每1个CSS像素所代表的物理像素是可以变化的(即CSS像素的第一方面的相对性);
- 在不同的设备之间，每1个CSS像素所代表的物理像素是可以变化的(即CSS像素的第二方面的相对性);

所以在谈论像素时一定要清楚它的上下文
- ppi(屏幕像素密度)：每英寸多少像素数，放到显示器上说的是每英寸多少物理像素及显示器设备的点距。
- dpi：每英寸多少点。
当用于描述显示器设备时ppi与dpi是同一个概念
计算方式：
var 斜边尺寸 = V(1920^2+1080^2) V代表开根号
var ppi = 斜边尺寸/5.5
ppi = 401ppi
dpr: 设备像素比与ppi相关，一般是ppi/160的整数倍
密度：ldpi 密度值：120 分辨率：240*320 dpr:12/16
mdpi
hdpi
xhdpi
xxhdpi

物理像素（DP、设备像素、分辨率）
概念：
- 如显示屏是由一个个物理像素点组成的，每个设备的物理像素点固定不变。单位是pt。1pt=1/72(inch)，1inch=2.54cm。
- 设备屏幕实际拥有的像素点。
  比如iPhone 6的屏幕在宽度方向有750个像素点，高度方向有1334个像素点，所以iPhone 6 总共有750*1334个物理像素。
  iphone6 的宽度是750/72*2.54(cm)


ppi=dpi
dpr=ppi/160
iphone6 plus比较特殊： 物理像素点是1080 * 1920,系统层面扩展为1242 * 2208，逻辑像素是414 * 736，dpr=3
设计图的大小一般是750*1134

手机上让像素等于分辨率像素
```javascript
var dpr = window.devicePixelRatio;
var meta = document.createElement('meta');
 meta.setAttribute('content', 'initial-scale=' + 1 / dpr + ', maximum-scale=' + 1 / dpr + ', minimum-scale=' + 1 / dpr + ', user-scalable=no');
 document.getElementsByTagName('head')[0].appendChild(meta);
```
让浏览器最多10rem
```javascript
 document.addEventListener('DOMContentLoaded', function (e) {
 document.getElementsByTagName('html')[0].style.fontSize = window.innerWidth / 10 + 'px';
 }, false);
```
计算需要的宽度

_假设元素宽度为 300px，则元素宽度：300/750*10（rem）_
自动转换方案
scss方案
```scss
$ue-width: 750; /* ue图的宽度 */
@function px2rem($px) {
 @return #{$px/$ue-width*10}rem;
}
p {
 width: px2rem(100);
}
```
postcss-pxtorem方案

