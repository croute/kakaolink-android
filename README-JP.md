カカオリンクAPI
===============

カカオリンクAPIは、外部アプリやモバイルWebページから
カカオトークにリンクを送ることができ、カスタムスキーム方式 で作成されているため、どのアプリでも簡単に適用することができ
ます。

### URL リンク共有

外部アプリ、モバイルウェブからカカともにURLリンクや メッセージを送信することができます。

* サポートOS : iOS、Android、モバイルウェブ(BlackBerryは今後対応予 定)

### App リンク共有

外部アプリ、モバイルウェブからカカともに、該当アプリに直接アクセス可能なリンクを送信することができます。リンクを受け取った相手側が該当アプリをインストールしていない場合は、マーケットにアクセスされ、 互換性のないOSの場合には、URLリンクに置き換えて共有することができます。

* サポートOS : iOS、Android、モバイルウェブ(BlackBerryは今後対応予定)
* サポートするインストールマーケット : Google Play、App Store 




Android
-------

Androidでは、[Intents and Intent Filters](http://developer.android.com/guide/components/intents-filters.html)を使用してカカオリンクを呼び出します。<br/>
共有先のリンクの種類によってURLリンクとAppリンクで区分されます。<br/>
ライブラリとサンプルアプリの最新バージョンは、[GitHubの kakaolink-android](https://github.com/kakao/kakaolink-android/) プロジェクトでご確認いただけます。

URL リンク共有
--------------

Custom URL Scheme

    kakaolink://sendurl?msg=[message]&url=[url]
    &appid=[appid]&appver=[appver]&type=[type]&appname=[appname]&apiver=[apiver]

パラメータ説明

    msg (String)      : ユーザーに送信されるメッセージの内容(UTF-8)
    url (String)      : ユーザーに送信されるメッセージに含まれるリンクurl(モバイルウェ ブ)
    appid (String)    : Appのbundle id
    appver (String)   : 3rd Appのバージョン カカオリンクタイプ カカオリンクAPIバージョン
    appname (String)  : 3rd Appの正確な名前
    type (String)     : カカオリンクタイプ(link, 固定値)
    apiver (String)   : カカオリンクAPIバージョン(2.0, 固定値)
    
### 使用例

> * type、apiverパラメータは「KakaoLink.java」で処理します。

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

App リンク共有
--------------

Custom URL Scheme

    kakaolink://sendurl?msg=[message]&url=[url]
    &appid=[appid]&appver=[appver]&type=[type]&appname=[appname]&apiver=[apiver]&metainfo=[metainfo]

パラメータ説明

    msg (String)      : ユーザーに送信されるメッセージの内容(UTF-8)
    url (String)      : ユーザーに送信されるメッセージに含まれるリンクurl(モバイルウェ ブ)
    appid (String)    : Appのbundle id
    appver (String)   : 3rd Appのバージョン カカオリンクタイプ カカオリンクAPIバージョン
    appname (String)  : 3rd Appの正確な名前
    type (String)     : カカオリンクタイプ(link, 固定値)
    apiver (String)   : カカオリンクAPIバージョン(2.0, 固定値)
    metainfo (String) : 3rd party app을 구동시키기 위한 meta 정보
                        (JSONObject의 String Array 형식으로 지원)

### 使用例

> * APPリンク送信を利用する際には、meta情報をAndroid、iOSすべて作成する必要があります。
> * `AndroidManifest.xml`にAPPリンクを通じて実行できるようにcustom schemeを追加する必要があります。
> * type、apiverパラメータは`KakaoLink.java`で処理します。

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

Android OS 共有機能利用
-----------------------

上記の方法以外にも、Androidが提供する共有機能を利用して、テキストや画像、動画を送ることができます。<br/>
具体的な定義は [http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND)を参照してください。

### 使用例

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

開発者向けカカオトークBIダウンロード
---------------------------------

### [Kakaotalk_BI.zip](http://alpha.kakao.com/images/v2/link/kakaotalk_icon.zip)

*B.Iはカカオリンクを使用してカカオトークにメッセージを送信する機能を説明するためのボタン/またはそれに該当するページに限って使用することができ、他のページやメニューなどで上記イメージをそのまま、または一部を変更して使用することを禁じております。 