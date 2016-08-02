#Velocee SDK

Velocee InstantRadio SDK transforms any mobile app into a personalized mobile radio station.


##SDK Integration

###1. Maven AAR

Velocee SDK is available on our Maven repository
To use it just add the following to your build.gradle file.

    repositories {
    mavenCentral()
        maven {
            url "https://s3.amazonaws.com/velocee-maven"
        }
    }

    dependencies {
        compile fileTree(dir: 'libs', include: ['*.jar'])
        compile 'com.android.support:appcompat-v7:23.4.0'
        compile 'com.velocee:veloceeSDK:0.2.3'
        compile 'com.velocee:audioplayer:0.2.5'
    }



###2. Zip Download Project Integration

Download vlc-android-sdk as a zip file and extract it. 
Copy the veloceeSDK.aar and veloceeSDK_Audioplayer.aar to the project libs folder.

Add to the application gradle file in the dependency section:

    dependencies { 
    	compile (name:'veloceeSDK', ext:'aar')
    	compile (name:'veloceeSDK_Audioplayer', ext:'aar')
    }
    repositories {
        flatDir { dirs 'libs' }
    }

optional link against veloceeSDK_Audioplayer.aar as well in the same manner

The SDK uses the following third party libraries 
GSON
https://sites.google.com/site/gson/gson-user-guide
JS
https://developer.mozilla.org/en-US/docs/Mozilla/Projects/Rhino
OkHTTP
http://square.github.io/okhttp/
Retrofit
http://square.github.io/retrofit/
Apache Commons Lang
https://commons.apache.org/proper/commons-lang/
AdsWiz SDK
http://www.adswizz.com

    Add dependencies to your application gradle file:
    dependencies {
        compile 'com.android.support:appcompat-v7:23.4.0’
        compile 'com.google.code.gson:gson:2.3'
        compile 'com.google.android.gms:play-services-location:9.2.1'
        compile 'com.squareup.retrofit:retrofit:1.9.0'
        compile 'com.squareup.okio:okio:1.4.0'
        compile 'com.squareup.okhttp:okhttp:2.4.0'
        compile 'org.apache.commons:commons-lang3:3.4'
        compile files('libs/adswizz-sdk.jar')
    }

##Code Integration

Usage
Get the sdk instance by calling VlcSdk.getObj() which returns an IVlcSDK inteface

Initializing

The library requires a one time initialization upon application startup. You should call the method VlcSdk.getObj().start preferably in your application.onCreate() method

    VlcSdk.getObj().start(getApplicationContext(),"sdk-key");


###Using Within a WebView

The Velocee SDK provides a WebView delegate for the application’s WebView to use. In order to use Velocee’s WebView delegate you should use the SDK’s setDelegate method instead of the web view one. In case the application has a webview delegate it will be chained.

    WebViewClient vlcClient = VlcSdk.getObj().setDelegate(new MyWebViewClient());
    myWebView.setWebViewClient(vlcClient);

###Add Labels

Your application data needs to be labeled in order to fine tune Velocee's recommendation algorithm. Any text can be used as a label. In order to set the current label use the setTag method – void setTag(String tag) tag. Once a label is set it affects all resources until a new label is set or the application is idle for a period of time.

##InstantRadio Integration

The Velocee InstantRadio module plays user adapted audio content to the user.

###InstantRadio Default Player User Interface

The SDK includes a default InstantRadio player user interface intended for use during the trial period of the SDK or if not customization required. This UI can be replaced by a customer designed UI if required later on.
When using the bundled UI the SDK handles the audio actions & events and there's no need for additional code other than starting the audio.
When using the default player interface users should call the following api call to start the intent:

    void startAudioExt(String source, String siteurl);

Example:

    String *sourceName = @”test app”; 
    String* siteUrl = @”http://testapp.com; 
    VlcSdk.getObj().startAudioExt(sourceName,  siteUrl);

This call will show the player UI and automatically start playing.
When users provide their own UI they should take care of showing the audio UI and handle relevant actions and events, as described bellow.

##Instant Radio Custom Player Integration

###Playback events

In order to be notified when a new playlist is dowloaded (so it may be played), call registerPlaybackEvents(IPlaybackEvents rcv)
supplying an instance implementing OnPlaylistLoaded(List<String> trackNames)

When the event fires, play() may be called in order for the playlist to start playing sequentially

###Interfaces

IVlcPlaybackEvents interface definition of callback invoked when the player loads a new playlist or a new media file started playing. use audioRegisterPlaybackEvents/audioUnregisterPlaybackEvents methods to register for playback notifications

    void OnTrackStart(String title, String author, int lengthInMillis)

A new media track has started playing

    void OnPlaylistLoaded(List<String> trackNames)

A new playlist has downloaded listing its track titles

###Public methods

    void startAudio()

Opens a sample Velocee branded audio player activity

    void audioPlay()

Starts / resumes playback

    void audioPrev()

Starts playback of the previous track in the playlist

    void audioNext()

Starts playback of the next track in the playlist

    void audioPause()

Pauses playback

    void audioPlayAtIndex(int index)

Starts playback of a specific track 

    void startAudio();

Opens velocee sample audio player screen

    boolean audioRegisterPlaybackEvents(IVlcPlaybackEvents rcv)
    void     audioUnregisterPlaybackEvents(IVlcPlaybackEvents rcv)

methods for (un)registering for playback events

    void audioReleasePlayer()

Releases resources associated with media player object

    boolean      audioIsPlaying()

Checks whether the media player is playing

    int     audioGetProgress()

Gets the current playback position in millisecods


    VlcMediaInfo  audioGetMediaInfo()

returns track information. also see VlcMediaInfo definition

###internal Classes

VlcMediaInfo basic track information
String mTitle track title
String mAuthor track author
int mLengthInMillis track duration in milliseconds
String mPlaylistUrl track’s played is part of the given playlist URL

##Caching API Additions

###Getting Cached items From Within a Native View

In order to use Velocee’s cache from non-webview view the application must change the relevant image & video urls to serve the content locally. The SDK provides several conversion methods for convenience:

     String getCachedResourceUrl(String originalUrl)

 receives the URL (as string) and returns the resource-from-cache URL.

    URL getCachedResourceUrlUrl(URL originalUrl)

is similar to the above but receives and returns a URL instead of string. Both methods returns the input unchanged in case the input URL cannot be found in the cache.

    Bitmap getCachedResourceData(String originalUrl)

the URL (as string) and returns an image bitmap info in case the resource can be found in cache, null otherwise

