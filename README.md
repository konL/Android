# Android

## 猜歌游戏实战

- 页面布局

**得到界面最终图，拆分，思考用什么布局**

![image](https://github.com/konL/Android/blob/master/screenshot/1.png)

**首先用布局特性拆分，可以看出整体游戏布局是垂直的`线性布局`，从上至下计分顶部，唱片动画，打字游戏部分；比较特殊的有`左右排布`的金币管理部分；并且游戏全屏显示。**

![image](https://github.com/konL/Android/blob/master/screenshot/2.png)
![image](https://github.com/konL/Android/blob/master/screenshot/3.png)
![image](https://github.com/konL/Android/blob/master/screenshot/4.png)
![image](https://github.com/konL/Android/blob/master/screenshot/5.png)

### 全屏显示
```
android:theme="@android:style/Theme.NoTitleBar.Fullscreen">
```
[注意]在MainActivity中要改成extends Activity(仍然不知道为什么）来源图片改成src

### title_bar分析

![image](https://github.com/konL/Android/blob/master/screenshot/2.png)

**背景,左半部分，右半部份，用LinearLayout包裹，右半部份有图片文字按钮湖湘叠加，用framelayout包裹（初始全部物件叠加在左上角）**

- 要实现的几个效果

- [x] 左右均为按钮(ImageButton),title_bar背景
```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    ........
    android:background="@mipmap/index_bar">
    ```

- [x] 按下有不同效果(新建xml文件）
```
android:background="@drawable/back_button_icon"
```
back_button_icon.xml(用selector）
```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true" android:drawable="@mipmap/all_back_sel"/>
    <item android:drawable="@mipmap/all_back" />
</selector>
```

按下效果不同的例子同理

- [x] 左右按钮垂直居中，右边按钮水平分布
```
        android:layout_centerVertical="true"
        android:layout_alignParentEnd="true"//与父元素同一个位置结束
        //各种居中都用
        layout_gravity="XXXX"实现
        android:layout_gravity="center_vertical|right"(右居中）
```
     
- [x] 布局到主Activity

```
<include layout="@layout/top_bar"/>
```

### 唱片动画（布局+动画应用）

![image](https://github.com/konL/Android/blob/master/screenshot/3.png)

- [x] 布局

**分析知为上下布局，上面是板子+文字叠加，下面是唱盘唱针按钮等等图片叠加，上下分别用FrameLayout包裹，大布局用LinearLayout**

```
//唱片相关动画
    private Animation mPanAnim;
    private LinearInterpolator mPanLin;//控制速度
    //唱针
    private Animation mBarInAnim;
    private LinearInterpolator mBarInLin;
    private Animation mBarOutAnim;
    private LinearInterpolator mBarOutLin;
    //按键play
    private ImageButton mBtnPlayStart;
    private boolean mIsRunning;
    //绑定
    private ImageView mViewPan;
    private ImageView mViewPanBar;
    ```
    xml动画
   - 新建anim文件夹，新建xml文件(加入Rotate属性）
   ```
   <?xmlns version="1.0" encoding="utf-8" ?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <rotate
        android:duration="2400"
        android:fromDegrees="0"
        android:pivotX="50%"
        android:pivotY="50%"
        android:repeatCount="3"
        android:toDegrees="359"/>
</set>
```
 
 >【Animation方法】
 -  Animation mPanAnim= AnimationUtils.loadAnimation(this,R.anim.rotate);应用xml动画到对象
 - Object.startAnimation(animation anim)绑定动画
 【LinearInterpolator】
 - Interpolator 被用来修饰动画效果，定义动画的变化率，可以使存在的动画效果accelerated(加速)，decelerated(减速),repeated(重复),bounced(弹跳)等。
 
 - 初始化动画（初始化应该在onCreate里)【同理对所有需要动画的实例做一次】
 ```
      //创建图片绑定对象
        mViewPan=(ImageView)findViewById(R.id.imageView1);
        mViewPanBar=(ImageView)findViewById(R.id.imageView2);
        
        mPanAnim= AnimationUtils.loadAnimation(this,R.anim.rotate);
       //创建控制速度的对象
        mPanLin= new LinearInterpolator() 
       //给动画对象传入速度对象
        mPanAnim.setInterpolator(mPanLin);
```

**动画效果分析：点击按钮，按钮消失（setVisibility),唱针完成第一部分动画定住（对动画对象添加setFillAfter(true)），然后唱片开始转，然后唱针归位，按钮出现**

动画前后效果应该使用监听事件实现
```
.setAnimationListener(new Animation.AnimationListener() {

public void onAnimationStart(Animation animation) {

public void onAnimationEnd(Animation animation) {
             
public void onAnimationRepeat(Animation animation) {
```

**对按钮的设置**
```
 mBtnPlayStart=(ImageButton)findViewById(R.id.btn_play_start);
        mBtnPlayStart.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                handlePlayButton();
            }
        });
        //初始化游戏
        initCurrentStageData();
    }
    private void handlePlayButton(){
        if(mViewPanBar !=null){
        if(!mIsRunning){
            mIsRunning=true;
        mViewPanBar.startAnimation(mBarInAnim);
        mBtnPlayStart.setVisibility(View.INVISIBLE);}
    }
    }
 ```
          

