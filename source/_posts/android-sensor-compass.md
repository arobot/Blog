---
title: Android 传感器学习（一）——指南针的实现
tags: [Android,sensor,compass,传感器,指南针]
categories: [Android]
date: 2016-12-23 09:53:48
---

>Always make sure to disable sensors you don't need, especially when your activity is paused. Failing to do so can drain the battery in just a few hours. Note that the system will not disable sensors automatically when the screen turns off.
>*确保在你不需要使用传感器的时候关掉它，特别是在 Activity 暂停的时候。如若不然，几个小时就会耗尽电池电量。请注意，系统在关闭屏幕的时候是不会自动停止传感器的。*
>**Google APIs** -- [SensorManager](https://developer.android.google.cn/reference/android/hardware/SensorManager.html)

简介
===
智能手机经过近十年的发展，机身上集成了多种传感器（Sensor），一些传感器是基于硬件直接得出结果，另一些则是通过软件复合运算得出的。常见的重力计、陀螺仪以及距离感应计都属于硬件传感器，而方向计则是通过复合计算得出结果，属于软件传感器。

<!--more-->

|Sensor|Type|Description|Common Uses|
|---|---|---|---|
|TYPE_ACCELEROMETER|硬件|测量设备在三个物理轴(x,y,z)上的加速度（m/s²），包括重力|运动检测（摇动，倾斜等）|
|TYPE_AMBIENT_TEMPERATURE|硬件|测量设备周围空间的摄氏温度（℃）|监测空气温度|
|TYPE_GRAVITY|硬件或软件|测量施加在设备三个物理轴向(x,y,z)的重力加速度(m/s²)|运动检测（摇动，倾斜等）|
|TYPE_GYROSCOPE|硬件|测量设备围绕三个物理轴(x,y,z)的旋转速率(rad/s)|旋转检测（旋转，转动等）|
|TYPE_LIGHT|硬件|测量环境光亮度等级流明(lx)|调节屏幕亮度|
|TYPE_LINEAR_ACCELERATION|硬件或软件|测量设备在三个物理轴(x,y,z)上的加速度（m/s²），包括重力|检测单个轴的加速度|
|TYPE_MAGNETIC_FIELD|硬件|检测沿三个物理轴(x,y,z)的磁场强度μT|创建指南针|
|TYPE_ORIENTATION|软件|测量设备围绕三个物理轴(x,y,z)的旋转度。API 等级大于3的可以通过调用重力计和磁场计获取倾斜矩阵和旋转矩阵，再结合 getRotationMatrix() 方法获取方向矩阵|确定设备位置|
|TYPE_PRESSURE|硬件|测量周围的空气压力(hPa or mbar)|检测气压变化|
|TYPE_PROXIMITY|硬件|测量一个物件到屏幕的距离(cm).该传感器通常用于确定手机是否靠近人耳|通话时的设备位置|
|TYPE_RELATIVE_HUMIDITY|硬件|测量周围的湿度(%)|检测露点，绝对和相对湿度|
|TYPE_ROTATION_VECTOR|软件或硬件|通过提供设备的三个旋转矢量元素确定设备的方向|运动和旋转检测|

调用
===

获取传感器
---
要使用传感器，需要先获取手机的传感器服务。通过`getSystemService`创建一个`SensorManager`实例:
``` java
private SensorManager mSensorManager;
...
mSensorManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);
```
接着，可以传入`TYPE_ALL`参数获取传感器列表：
```java
List<Sensor> deviceSensors = mSensorManager.getSensorList(Sensor.TYPE_ALL);
```
如果你想获取指定类型的传感器列表，可以通过传入其他的类型常量，如`TYPE_GYROSCOPE`或者`TYPE_GRAVITY`来取代`TYPE_ALL`.
所有支持的传感器，并不会在所有的手机上都存在，类似于气压计这样的传感器，目前也只在高端机型上存在。所以在调取传感器的时候，需要对其做代码检查，例如
```java
private SensorManager mSensorManager;
...
mSensorManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);
if(mSensorManager.getDefaultSensor(Sensor.TYPE_MAGNETIC_FIELD) != null){
  //Success!存在磁力计
}else{
  //Failure!不存在磁力计
}
```
监控传感器事件
---
要监控传感器事件，必须实现`SensorEventListener`接口的两个回调方法：`onAccuracyChanged()`和`onSensorChanged()`.
### A sensor's accuracy changes.
传感器的精度变化会调用`onAccuracyChanged()`方法。精度有四个常量：`SENSOR_STATUS_ACCURACY_LOW`,`SENSOR_STATUS_ACCURACY_MEDIUM`,`SENSOR_STATUS_ACCURACY_HIGH`和`SENSOR_STATUS_UNRELIABLE`。
### A sensor reports a new value.
传感器返回新的数据时候会调用`onSensorChanged()`方法，提供`SensorEvent`对象，该对象包含传感器的数据，包含数据精度，传感器，数据生成时间和传感器生成的数据。

接下来将通过一个指南针的例子来介绍传感器事件监听的运行机制。通过对 API 的查询了解，`SENSOR_ORIENTATION`已经在 API level 8废弃了，想要通过`getDefaultSensor()`方法直接获取方向参数已经不可取。官方给出的替代方法是一个静态(static)方法`getOrientation(float[] R, float[] values)`.

### getOrientation
基于旋转矩阵获取设备的方向。

|请求参数||
|---|---|
|R|float：旋转矩阵，通过`getRotationMatrix(float[], float[], float[], float[])`方法获取|
|values|float：包含三个float的数组|

|返回参数||
|---|---|
|float[]|返回的数组|

### getRotationMatrix
`getRotationMatrix(float[] R, float[] I, float[] gravity, float[] geomagnetic)`
计算倾斜矩阵**I**和旋转矩阵**R**并将其通过矢量变换从设备的坐标系转换成真实世界正交坐标系：
* X 定义为Y·Z的向量积（在设备所在的位置和地面相切，并大致指向东方）。
* Y 在设备所在的位置与地面相切，并指向磁北极。
* Z 指向天空并垂直于地面。
![正交坐标系](/images/android/sensor/axis_globe.png)
其中

|请求参数||
|---|---|
|R|float:9个float参数的数组，保存旋转矩阵R的数据。R可以为空|
|I|float:9个float参数的数组，保存倾斜矩阵I的数据。I可以为空|
|gravity|float:3个float参数的数组，包含基于设备坐标系的重力矢量。使用类型为`TYPE_ACCELEROMETER`的传感器返回的数据|
|geomagnetic|float:3个float参数的数组，包含基于设备坐标系的磁场矢量。使用类型为`TYPE_MAGNETIC_FIELD`的传感器返回的数据|

|返回参数||
|---|---|
|boolean|`true`表示成功，`false`表示失败(当设备处于自由落体状态)。自由落体的界定，当设备承受的重力小于标称值的1/10时，视为自由落体。失败时，输出的矩阵值不会修改|

### 构建一个指南针

在现实世界的正交坐标系中，磁北极是固定方向的。想要构造一个指南针应用，最直观的思考是获取到设备朝向和所处位置磁北极方向的夹角。这个时候，直观的思考就是正确的思考，如何获取这样一个夹角呢。通过上面介绍的 API 来看，`getRotationMatrix()`方法，能够获得设备的旋转矩阵和倾斜矩阵。旋转矩阵是一个3*3或者4*4的矩阵，通过`getOrientation()`方法就能直接获得设备关于正交系的三轴旋转数据。设备的指向必然是设备关于z轴的旋转角度。如何取值可以参考源码或者文档：
> * values[0]: Azimuth, angle of rotation about the -z axis. This value represents the angle between the device's y axis and the magnetic north pole. When facing north, this angle is 0, when facing south, this angle is π. Likewise, when facing east, this angle is π/2, and when facing west, this angle is -π/2. The range of values is -π to π.
* values[1]: Pitch, angle of rotation about the x axis. This value represents the angle between a plane parallel to the device's screen and a plane parallel to the ground. Assuming that the bottom edge of the device faces the user and that the screen is face-up, tilting the top edge of the device toward the ground creates a positive pitch angle. The range of values is -π to π.
* values[2]: Roll, angle of rotation about the y axis. This value represents the angle between a plane perpendicular to the device's screen and a plane perpendicular to the ground. Assuming that the bottom edge of the device faces the user and that the screen is face-up, tilting the left edge of the device toward the ground creates a positive roll angle. The range of values is -π/2 to π/2.

可知，此时此刻的`values[0]`正是我们想要的值。回归到代码的世界，是这样的：
```java
public class SensorActivity extends Activity implements SensorEventListener{
	//sensor object
	private SensorManager mSensorManager;
	private Sensor mAccelerometer;
	private Sensor mMagneticField;
	//sensor data
	private float[] r = new float[9];	//rotation matrix
	private float[] values = new float[3]	//orientation values
	private float[] accelerometerValues = new float[3]	//data of acclerometer sensor
	private float[] magneticFieldValues = new fooat[3]	//data of magnetic field sensor

	@Ovrride
	protected void onCreate(Bundle savedInstanceState) {
		...
		//init sensor
		mSensorManager = (SensorManager) getSystemService(SENSOR_SERVICE);
        	mAccelerometer = mSensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
        	mMagneticField = mSensorManager.getDefaultSensor(Sensor.TYPE_MAGNETIC_FIELD);
		...
	}

	
    	@Override
    	protected void onResume() {
        	super.onResume();
		//regist listener
        	mSensorManager.registerListener(this, mAccelerometer, SensorManager.SENSOR_DELAY_NORMAL);
        	mSensorManager.registerListener(this, mMagneticField, SensorManager.SENSOR_DELAY_NORMAL);
    		...
    	}

	@Override
    	protected void onPause() {
        	super.onPause();
		//unregist listener
        	mSensorManager.unregisterListener(this);
    	}

	@Override
    	public void onSensorChanged(SensorEvent sensorEvent) {
        	if (sensorEvent.sensor.getType() == Sensor.TYPE_ACCELEROMETER) {    
        	    accelerometerValues = sensorEvent.values;
        	}
        	if (sensorEvent.sensor.getType() == Sensor.TYPE_MAGNETIC_FIELD) { 
        	    magneticFieldValues = sensorEvent.values;
        	}
        	SensorManager.getRotationMatrix(r, null, accelerometerValues, magneticFieldValues);
        	SensorManager.getOrientation(r, values);
        	values[0] = (float) Math.toDegrees(values[0]);
    	}

    	@Override
    	public void onAccuracyChanged(Sensor sensor, int i) {

    	}
}
```
整个过程是很流畅的，由于传感器本身是不受Activity的生命周期控制，并且系统并不会在关闭屏幕时关闭传感器，所以，请注意手动控制好传感器的开闭，以节省电量。
