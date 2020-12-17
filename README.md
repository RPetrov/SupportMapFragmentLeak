## SupportMapFragmentLeak
Demonstration memory leak in Google SupportMapFragment using leakcanary


Read this first: https://square.github.io/leakcanary/faq/#can-a-leak-be-caused-by-the-android-sdk

### LeakTrace information
Steps to reproduce:
1. Create application from template (for example "basic activity" with 2 fragments: A -> B)
2. Add dependency:
```
dependencies {
    implementation "com.google.android.gms:play-services-maps:16.1.0"
    debugImplementation 'com.squareup.leakcanary:leakcanary-android:2.5'
...
}
```
3. Add this to layout of Fragment A:
```
    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/map"
        android:name="com.google.android.gms.maps.SupportMapFragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
```
4. And you have to add API_KEY to Minifest:

```
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.SupportMapFragmentLeak">

        <meta-data android:name="com.google.android.geo.API_KEY" android:value="INVALID_KEY"/>

        <activity
...
        </activity>
    </application>
```

5. Open app. Press next and back (A -> B -> A->B->A and so on)
onDestroyView will be called in Fragment A, but anyway we have memory leak detection.


```
 ====================================
HEAP ANALYSIS RESULT
====================================
1 APPLICATION LEAKS
References underlined with "~~~" are likely causes.
Learn more at https://squ.re/leaks.
353960 bytes retained by leaking objects
Displaying only 1 leak trace out of 4 with the same signature
Signature: 44ae215069555df79943f5d818356a2fb95fce68
┬───
│ GC Root: Local variable in native code
│
├─ com.google.maps.api.android.lib6.gmm6.store.m instance
│    Leaking: UNKNOWN
│    Retaining 8188 bytes in 66 objects
│    Thread name: 'androidmapsapi-its'
│    ↓ m.c
│        ~
├─ com.google.maps.api.android.lib6.drd.r instance
│    Leaking: UNKNOWN
│    Retaining 926468 bytes in 6431 objects
│    ↓ r.d
│        ~
├─ java.util.concurrent.CopyOnWriteArrayList instance
│    Leaking: UNKNOWN
│    Retaining 294 bytes in 13 objects
│    ↓ CopyOnWriteArrayList.array
│                           ~~~~~
├─ java.lang.Object[] array
│    Leaking: UNKNOWN
│    Retaining 270 bytes in 11 objects
│    ↓ Object[].[1]
│               ~~~
├─ com.google.maps.api.android.lib6.gmm6.util.n instance
│    Leaking: UNKNOWN
│    Retaining 20 bytes in 1 objects
│    ↓ n.c
│        ~
├─ com.google.maps.api.android.lib6.gmm6.util.m instance
│    Leaking: UNKNOWN
│    Retaining 662180 bytes in 2154 objects
│    ↓ m.e
│        ~
├─ java.util.HashSet instance
│    Leaking: UNKNOWN
│    Retaining 662155 bytes in 2153 objects
│    ↓ HashSet.map
│              ~~~
├─ java.util.HashMap instance
│    Leaking: UNKNOWN
│    Retaining 662143 bytes in 2152 objects
│    ↓ HashMap.table
│              ~~~~~
├─ java.util.HashMap$Node[] array
│    Leaking: UNKNOWN
│    Retaining 662103 bytes in 2151 objects
│    ↓ HashMap$Node[].[0]
│                     ~~~
├─ java.util.HashMap$Node instance
│    Leaking: UNKNOWN
│    Retaining 662039 bytes in 2150 objects
│    ↓ HashMap$Node.key
│                   ~~~
├─ com.google.maps.api.android.lib6.gmm6.vector.bx instance
│    Leaking: UNKNOWN
│    Retaining 662015 bytes in 2149 objects
│    ↓ bx.y
│         ~
├─ com.google.maps.api.android.lib6.gmm6.vector.n instance
│    Leaking: UNKNOWN
│    Retaining 52 bytes in 1 objects
│    ↓ n.k
│        ~
├─ com.google.maps.api.android.lib6.gmm6.api.y instance
│    Leaking: UNKNOWN
│    Retaining 1988 bytes in 50 objects
│    View not part of a window view hierarchy
│    View.mAttachInfo is null (view detached)
│    View.mWindowAttachCount = 1
│    mContext instance of android.app.Application
│    ↓ y.mParent
│        ~~~~~~~
├─ android.widget.FrameLayout instance
│    Leaking: UNKNOWN
│    Retaining 89383 bytes in 1315 objects
│    View not part of a window view hierarchy
│    View.mAttachInfo is null (view detached)
│    View.mWindowAttachCount = 1
│    mContext instance of android.app.Application
│    ↓ FrameLayout.mParent
│                  ~~~~~~~
╰→ android.widget.FrameLayout instance
​     Leaking: YES (ObjectWatcher was watching this because com.google.android.gms.maps.SupportMapFragment received
​     Fragment#onDestroyView() callback (references to its views should be cleared to prevent leaks))
​     Retaining 88486 bytes in 1304 objects
​     key = 5f7835e7-05f8-4528-802c-ff6c5582e96c
​     watchDurationMillis = 9902
​     retainedDurationMillis = 4901
​     View not part of a window view hierarchy
​     View.mAttachInfo is null (view detached)
​     View.mWindowAttachCount = 1
​     mContext instance of com.example.supportmapfragmentleak.MainActivity with mDestroyed = false
====================================
0 LIBRARY LEAKS
A Library Leak is a leak caused by a known bug in 3rd party code that you do not have control over.
See https://square.github.io/leakcanary/fundamentals-how-leakcanary-works/#4-categorizing-leaks
====================================
METADATA
Please include this in bug reports and Stack Overflow questions.
Build.VERSION.SDK_INT: 29
Build.MANUFACTURER: Google
LeakCanary version: 2.5
App process name: com.example.supportmapfragmentleak
Stats: LruCache[maxSize=3000,hits=4817,misses=58320,hitRate=7%]
RandomAccess[bytes=2968844,reads=58320,travel=16165406106,range=15960284,size=20294396]
Analysis duration: 4144 ms
Heap dump file path: /data/user/0/com.example.supportmapfragmentleak/files/leakcanary/2020-12-17_18-44-22_357.hprof
Heap dump timestamp: 1608219870902
Heap dump duration: 3831 ms
====================================


```
