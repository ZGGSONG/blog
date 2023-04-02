---
title: 安卓计算器简单实例
date: 2020-07-15T17:20:00+08:00
lastmod: 2020-07-16T03:08:16+08:00
description: ""
featuredImage: ""
categories:
- coding
tags:
- android
---

## 前言

安卓实现计算器完成简单的四则运算，完全使用线性布局 (LinearLayout) 嵌套实现。

效果图如下

![计算器](https://cdn.zggsong.cn/2020/07/15/57a910e871698.jpg)

布局文件
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:paddingBottom="10dp"
    android:paddingLeft="10dp"
    android:paddingRight="10dp"
    android:paddingTop="10dp"
    tools:context="com.example.calculator.Calculator"
    >
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_weight="7">
        <EditText
            android:id="@+id/et_input"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:inputType="none"
            android:hint="@string/app_name"
            android:textSize="50dp"
            android:textStyle="bold"
            android:gravity="center|right"
            android:background="#f0f0f0"/>
    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_weight="3"
        android:layout_marginTop="20dp"
        android:orientation="horizontal">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:orientation="vertical">
            <!--C del / * -->
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:orientation="horizontal"
                android:layout_weight="1">
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:paddingBottom="5dp"
                    android:paddingLeft="5dp"
                    android:paddingRight="5dp"
                    android:paddingTop="5dp"
                    android:layout_weight="1">
                    <Button
                        android:id="@+id/btn_clear"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/btn_selector"
                        android:text="AC"
                        android:textSize="26sp"/>
                </LinearLayout>
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:paddingBottom="5dp"
                    android:paddingLeft="5dp"
                    android:paddingRight="5dp"
                    android:paddingTop="5dp"
                    android:layout_weight="1">
                    <Button
                        android:id="@+id/btn_del"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/btn_selector"
                        android:text="DEL"
                        android:textSize="26sp"/>
                </LinearLayout>
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:paddingBottom="5dp"
                    android:paddingLeft="5dp"
                    android:paddingRight="5dp"
                    android:paddingTop="5dp"
                    android:layout_weight="1">
                    <Button
                        android:id="@+id/btn_divide"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/btn_selector"
                        android:text="/"
                        android:textSize="26sp" />
                </LinearLayout>
            </LinearLayout>
            <!--7 8 9 - -->
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:orientation="horizontal"
                android:layout_weight="1">
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:paddingBottom="5dp"
                    android:paddingLeft="5dp"
                    android:paddingRight="5dp"
                    android:paddingTop="5dp"
                    android:layout_weight="1">
                    <Button
                        android:id="@+id/btn_7"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/btn_selector_num"
                        android:text="7"
                        android:textSize="26sp"/>
                </LinearLayout>
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:paddingBottom="5dp"
                    android:paddingLeft="5dp"
                    android:paddingRight="5dp"
                    android:paddingTop="5dp"
                    android:layout_weight="1">
                    <Button
                        android:id="@+id/btn_8"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/btn_selector_num"
                        android:text="8"
                        android:textSize="26sp"/>
                </LinearLayout>
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:paddingBottom="5dp"
                    android:paddingLeft="5dp"
                    android:paddingRight="5dp"
                    android:paddingTop="5dp"
                    android:layout_weight="1">
                    <Button
                        android:id="@+id/btn_9"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/btn_selector_num"
                        android:text="9"
                        android:textSize="26sp" />
                </LinearLayout>

            </LinearLayout>
            <!--4 5 6  + -->
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:orientation="horizontal"
                android:layout_weight="1">
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:paddingBottom="5dp"
                    android:paddingLeft="5dp"
                    android:paddingRight="5dp"
                    android:paddingTop="5dp"
                    android:layout_weight="1">
                    <Button
                        android:id="@+id/btn_4"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/btn_selector_num"
                        android:text="4"
                        android:textSize="26sp"/>
                </LinearLayout>
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:paddingBottom="5dp"
                    android:paddingLeft="5dp"
                    android:paddingRight="5dp"
                    android:paddingTop="5dp"
                    android:layout_weight="1">
                    <Button
                        android:id="@+id/btn_5"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/btn_selector_num"
                        android:text="5"
                        android:textSize="26sp"/>
                </LinearLayout>
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:paddingBottom="5dp"
                    android:paddingLeft="5dp"
                    android:paddingRight="5dp"
                    android:paddingTop="5dp"
                    android:layout_weight="1">
                    <Button
                        android:id="@+id/btn_6"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/btn_selector_num"
                        android:text="6"
                        android:textSize="26sp" />
                </LinearLayout>
            </LinearLayout>
            <!-- 1 2 3 = -->
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:orientation="horizontal"
                android:layout_weight="1">
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:paddingBottom="5dp"
                    android:paddingLeft="5dp"
                    android:paddingRight="5dp"
                    android:paddingTop="5dp"
                    android:layout_weight="1">
                    <Button
                        android:id="@+id/btn_1"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/btn_selector_num"
                        android:text="1"
                        android:textSize="26sp"/>
                </LinearLayout>
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:paddingBottom="5dp"
                    android:paddingLeft="5dp"
                    android:paddingRight="5dp"
                    android:paddingTop="5dp"
                    android:layout_weight="1">
                    <Button
                        android:id="@+id/btn_2"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/btn_selector_num"
                        android:text="2"
                        android:textSize="26sp"/>
                </LinearLayout>
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:paddingBottom="5dp"
                    android:paddingLeft="5dp"
                    android:paddingRight="5dp"
                    android:paddingTop="5dp"
                    android:layout_weight="1">
                    <Button
                        android:id="@+id/btn_3"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/btn_selector_num"
                        android:text="3"
                        android:textSize="26sp" />
                </LinearLayout>
            </LinearLayout>
            <!-- 0 .  = -->
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:orientation="horizontal"
                android:layout_weight="1">
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:paddingBottom="5dp"
                    android:paddingLeft="5dp"
                    android:paddingRight="5dp"
                    android:paddingTop="5dp"
                    android:layout_weight="3">
                    <Button
                        android:id="@+id/btn_0"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/btn_selector_num"
                        android:text="0"
                        android:textSize="26sp"/>
                </LinearLayout>
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:paddingBottom="5dp"
                    android:paddingLeft="5dp"
                    android:paddingRight="5dp"
                    android:paddingTop="5dp"
                    android:layout_weight="6">
                    <Button
                        android:id="@+id/btn_point"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/btn_selector_num"
                        android:text="."
                        android:textSize="26sp"/>
                </LinearLayout>

            </LinearLayout>

        </LinearLayout>
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_weight="3"
            android:orientation="vertical">
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:paddingBottom="5dp"
                android:paddingLeft="5dp"
                android:paddingRight="5dp"
                android:paddingTop="5dp"
                android:layout_weight="4">
                <Button
                    android:id="@+id/btn_multply"
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:background="@drawable/btn_selector"
                    android:text="*"
                    android:textSize="26sp"/>
            </LinearLayout>

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:paddingBottom="5dp"
                android:paddingLeft="5dp"
                android:paddingRight="5dp"
                android:paddingTop="5dp"
                android:layout_weight="4">
                <Button
                    android:id="@+id/btn_minus"
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:background="@drawable/btn_selector"
                    android:text="-"
                    android:textSize="26sp"/>
            </LinearLayout>

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:paddingBottom="5dp"
                android:paddingLeft="5dp"
                android:paddingRight="5dp"
                android:paddingTop="5dp"
                android:layout_weight="4">
                <Button
                    android:id="@+id/btn_plus"
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:background="@drawable/btn_selector"
                    android:text="+"
                    android:textSize="26sp"/>
            </LinearLayout>

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:paddingBottom="5dp"
                android:paddingLeft="5dp"
                android:paddingRight="5dp"
                android:paddingTop="5dp"
                android:layout_weight="3">
                <Button
                    android:id="@+id/btn_equal"
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:background="@drawable/btn_bg_normal_equal"
                    android:text="="
                    android:textSize="26sp"/>
            </LinearLayout>

        </LinearLayout>

    </LinearLayout>

</LinearLayout>
```

Java 代码
```java
package com.example.calculator;

import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

public class Calculator extends AppCompatActivity implements View.OnClickListener {
    private Button btn_0;//0 数字按钮
    private Button btn_1;//1 数字按钮
    private Button btn_2;//2 数字按钮
    private Button btn_3;//3 数字按钮
    private Button btn_4;//4 数字按钮
    private Button btn_5;//5 数字按钮
    private Button btn_6;//6 数字按钮
    private Button btn_7;//7 数字按钮
    private Button btn_8;//8 数字按钮
    private Button btn_9;//9 数字按钮
    private Button btn_point;//小数点按钮
    private Button btn_clear;//clear 按钮
    private Button btn_del;//del 按钮
    private Button btn_plus;//+按钮
    private Button btn_minus;//-按钮
    private Button btn_multply;//*按钮
    private Button btn_divide;//除号按钮
    private Button btn_equal;//=按钮
    private EditText editText;

    boolean clear_flag;//清空标识

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_calculator);

//        绑定按钮
        btn_0 = (Button) findViewById(R.id.btn_0);
        btn_1 = (Button) findViewById(R.id.btn_1);
        btn_2 = (Button) findViewById(R.id.btn_2);
        btn_3 = (Button) findViewById(R.id.btn_3);
        btn_4 = (Button) findViewById(R.id.btn_4);
        btn_5 = (Button) findViewById(R.id.btn_5);
        btn_6 = (Button) findViewById(R.id.btn_6);
        btn_7 = (Button) findViewById(R.id.btn_7);
        btn_8 = (Button) findViewById(R.id.btn_8);
        btn_9 = (Button) findViewById(R.id.btn_9);
        btn_point = (Button) findViewById(R.id.btn_point);
        btn_clear = (Button) findViewById(R.id.btn_clear);
        btn_del = (Button) findViewById(R.id.btn_del);
        btn_plus = (Button) findViewById(R.id.btn_plus);
        btn_minus = (Button) findViewById(R.id.btn_minus);
        btn_multply = (Button) findViewById(R.id.btn_multply);
        btn_divide = (Button) findViewById(R.id.btn_divide);
        btn_equal = (Button) findViewById(R.id.btn_equal);
        editText = (EditText) findViewById(R.id.et_input);

//        绑定事件
        btn_0.setOnClickListener(this);
        btn_1.setOnClickListener(this);
        btn_2.setOnClickListener(this);
        btn_3.setOnClickListener(this);
        btn_4.setOnClickListener(this);
        btn_5.setOnClickListener(this);
        btn_6.setOnClickListener(this);
        btn_7.setOnClickListener(this);
        btn_8.setOnClickListener(this);
        btn_9.setOnClickListener(this);
        btn_point.setOnClickListener(this);
        btn_clear.setOnClickListener(this);
        btn_del.setOnClickListener(this);
        btn_plus.setOnClickListener(this);
        btn_minus.setOnClickListener(this);
        btn_multply.setOnClickListener(this);
        btn_divide.setOnClickListener(this);
        btn_equal.setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        String input = editText.getText().toString();
        switch (view.getId()){
            case R.id.btn_0:
            case R.id.btn_1:
            case R.id.btn_2:
            case R.id.btn_3:
            case R.id.btn_4:
            case R.id.btn_5:
            case R.id.btn_6:
            case R.id.btn_7:
            case R.id.btn_8:
            case R.id.btn_9:
            case R.id.btn_point:
                if(clear_flag){
                    clear_flag = false;
                    editText.setText("");
                }
                editText.setText(input + ((Button)view).getText());
                break;
            case R.id.btn_plus:
            case R.id.btn_minus:
            case R.id.btn_multply:
            case R.id.btn_divide:
                if(clear_flag){
                    clear_flag = false;
                    input = "";
                    editText.setText("");
                }
                editText.setText(input + " " + ((Button)view).getText() + " ");
                break;
            case R.id.btn_clear:
                clear_flag = false;
                input = "";
                editText.setText("");
                break;
            case R.id.btn_del:
                if(clear_flag){
                    clear_flag = false;
                    input = "";
                    editText.setText("");
                }else if(input != null || !input.equals("")) {
                    editText.setText(input.substring(0, input.length() - 1));
                }
                break;
            case R.id.btn_equal:
                getResult();
                break;
        }
    }
    //运算结果
    private void getResult(){
        String exp = editText.getText().toString();
        if(exp==null||exp.equals(""))
            return;
        if(!exp.contains(" "))
            return;
        if(clear_flag){
            clear_flag = false;
            return;
        }
        clear_flag = true;
        double result = 0;
        //运算符前的数字
        String s1 = exp.substring(0,exp.indexOf(" "));
        //运算符
        String op = exp.substring(exp.indexOf(" ")+1,exp.indexOf(" ")+2);
        //运算符后的数字
        String s2 = exp.substring(exp.indexOf(" ")+3);

        if(!s1.equals("")&&!s2.equals("")) {
            double d1 = Double.parseDouble(s1);
            double d2 = Double.parseDouble(s2);
            if (op.equals("+")) {
                result = d1 + d2;
            } else if (op.equals("-")) {
                result = d1 - d2;
            } else if (op.equals("*")) {
                result = d1 * d2;
            } else if (op.equals("/")) {
                if (d2 == 0)
                    result = 0;
                else
                    result = d1 / d2;
            }

            if (!s1.contains(".") && !s2.contains(".") && !op.equals("/")) {
                int r = (int) result;
                editText.setText(r + "");
            } else
                editText.setText(result + "");
        }else if(!s1.equals("") && s2.equals("")){
            editText.setText(exp);
        }else if(s1.equals("") && !s2.equals("")){
            double d2 = Double.parseDouble(s2);
            if (op.equals("+")) {
                result = 0 + d2;
            } else if (op.equals("-")) {
                result = 0 - d2;
            } else if (op.equals("*")) {
                result = 0;
            } else if (op.equals("/")) {
                result = 0;
            }

            if (!s1.contains(".") && !s2.contains(".")) {
                int r = (int) result;
                editText.setText(r + "");
            } else
                editText.setText(result + "");
        }else {
            editText.setText("");
        }

    }
}
```
 
按钮圆角化和按下效果
```xml
android:background="@drawable/btn_selector"
```

btn_selector.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- Button 正常状态下的背景 -->
    <item android:drawable="@drawable/btn_bg_normal" android:state_pressed="false"/>
    <!-- Button 按下时的背景 -->
    <item android:drawable="@drawable/btn_bg_pressed" android:state_pressed="true"/>
</selector>
```

btn_bg_normal.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<!-- 按钮正常的时候的背景 -->
<!-- shape 的默认形状是 rectangle，还有 oval（椭圆）,line（线）,ring（圆环） -->

<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- 矩形的圆角弧度 -->
    <corners android:radius="10dp" />
    <!-- 矩形的填充色 -->
    <solid android:color="#85929E" />

    <!-- 矩形的边框（实线）的宽度，边框的颜色 -->
    <stroke
        android:width="1dp"
        android:color="#85929E" />
</shape>
```

btn_bg_pressed.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- 矩形的圆角弧度 -->
    <corners android:radius="10dp" />
    <!-- 矩形的填充色 -->
    <solid android:color="#FFF" />
    <!-- 矩形的边框（实线）的宽度，边框的颜色 -->
<stroke
    android:width="1dp"
    android:color="#FFF" />
</shape>
```
## 参考

- [https://blog.csdn.net/tracydragonlxy/article/details/77983241](https://blog.csdn.net/tracydragonlxy/article/details/77983241)
- [https://blog.csdn.net/leilifengxingmw/article/details/50783574](https://blog.csdn.net/leilifengxingmw/article/details/50783574)
