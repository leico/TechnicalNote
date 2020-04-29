---
layout : post
title  : 古い openFrameworks のプロジェクトを最新版に対応させる
date   : 2020/04/30
lastchange : 2020-04-30 02:44:18.
tags   :
  - C++
  - openFrameworks
  - 0.0.6
  - 0.11
---

## openFrameworks の各バージョンが場所を取り始めた

ので、古いファイルから最新バージョンで動作するように移行を開始した。

参考:

* [oF0.9.0 変更されたAPI 要点まとめ - Qiita](https://qiita.com/2bbb/items/13f2e20760ec61e3ec89)
* [c++ - No member named idleMovie in ofVideoPlayer - Stack Overflow](https://stackoverflow.com/questions/26310285/no-member-named-idlemovie-in-ofvideoplayer)
* [音声入力でofSoundStreamSetupがDeprecated - Qiita](https://qiita.com/k6t4n/items/f140c1954051ce88506a)
* [lecture:インタラクションデザイン:演習実習a \[\]](https://ws.tetsuakibaba.jp/doku.php?id=lecture:%E3%82%A4%E3%83%B3%E3%82%BF%E3%83%A9%E3%82%AF%E3%82%B7%E3%83%A7%E3%83%B3%E3%83%87%E3%82%B6%E3%82%A4%E3%83%B3:%E6%BC%94%E7%BF%92%E5%AE%9F%E7%BF%92a)


## XCode プロジェクトの更新

過去の XCode プロジェクトをそのまま移行すると、プロジェクト設定が大きく変わっているので不都合が出そうだった。
なので現行の _emptyExample_ をコピーし、内部の _src_ やプロジェクトが参照しているソースのリンクを修正する方針にした。


## _main.cpp_ の修正

**0062** では

``` cpp
int main( ){

  ofAppGlutWindow window;
	ofSetupOpenGL(&window, 800,600, OF_WINDOW);			// <-------- setup the GL context

	// this kicks off the running of my app
	// can be OF_WINDOW or OF_FULLSCREEN
	// pass in width and height too:
	ofRunApp( new testApp());

}
```

こうなっていたのだが、この記事執筆中現行の **0.11.0** の _emptyExample_ に倣い、

``` cpp
int main( ){

	ofSetupOpenGL(1024,768, OF_WINDOW);			// <-------- setup the GL context

	// this kicks off the running of my app
	// can be OF_WINDOW or OF_FULLSCREEN
	// pass in width and height too:
	ofRunApp( new testApp());

}
```

に変更する。



## 描画系の変更

{% capture text %}

### ex.

| new               | old           |
|-------------------|---------------|
| ofDrawLine        | ofLine        |
| ofDrawCurve       | ofCurve       |
| ofDrawBezier      | ofBezier      |
| ofDrawCircle      | ofCircle      |
| ofDrawEllipse     | ofEllipse     |
| ofDrawTriangle    | ofTriangle    |
| ofDrawRectangle   | ofRect        |
| ofDrawRectRounded | ofRectRounded |
| ofDrawSphere      | ofSphere      |
| ofDrawCone        | ofCone        |
| ofBox             | ofDrawBox     |

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[oF0.9.0 変更されたAPI 要点まとめ - Qiita](https://qiita.com/2bbb/items/13f2e20760ec61e3ec89)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

描画系で depredated 警告を受けたので、引用の左のものへ変更する。



## `update()` に統一されている

{% capture text %}
`vidPlayer.idleMovie();` replaced with `vidPlayer.update();`

and `vidGrabber.grabFrame();` got replaced by `vidGrabber.update();`
{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[c++ - No member named idleMovie in ofVideoPlayer - Stack Overflow](https://stackoverflow.com/questions/26310285/no-member-named-idlemovie-in-ofvideoplayer)

by [PeterT](https://stackoverflow.com/users/777689/petert)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

ということで、クラス毎にさまざまだった `update()` 関数内の処理が `update()` へ統一されているようだ。



## `setup()` 関数へ統一

{% capture text %}


## ofVideoGrabber

`setup` に揃いました.

### ex.

| new   | old         |
|-------|-------------|
| setup | initGrabber |

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[oF0.9.0 変更されたAPI 要点まとめ - Qiita](https://qiita.com/2bbb/items/13f2e20760ec61e3ec89)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


## オーディオ周りの変更


{% capture text %}

具体的には、ofSoundStreamSetupをこのクラスに置き換える

* ofSoundStream
* ofSoundStreamSettings


{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[音声入力でofSoundStreamSetupがDeprecated - Qiita](https://qiita.com/k6t4n/items/f140c1954051ce88506a)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}




{% capture text %}

``` cpp
class ofApp : public ofBaseApp{
    
public:
    void setup();
    void update();
    void draw();
    
    void audioIn(ofSoundBuffer &buffer);
    ofSoundStream sound_stream;
    float buffer_input[1024];
};
```

---

``` cpp
void ofApp::setup(){
    ofSetFrameRate(60);
    ofSoundStreamSettings settings;
    sound_stream.printDeviceList();
    settings.setInListener(this);
    settings.sampleRate = 44100;
    settings.numOutputChannels=0;
    settings.numInputChannels=1;
    settings.bufferSize = 1024;
    sound_stream.setup(settings);
}
```

---

``` cpp
void ofApp::audioIn(ofSoundBuffer &buffer)
{
    for( int i = 0; i < 1024; i++ ){
        buffer_input[i] = buffer[i];
    }
}
```

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[lecture:インタラクションデザイン:演習実習a \[\]](https://ws.tetsuakibaba.jp/doku.php?id=lecture:%E3%82%A4%E3%83%B3%E3%82%BF%E3%83%A9%E3%82%AF%E3%82%B7%E3%83%A7%E3%83%B3%E3%83%87%E3%82%B6%E3%82%A4%E3%83%B3:%E6%BC%94%E7%BF%92%E5%AE%9F%E7%BF%92a)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}


変更が多いので個別に見ていく。

まずは、今まで本体クラスと一体化していた音声処理が分離したようだ。なので _testApp.cpp_ 内の `testApp` クラスに

``` cpp
class testApp : public ofBaseApp{
  ...

  public:
    ...
    ofSoundSream soundstream;
};
```

と `ofSoundStream` クラスを持つ必要がある。

`ofSoundStream` の初期化には `ofSoundStreamSettings` クラスを用いる。今まで

``` cpp
ofSoundStreamSetup(
   2      // output channel count
 , 0      // input channel count
 , this   // event listener class
 , 44100  // sampling rate
 , 1024   // buffer size
 , 4      // num buffers
);
```

としていた処理は

``` cpp
ofSoundStreamSettings settings;

settings.setOutListener(this);
settings.setInListener (this);

settings.sampleRate        = 44100;
settings.numInputChannels  = 0;
settings.numOutputChannels = 2;
settings.bufferSize        = 1024
settings.numBuffers        = 4;

soundstream.setup( settings );

```

このようになる。 `setOutListener()` 、 `setInListener()` と、入出力のイベント処理を行うリスナークラスを分けることができるようになっている。

また、リスナークラスのコールバック関数も変更されている。以前は

``` cpp
void testApp :: audioReceived ( float * input , int bufferSize, int nChannels ){}
void testApp :: audioRequested( float * output, int bufferSize, int nChannels ){}
```

だったものが

``` cpp
void testApp :: audioIn ( ofSoundBuffer &input  ){}
void testApp :: audioOut( ofSoundBuffer &output ){}
```

に変わり、

``` cpp
void testApp :: audioRequested( float * output, int bufferSize, int nChannels ){

  for (int f = 0 ; f < bufferSize ; ++ f ) {
    for ( int ch = 0 ; ch < nChannels ; ++ ch ){
      output[f * nChannels + ch ] = audiodata[f * nChannels + ch ];
    }
  }
}

```

このような処理だったら

``` cpp
void testApp :: audioOut(ofSoundBuffer & output){

  const int frames = output.getNumFrames();
  for (int f = 0 ; f < frames ; ++ f ) {
    const int channels = output.getNumChannels();
    for( int ch = 0 ; ch < channels ; ++ ch ){
      output[f * channels + ch ] = audiodata[f * channels + ch ];
    }
  }
  return;
}
```

これで同じ処理になる。



## `getPixels()` の仕様変更

{% capture text %}

これに対して注意すべき点として `getPixels()` の挙動が変わりました.

0.8.4までは `getPixels()` は `unsigned char *` 要するに生のピクセル配列を返してましたが
今後は `ofPixels &` を返す様になります. 今までの `getPixels()` と同様の挙動をさせるためには
`getPixels().getData()` とします.

ちなみに, `ofPixels::getData()` も0.8.4までは `ofPixels::getPixels()` から変更になっています.

ちなみにちなみに, `ofPixels_<PixelType>` から `PixelType *` へのキャスト演算子もdeprecatedになっています.

{% endcapture %}
{% assign text=text | markdownify %}
{% capture source %}
[oF0.9.0 変更されたAPI 要点まとめ - Qiita](https://qiita.com/2bbb/items/13f2e20760ec61e3ec89)
{% endcapture %}
{% assign source=source | markdownify | remove: '<p>' | remove: '</p>' %}
{% include cite.html text=text source=source %}

`ofPixels` クラスに変更されているので、いろいろ簡単になっている。例えば

``` cpp
ofImage image;
...

ofPixels pixels = image.getPixels();

pixels.getWidth(); // same value of image.getWidth()
```

と、幅や高さを持っていたり、


``` cpp
  unsigned char *pixels = image.getPixels();
  int pixel_w = image.width * 3;
  int pixel_h = image.height;

  int r, g, b;

  for (int x = 0 ; x < image.width ; ++ x) {
    for (int y = 0 ; y < image.height ; ++ y) {

      r = pixels[y * pixel_w + x * 3    ];
      g = pixels[y * pixel_w + x * 3 + 1];
      b = pixels[y * pixel_w + x * 3 + 2];
    }
  }
```

みたいなレガシーなコードは

``` cpp
  ofPixels pixels = image.getPixels();

  const int pixel_w  = pixels.getWidth ();
  const int pixel_h  = pixels.getHeight();

  int r, g, b;

  for (int x = 0 ; x < pixel_w ; ++ x) {
    for (int y = 0 ; y < pixel_h ; ++ y) {

      const ofColor color = pixels.getColor(x, y);

      r = color.r;
      g = color.g;
      b = color.b;

    }
  }
```

みたいに書き直せる。


## `getWidth()` 、 `getHeight()` を使おう

上記レガシーなコードの `ofImage.width` などが protected なメンバになっているため、
各種変更すると問題が発生するメンバ変数を取得するにはアクセサを利用する。

``` cpp
ofImage image;
...
int w = ofImage.width;
int h = ofImage.height;
```

は

``` cpp
ofImage image;
...
int w = ofImage.getWidth ();
int h = ofImage.getHeight();
```

に置き換える。
