스플래시 화면에 관해 기존 공식에서 제공하는 api를 사용하는 것에서 생긴 문제점:
- 아이콘이 일정 크기를 넘어가면 다 잘려서 나오는데 그 크기가 너무 작아서 원하는 결과를 담기가 힘듦
-  배경이 단색만 가능
-  애니메이션이 복잡하고 아이콘이 여러개일 때 코드가 너무 복잡해지고 원하는 기능 구현이 잘 안됨(xml)


<img width="270" height="606" alt="image" src="https://github.com/user-attachments/assets/65126abe-86d7-4b4f-a4ee-0b112f850cb8" />|<img width="440" height="392" alt="스크린샷 2026-04-24 오전 12 20 42" src="https://github.com/user-attachments/assets/77bcc692-4926-4786-b313-20b7261535f1" />
|:---:|:---:|



### &rarr; 그래서 기존에 나오는 스플래시 화면(빈화면으로)을 아주 짧게 설정한 다음 원하는 화면을 kotlin으로 구현하여 스플래시 화면으로 사용하는 방법을 선택

> [xml](https://velog.io/@mraz3068/How-to-make-Custom-Splash-Screen-with-Splash-Screen-API)
> [도형 그리기](https://developer.android.com/develop/ui/compose/graphics/draw/shapes?hl=ko)

1. 원하는 스플래시 화면이 나오기 전에 짧게 빈 화면(하얀색)을 화면에 출력
    따로 설정하지 않으면 기본 아이콘이 정가운데에 표시되는 화면을 스플래시 화면으로 출력되기 때문
   ic_dummy.xml(생성 후 themes.xml의 아이콘으로 지정, drawable)
   |:---:|
   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <vector xmlns:android="http://schemas.android.com/apk/res/android"
      android:width="108dp"
      android:height="108dp"
      android:viewportHeight="192"
      android:viewportWidth="192">

      <path
          android:fillColor="@color/white"
          android:pathData="M0,0h1200v108h-108z" />

   </vector>    
   ```

2. Splash 화면을 따로 작성 후 MainActivity에 연결
3. themes.xml에 시간 조절 로직 추가
    ```xml
    <!-- 스플래시 api 표시될 시간-->
    <item name="android:windowSplashScreenAnimationDuration" tools:targetApi="31">1</item>
    ```

!근데 빈 화면이 너무 눈에 띔....
Multi-Activity로 해봤는데 똑같음. 다른 앱도 확인해보니깐 다 똑같았음
