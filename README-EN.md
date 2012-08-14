KakaoLink API
=============

KakaoLink API can be used to send links from external apps or mobile web pages to KakaoTalk. <br />
KakaoLink can be easily applied to any app because it uses custom schemes.

### Sending URL Links

Users can send URL links or messages (TEXT) to KakaoTalk friends from external apps or mobile web pages.

* Supporting OS:iOS、Android、mobile web (BlackBerry will be supported in the future)

### Sending App Links

Users can send links to KakaoTalk friends from external apps or mobile web pages, and the KakaoTalk friends can use the link to directly connect to these external apps. If the friend does not install the app, then they can connect to the app market. If the friend use an incompatible OS, then URL links can be sent instead.

* Supporting OS:iOS、Android、mobile web (BlackBerry will be supported in the future) 
* Supported app market : Google Play、App store




Android
-------

Intents and Intent Filters in Android is used to call KakaoLink.<br />
Links that are to be sent are classified into URL links and App links, depending on the type of link. <br />
Visit GitHub’s [Kakaolink-android project](https://github.com/kakao/kakaolink-android/) for the latest version of the library and sample app.

Sending URL Links
-----------------

Custom URL Scheme

    kakaolink://sendurl?msg=[message]&url=[url]
    &appid=[appid]&appver=[appver]&type=[type]&appname=[appname]&apiver=[apiver]

Parameter Description

    msg (String)      : Message that is to be sent to the user (UTF-8)
    url (String)      : URL (mobile web) of link included in the message that is to be sent to the user
    appid (String)    : App's bundle id
    appver (String)   : 3rd App's version
    appname (String)  : 3rd App's accurate name
    type (String)     : KakaoLink type(link, fixed value)
    apiver (String)   : KakaoLink API version(2.0, fixed value)
    
### Example 

> * type, apiver parameter are processed in `KakaoLink.java`

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

Sending App Links
-----------------

Custom URL Scheme

    kakaolink://sendurl?msg=[message]&url=[url]
    &appid=[appid]&appver=[appver]&type=[type]&appname=[appname]&apiver=[apiver]&metainfo=[metainfo]


Parameter Description

    msg (String)      : Message that is to be sent to the user (UTF-8)
    url (String)      : URL (mobile web) of link included in the message that is to be sent to the user
    appid (String)    : App's bundle id
    appver (String)   : 3rd App's version
    appname (String)  : 3rd App's accurate name
    type (String)     : KakaoLink type(link, fixed value)
    apiver (String)   : KakaoLink API version(2.0, fixed value)
    metainfo (String) : Meta information used to run the 3rd app
                        (Supported in the JSON Object's String Array format)

### Example 

> * To send APP link, meta data should be prepared for both iOS and Android. 
> * To run with APP link, custom scheme should be added to `AndroidManifest.xml`
> * type, apiver parameter are processed in `KakaoLink.java`

```java
ArrayList<Map<String, String>> metaInfoArray = new ArrayList<Map<String, String>>();

// If application is support Android platform.
Map<String, String> metaInfoAndroid = new Hashtable<String, String>(1);
metaInfoAndroid.put("os", "android");
metaInfoAndroid.put("devicetype", "phone");
metaInfoAndroid.put("installurl", "market://details?id=com.kakao.talk");
metaInfoAndroid.put("executeurl", "kakaoLinkTest://starActivity");

// If application is support ios platform.
Map<String, String> metaInfoIOS = new Hashtable<String, String>(1);
metaInfoIOS.put("os", "ios");
metaInfoIOS.put("devicetype", "phone");
metaInfoIOS.put("installurl", "your iOS app install url");
metaInfoIOS.put("executeurl", "kakaoLinkTest://starActivity");

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
        <data android:scheme="kakaoLinkTest" android:host="starActivity" />
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.BROWSABLE" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

Using Android OS Share Function
-------------------------------

The share function provided in Android can also be used to send text, image or videos.<br />
Visit [http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND) for a look at detailed definitions.

### Example

```java
// send Text  
Intent intent = new Intent(Intent.ACTION_SEND);
intent.setType("text/plain");
intent.putExtra(Intent.EXTRA_SUBJECT, "Subject");
intent.putExtra(Intent.EXTRA_TEXT, "Text");

// send Image
Intent intent = new Intent(Intent.ACTION_SEND);
intent.setType("image/png");
intent.putExtra(Intent.EXTRA_STREAM, Uri.parse(path));

// send video
Intent intent = new Intent(Intent.ACTION_SEND);
intent.setType("video/3gpp");
intent.putExtra(Intent.EXTRA_STREAM, Uri.parse(path));

// You want to send to KakaoTalk, use this line.
intent.setPackage("com.kakao.talk");
```

KakaoTalk B.I download for Developers
-------------------------------------

### [Kakaotalk_BI.zip](http://alpha.kakao.com/images/v2/link/kakaotalk_icon.zip)

*B.I must only be used on buttons/pages that refers to the function that sends messages via KakaoTalk using KakaoLink. The whole BI, or portions of the BI, must not be used in pages or menus other than those defined above, since it may mislead users into believing that the app was developed by Kakao.