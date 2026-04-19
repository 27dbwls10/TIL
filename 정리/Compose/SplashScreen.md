[splash android 12](https://developer.android.com/develop/ui/views/launch/splash-screen/migrate?hl=ko)
[splash 화면 커스텀](https://developer.android.com/develop/ui/views/launch/splash-screen?hl=ko&_gl=1*12rn69s*_up*MQ..*_ga*MTAzMjE3ODA2Mi4xNzc2NTc4OTA1*_ga_6HH9YJMN9M*czE3NzY1Nzg5MDUkbzEkZzAkdDE3NzY1Nzg5MDUkajYwJGwwJGg1NTU5MDA5NDY.#customize-animation)

!Process Init, Activity.onCreate에서만 실행, Activity.onStart에서는 실행 X

1. app 수준 gradle에 의존성 추가
  ```kotlin
  dependencies {
    implementation("androidx.core:core-splashscreen:1.2.0")
  }
  ```
2. themes.xml에 splash 테마 추가
  ```xml
  // 아이콘 아래에 배경색을 추가하고 싶으면 Theme.SplashScreen.IconBackground 테마 사용
  // -> windowSplashScreenIconBackground 속성 설정
  <style name="Theme.App.Starting" parent="Theme.SplashScreen">
    <!-- Set the splash screen background, animated icon, and animation
    duration. -->
    <item name="windowSplashScreenBackground">@color/...</item>

    <!-- Use windowSplashScreenAnimatedIcon to add a drawable or an animated
          drawable. One of these is required. -->
    <item name="windowSplashScreenAnimatedIcon">@drawable/...</item>
    <!-- Required for animated icons. -->
    <item name="windowSplashScreenAnimationDuration">200</item>
    
    <!-- Set the theme of the Activity that directly follows your splash
    screen. This is required. -->
    <item name="postSplashScreenTheme">@style/Theme.App</item>
  </style>
  ```
3. AndroidManifest.xml에서 시작활동 테마를 변경
  ```xml
  <manifest>
     <application android:theme="@style/Theme.App.Starting">
      <!-- or -->
          <activity android:theme="@style/Theme.App.Starting">
  ...
  ```

4. MainActivity.kt에서 `super.onCreate` 호출하기 전에 `installSplashScreen` 호출
  ```kotlin
  class MainActivity : Activity() {
  
     override fun onCreate(savedInstanceState: Bundle?) {
         // Handle the splash screen transition.
         val splashScreen = installSplashScreen()
  
         super.onCreate(savedInstanceState)
         setContentView(R.layout.main_activity)
  ...
  ```

5. 만약 이전 스플래시 화면이 라우팅된다면 이전 스플래시 화면 삭제 (실제 Activity에 직접 연결 or 하위 구성요소가 있는 단일 활동으로 이동)
  ```kotlin
  class RoutingActivity : Activity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        val splashScreen = installSplashScreen()
        super.onCreate(savedInstanceState)

        // Keep the splash screen visible for this Activity.
        splashScreen.setKeepOnScreenCondition { true }
        startSomeNextActivity()
        finish()
     }
   ...
  ```

6. 스플래시 화면 커스텀 (`Activity.getSplashScreen()` 사용)
  ```kotlin
  override fun onCreate(savedInstanceState: Bundle?) {
      super.onCreate(savedInstanceState)
      // ...
  
      // Add a callback that's called when the splash screen is animating to the
      // app content.
      splashScreen.setOnExitAnimationListener { splashScreenView ->
          // Create your custom animation.
          val slideUp = ObjectAnimator.ofFloat(
              splashScreenView,
              View.TRANSLATION_Y,
              0f,
              -splashScreenView.height.toFloat()
          )
          slideUp.interpolator = AnticipateInterpolator()
          slideUp.duration = 200L
  
          // Call SplashScreenView.remove at the end of your custom animation.
          slideUp.doOnEnd { splashScreenView.remove() }
  
          // Run your animation.
          slideUp.start()
      }
  }
  ```

  ```kotlin
  // 남은 시간 계산
  // Get the duration of the animated vector drawable.
  val animationDuration = splashScreenView.iconAnimationDuration
  // Get the start time of the animation.
  val animationStart = splashScreenView.iconAnimationStart
  // Calculate the remaining duration of the animation.
  val remainingDuration = if (animationDuration != null && animationStart != null) {
      (animationDuration - Duration.between(animationStart, Instant.now()))
          .toMillis()
          .coerceAtLeast(0L)
  } else {
      0L
  }
  ```

