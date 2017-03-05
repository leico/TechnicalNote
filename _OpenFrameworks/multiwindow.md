---
layout : post
title  : ネイティブ対応したマルチウィンドウの実験
date   : 2016/09/13
lastchange : 2017-03-06 01:32:03.
tags   :
  - C++
  - multi window
  - openFrameworks
  - 0.9
---

openFrameworks 0.9からGLFWを利用してマルチウィンドウなアプリケーションを作成できるようになった。

```
examples -> events -> multiWindowExample
```

に複数画面の例が入っている。やらないといけないことはwindow分ofAppクラスを用意して、
`GLFWWindowSettings`に合わせてwindowの設定を記述、`createWindow`した後、
`window`と`ofApp`等のルーチンを紐づけ、メインルーチンをぶん回す。

## window分ofAppクラスを作る

### ofApp.h

```cpp
class ofApp : public ofBaseApp{

  ofxOscReceiver receiver;

  KinectCV kinectcv; 

  public:
  void setup();
  void exit();
  void update();
  void draw();

  void keyPressed(int key);
  void keyReleased(int key);
  void mouseMoved(int x, int y);
  void mouseDragged(int x, int y, int button);
  void mousePressed(int x, int y, int button);
  void mouseReleased(int x, int y, int button);
  void windowResized(int w, int h);
  void dragEvent(ofDragInfo dragInfo);
  void gotMessage(ofMessage msg);

  shared_ptr<SecondScreen> secondscreen;
};
```

### SecondScreen.h

```cpp
class SecondScreen : public ofBaseApp{

  public:
  void setup (void);
  void exit  (void);
  void update(void);
  void draw  (void);

  void keyPressed   (int key);
  void keyReleased  (int key);
  void mouseMoved   (int x, int y);
  void mouseDragged (int x, int y, int button);
  void mousePressed (int x, int y, int button);
  void mouseReleased(int x, int y, int button);

  void windowResized(int w, int h);

  void dragEvent (ofDragInfo dragInfo);
  void gotMessage(ofMessage msg);

};
```

今回は2画面なのでこの2つのクラスを作った。
ここで重要なのは、別のWindowアプリをコントロールする側に

```cpp
shared_ptr<クラス名> name;
```

という変数が組み込まれていること。

## `GLFWWindowSettings`に合わせてWindowの設定を記述

### main関数

```cpp
ofGLFWWindowSettings settings;

settings.width  = 300;
settings.height = 300;
settings.setPosition( ofVec2f(20, 20) );
settings.resizable = true;
shared_ptr<ofappbasewindow> mainwindow = ofCreateWindow(settings);

settings.width  = 300;
settings.height = 300;
settings.setPosition( ofVec2f(400, 20) );
settings.resizable = true;
shared_ptr<ofappbasewindow> secondwindow = ofCreateWindow(settings);
```

サイズや画面を出す位置、リサイズ可能にするかどうかなどの設定をしてWindowを作る。
`window`と`ofApp`等のルーチンを紐づけてメインルーチンを回す

### main関数続き

```cpp
shared_ptr<ofapp> mainApp(new ofApp);
shared_ptr<secondscreen> secondApp(new SecondScreen);

mainApp -> secondscreen = secoundApp;

ofRunApp(mainwindow, mainApp);
ofRunApp(secondwindow, secondApp);

ofRunMainLoop();
```

もし他のWindowに何か反映させるデータがある場合、先ほど作ったクラス内の`shared_ptr`に反映させたい側を渡す。

## キー入力実験

キー入力関係の関数はフォーカスがあるWindowの関数が呼ばれる。
フォーカスが合っているWindowはバーの色が濃く、閉じるなどのボタンの色が付いている。

## 個別の操作スペースでのフルスクリーン実験

各々のWindowを個別の画面でフルスクリーンすることができる。
また、メニューバーは全てのWindowをフルスクリーンにすると隠れる。
