카카오링크 API
==============

카카오링크 API는 외부 앱이나 모바일 웹페이지에서 카카오톡으로 링크를 보낼 수 있고, 커스텀스킴방식으로 구현되어 어느 앱에서나 간단하게 적용할 수 있습니다.

### URL 링크 전달

외부 앱, 모바일웹에서 카카오톡 친구들에게 URL 링크 혹은 메세지(TEXT)를 전송할 수 있습니다.

* 지원 OS: iOS, Android, 모바일웹, Blackberry 추후 지원 예정

### App 링크 전달

외부 앱, 모바일웹에서 카카오톡 친구들에게 해당 앱으로 바로 연결 할수 있는 링크를 전송할 수 있습니다. 링크를 받는 사람이 해당 앱을 설치하지 않은 경우 설치마켓으로 연결 할 수 있으며, 호환되지 않는 OS의 경우 URL 링크로 대체하여 전달할 수 있습니다.

* 지원 OS: iOS, Android, 모바일웹, Blackberry 추후 지원 예정
* 지원 설치마켓: Google play(Android market), App store




안드로이드폰
------------

Android에서는 Intent and IntentFilter를 사용하여 카카오링크를 호출합니다. <br />
전달하려는 링크의 종류에 따라 URL 링크와 App 링크로 구분됩니다.

URL 링크 전달
-------------

Custom URL Scheme

    kakaolink://sendurl?msg=[message]&url=[url]
    &appid=[appid]&appver=[appver]&type=[type]&appname=[appname]&apiver=[apiver]

파라미터 설명

    msg (String)      : 유저에게 전달될 메세지 내용(UTF-8)
    url (String)      : 유저에게 전달될 메세지에 포함되는 링크 url(모바일웹)
    appid (String)    : App의 bundle id
    appver (String)   : 3rd party app의 버전
    appname (String)  : 3rd party app의 정확한 이름
    type (String)     : 카카오링크 타입(link, 고정값)
    apiver (String)   : 카카오링크 API 버전(2.0, 고정값)
    
### 사용 예

> * type, apiver 파라미터는 `KakaoLink.java`에서 처리합니다.

```java
// Recommended: Use application context for parameter.
KakaoLink kakaoLink = KakaoLink.getLink(getApplicationContext());

// check, intent is available.
if (!kakaoLink.isAvailableIntent())
  return;

/**
 * @param activity
 * @param url
 * @param message
 * @param appId
 * @param appVer
 * @param appName
 * @param encoding
 */
kakaoLink.openKakaoLink(this, 
		"http://link.kakao.com/?test-android-app", 
		"First KakaoLink Message for send url.", 
		getPackageName(), 
		getPackageManager().getPackageInfo(getPackageName(), 0).versionName, 
		"KakaoLink Test App", 
		"UTF-8");
```

APP 링크 전달
-------------

Custom URL Scheme

    kakaolink://sendurl?msg=[message]&url=[url]
    &appid=[appid]&appver=[appver]&type=[type]&appname=[appname]&apiver=[apiver]&metainfo=[metainfo]


파라미터 설명

    msg (String)      : 유저에게 전달될 메세지 내용(UTF-8)
    url (String)      : 유저에게 전달될 메세지에 포함되는 링크 url(모바일웹)
    appid (String)    : App의 bundle id
    appver (String)   : 3rd party app의 버전메ㅔ
    appname (String)  : 3rd party app의 정확한 이름
    type (String)     : 카카오링크 타입(app, 고정값)
    apiver (String)   : 카카오링크 API 버전(2.0, 고정값)
    metainfo (String) : 3rd party app을 구동시키기 위한 meta 정보
                        (JSONObject의 String Array 형식으로 지원)

### 사용 예

> * APP 링크 전달을 사용할 때에는 meta 정보를 Android, iOS 모두 만들어야 합니다.
> * `AndroidManifest.xml`에 APP 링크를 통해 실행될 수 있도록 custom scheme이 추가되어야 합니다.
> * type, apiver 파라미터는 `KakaoLink.java`에서 처리합니다.

```java
ArrayList<Map<String, String>> metaInfoArray = new ArrayList<Map<String, String>>();

// If application is support Android platform.
Map<String, String> metaInfoAndroid = new Hashtable<String, String>(1);
metaInfoAndroid.put("os", "android");
metaInfoAndroid.put("devicetype", "phone");
metaInfoAndroid.put("installurl", "market://details?id=com.kakao.talk");
metaInfoAndroid.put("executeurl", "kakaoLinkTest://startActivity");

// If application is support ios platform.
Map<String, String> metaInfoIOS = new Hashtable<String, String>(1);
metaInfoIOS.put("os", "ios");
metaInfoIOS.put("devicetype", "phone");
metaInfoIOS.put("installurl", "your iOS app install url");
metaInfoIOS.put("executeurl", "kakaoLinkTest://startActivity");

// add to array
metaInfoArray.add(metaInfoAndroid);
metaInfoArray.add(metaInfoIOS);

// Recommended: Use application context for parameter. 
KakaoLink kakaoLink = KakaoLink.getLink(getApplicationContext());

// check, intent is available.
if(!kakaoLink.isAvailableIntent()) 
  return;

/**
 * @param activity
 * @param url
 * @param message
 * @param appId
 * @param appVer
 * @param appName
 * @param encoding
 * @param metaInfoArray
 */
kakaoLink.openKakaoAppLink(
		this, 
		"http://link.kakao.com/?test-android-app", 
		"First KakaoLink Message for send app data.",  
		getPackageName(), 
		getPackageManager().getPackageInfo(getPackageName(), 0).versionName,
		"KakaoLink Test App",
		"UTF-8", 
		metaInfoArray);
```

```xml
<!-- AndroidManifest.xml -->
<activity android:name=".MainActivity" >
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
    <!-- custom scheme(execute url) -->
    <intent-filter>
        <data android:scheme="kakaoLinkTest" android:host="startActivity" />
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.BROWSABLE" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

안드로이드 OS 공유기능 활용
---------------------------

위 방법 외에도 Android에서 제공하는 공유 기능을 활용하여 텍스트, 이미지 및 동영상을 전송할 수 있습니다. <br />
구체적인 정의는 [http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND)를 참조하세요.

### 사용 예

```java
// Sending Text  
Intent intent = new Intent(Intent.ACTION_SEND);
intent.setType("text/plain");
intent.putExtra(Intent.EXTRA_SUBJECT, "Subject");
intent.putExtra(Intent.EXTRA_TEXT, "Text");

// Sending Images
Intent intent = new Intent(Intent.ACTION_SEND);
intent.setType("image/png");
intent.putExtra(Intent.EXTRA_STREAM, Uri.parse(path));

// Sending Video types
Intent intent = new Intent(Intent.ACTION_SEND);
intent.setType("video/3gpp");
intent.putExtra(Intent.EXTRA_STREAM, Uri.parse(path));

// Add the following code if you wish to send directly to KakaoTalk
intent.setPackage("com.kakao.talk");
```

