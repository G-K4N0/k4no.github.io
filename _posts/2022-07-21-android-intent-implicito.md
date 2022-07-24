---
layout: single
title: Intent-Implicito (Scanner)
excerpt: "Un intent implicito en android, es la capacidad de hacer uso de una funcionalidad especifica para una aplicación sin que nosotros lo desarrollemos, esto se logra haciendo uso de uno que ya está instalado en el celular y posteriormente el dato que nos interesa, tratarlo en nuestra propia aplicación. Esto es muy usado por muchas aplicaciones hoy en dia, como el de compartir un archivo o simplemente tomar una foto."
date: 2021-07-20
classes: wide
header:
  teaser: /assets/images/android_Activity/activity_android.png
  teaser_home_page: true
  icon: /assets/images/android_logo.png
categories:
  - Android
  - Development
tags:  
  - android
  - Intents
  - Java
  - Scanner QR
---

![Intent Implicito](/assets/images/android_scanner_implicit/scann.jpg)

El ejercicio consta de hacer uso de una aplicación de terceros y traer la información a nuestro aplicativo, la ventaja es que solo son necesarios unas cuantas lineas de código para poder implentar las funcionalidades que nosotros necesitemos, ya que si lo hacemos desde cero seria mas tardado el desarrollo.

Iniciamos con el layout el cúal es bastante sencillo, consta de un **Button** para poder iniciar el lector de codigos y un **TextView** para mostrar la información escaneada.

![layout](/assets/images/android_scanner_implicit/layout_scanner.png)
## main_layout.xml

```
   1   │  <?xml version="1.0" encoding="utf-8"?>
   2   │ <android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
   3   │     xmlns:app="http://schemas.android.com/apk/res-auto"
   4   │     xmlns:tools="http://schemas.android.com/tools"
   5   │     android:layout_width="match_parent"
   6   │     android:layout_height="match_parent"
   7   │     tools:context=".MainActivity">
   8   │ 
   9   │     <Button
  10   │         android:id="@+id/btnScan"
  11   │         android:layout_width="wrap_content"
  12   │         android:layout_height="wrap_content"
  13   │         android:layout_marginStart="8dp"
  14   │         android:layout_marginEnd="8dp"
  15   │         android:layout_marginBottom="64dp"
  16   │         android:text="Scan"
  17   │         app:layout_constraintBottom_toBottomOf="parent"
  18   │         app:layout_constraintEnd_toEndOf="parent"
  19   │         app:layout_constraintStart_toStartOf="parent" />
  20   │ 
  21   │     <TextView
  22   │         android:id="@+id/txtInfo"
  23   │         android:layout_width="wrap_content"
  24   │         android:layout_height="wrap_content"
  25   │         android:layout_marginStart="8dp"
  26   │         android:layout_marginTop="128dp"
  27   │         android:layout_marginEnd="8dp"
  28   │         app:layout_constraintEnd_toEndOf="parent"
  29   │         app:layout_constraintStart_toStartOf="parent"
  30   │         app:layout_constraintTop_toTopOf="parent" />
  31   │ 
  32   │ </android.support.constraint.ConstraintLayout>
```
## build.gradle
 Las siguentes lineas se colocan en las dependencias para poder ocupar la libreria.

- `implementation 'com.journeyapps:zxing-android-embedded:4.3.0`
- `implementation 'com.google.zxing:core:3.3.0`

```
dependencies {
       implementation 'com.android.support:appcompat-v7:28.0.0'
       implementation 'com.android.support.constraint:constraint-layout:2.0.4'

       implementation 'com.journeyapps:zxing-android-embedded:4.3.0
       implementation 'com.google.zxing:core:3.3.0
       
       testImplementation 'junit:junit:4.13.2'
       androidTestImplementation 'com.android.support.test:runner:1.0.2'
       androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
       }
  ```

## MainActivity.java

Aqui vamos a ocupar la libreria de `zxing`, esta libreria será la estaremos ocupando en la linea `27` del codigo de ejemplo.
```
   1   │ package com.talentounido.qrscanner;
   2   │ 
   3   │ import android.content.Intent;
   4   │ import android.provider.MediaStore;
   5   │ import android.support.annotation.Nullable;
   6   │ import android.support.v7.app.AppCompatActivity;
   7   │ import android.os.Bundle;
   8   │ import android.widget.Button;
   9   │ import android.widget.TextView;
  10   │ 
  11   │ public class MainActivity extends AppCompatActivity {
  12   │ 
  13   │     private Button btnScan;
  14   │     private TextView txtView;
  15   │ 
  16   │     private static final int QR_SCAN_CODE = 5;
  17   │ 
  18   │     @Override
  19   │     protected void onCreate(Bundle savedInstanceState) {
  20   │         super.onCreate(savedInstanceState);
  21   │         setContentView(R.layout.activity_main);
  22   │ 
  23   │         btnScan = findViewById(R.id.btnScan);
  24   │         txtView = findViewById(R.id.txtInfo);
  25   │ 
  26   │         btnScan.setOnClickListener(v -> {
  27   │             Intent scan_init = new Intent("com.google.zxing.client.android.SCAN");
  28   │             if(scan_init.resolveActivity(getPackageManager())!= null){
  29   │               startActivityForResult(scan_init,QR_SCAN_CODE);
  30   │             }
  31   │         });
  32   │     }
  33   │ 
  34   │     @Override
  35   │     protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
  36   │         super.onActivityResult(requestCode, resultCode,data);
  37   │ 
  38   │         if (requestCode==QR_SCAN_CODE && resultCode == RESULT_OK) {
  39   │           
  40   │           Bundle extras = data.getExtras();
  41   │           String info = (String) extras.get("SCAN_RESULT");
  42   │           txtView.setText(info);
  43   │         }
  44   │ 
  45   │     }
  46   │ }

```

## Ejecución

![](/assets/images/android_scanner_implicit/scanner_init.png)

## Escaneo de código
![Escaneo](/assets/images/android_scanner_implicit/code_scanning.png)

## Resultado del escaneo

![Resultado](/assets/images/android_scanner_implicit/result_scanning.png)

Cabe destacar que tengo instalado un lector de código de barras/qr al que llamo desde la aplicación de práctica.
![Apps](/assets/images/android_scanner_implicit/apps.png)

Este ejemplo se encuentra en:
- [https://github.com/G-K4N0/QRSCANNER](https://github.com/G-K4N0/QRSCANNER)

Repositorio de la libreria:
- [https://github.com/zxing/zxing](https://github.com/zxing/zxing)
