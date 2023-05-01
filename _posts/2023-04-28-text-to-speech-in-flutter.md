---
layout: post
title:  "Text to Speech in Flutter"
date:   2023-04-26 11:22:58 +0800
categories: flutter
image: /assets/images/text_to_speech_image.png
tags: [flutter]
---


In this blog post, I will show you how to use TTS(Text To Speech) feature in Flutter. There are many application for this, some is when reading documents files. When you are tired of reading, you just want to listen. You can apply this technology.

To implement the TTS feature in Flutter, I will use the flutter_tts package. 

#### **Setup the project**

Let’s get started by creating our Flutter project. Open and run the command below in your terminal to create and generate a Flutter project.

{% highlight ruby %}
flutter create text_to_speech_app
{% endhighlight %}

Then navigate to the project folder,
{% highlight ruby %}
cd text_to_speech_app
{% endhighlight %}


#### **Add the dependencies**

Add the dependencies inside the `pubspec.yaml` file.
The contents inside the file should look like this:

{% highlight ruby %}
dependencies:
 flutter:
   sdk: flutter
 cupertino_icons: ^1.0.2
 flutter_tts: ^3.5.1

{% endhighlight %}

- `flutter_tts` is the dependency for converting the text to speech
- The rest are just default dependencies


#### **Enable permission**

For the plugin to record audio, you need to give permission for both the IOS and Android platform. For this, you can update the platform-specific files.


**For IOS**

Just set iOS minimum version: 7.0

**For Android**

Head over to the `android/app/src/main/AndroidManifest.xml` file and paste the code below before the `<application` tag.

{% highlight ruby %}
package="com.example.speech_to_text_app">
  <queries>
  <intent>
        <action android:name="android.intent.action.TTS_SERVICE" />
  </intent>
  </queries>
  <application
       android:label="speech_to_text_app"
       android:name="${applicationName}"
       android:icon="@mipmap/ic_launcher">

{% endhighlight %}

- Applications targeting SDK 30 (Android 11) need to declare `TextToSpeech.Engine.INTENT_ACTION_TTS_SERVICE` in the queries elements of Android Manifest 



#### **Set minimum SDK on Android**

Finally, head over to the `android/app/build.gradle` file under the defaultConfig object and change the minSdkVersion to **21**. The `speech_to_text` plugin requires at least Android SDK `21` because some of the speech functions in Android were only introduced in that version.

{% highlight ruby %}
   defaultConfig {
       // TODO: Specify your own unique Application ID (https://developer.android.com/studio/build/application-id.html).
       applicationId "com.example.speech_to_text_app"
       // You can update the following values to match your application needs.
       // For more information, see: https://docs.flutter.dev/deployment/android#reviewing-the-gradle-build-configuration.
       minSdkVersion 21 
       targetSdkVersion flutter.targetSdkVersion
       versionCode flutterVersionCode.toInteger()
       versionName flutterVersionName
   }
{% endhighlight %}


#### **Create the UI**

Head over to the `main.dart` and delete the entire default code first. Now, import the dependencies.

{% highlight ruby %}
import 'package:flutter_tts/flutter_tts.dart';
{% endhighlight %}

Add the `MyApp class` and in the override function `build`, return a MaterialApp with home: `SpeakScreen()`.

{% highlight ruby %}

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      home: SpeakScreen(),
    );
  }
}

{% endhighlight %}


Create the Stateful Widget **SpeakScreen Class**.

{% highlight ruby %}

class SpeakScreen extends StatefulWidget {
  const SpeakScreen({super.key});

  @override
  _SpeakScreenState createState() => _SpeakScreenState();
}

class _SpeakScreenState extends State<SpeakScreen> {}

{% endhighlight %}

Inside the **_SpeakScreenState**, add the important variables.

{% highlight ruby %}
FlutterTts ftts = FlutterTts();
{% endhighlight %}

Now, override the `build` function


{% highlight ruby %}
    @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          title: const Text("Text to Speech in Flutter"),
          backgroundColor: Colors.redAccent,
        ),
        body: Container(
          padding: const EdgeInsets.all(20),
          alignment: Alignment.center,
          child: Column(
            children: [
              ElevatedButton(
                  onPressed: () async {
                    //your custom configuration
                    await ftts.setLanguage("en-US");
                    await ftts.setSpeechRate(0.5); //speed of speech
                    await ftts.setVolume(1.0); //volume of speech
                    await ftts.setPitch(1); //pitc of sound

                    //play text to sp
                    var result = await ftts.speak("Hello World, this is Flutter Campus.");
                    if (result == 1) {
                      //speaking
                    } else {
                      //not speaking
                    }
                  },
                  child: const Text("Text to Speech"))
            ],
          ),
        ));
  }

{% endhighlight %}

- `await ftts.setLanguage("en-US")` - will set the FlutterTts objects local
- `await ftts.setSpeechRate(0.5)` - will set the speed of speech
- `await ftts.setVolume(1.0)` - will set the volume of speech
- `await ftts.setPitch(1)` - will set the pitch of sound
- `await ftts.speak("Hello World, this is Flutter Campus.")` - is the command to speak the text parameter.

So in all, here is the complete code.

{% highlight ruby %}
import 'package:flutter/material.dart';
import 'package:flutter_tts/flutter_tts.dart';

void main(){
  runApp(const MyApp());
}

class MyApp extends StatelessWidget{
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      home: Home(),
    );
  }
}

class Home extends StatefulWidget{
  const Home({super.key});

  @override
  _HomeState createState() => _HomeState();
}

class _HomeState extends State<Home> { 
  FlutterTts ftts = FlutterTts();

  @override
  Widget build(BuildContext context) { 
    return  Scaffold(
          appBar: AppBar(
            title: const Text("Text to Speech in Flutter"),
            backgroundColor: Colors.redAccent,
          ),
          body: Container( 
            padding: const EdgeInsets.all(20),
            alignment: Alignment.center,
            child: Column(
                children: [
                    ElevatedButton(
                      onPressed:() async {

                          //your custom configuration
                          await ftts.setLanguage("en-US");
                          await ftts.setSpeechRate(0.5); //speed of speech
                          await ftts.setVolume(1.0); //volume of speech
                          await ftts.setPitch(1); //pitc of sound

                          //play text to sp
                          var result = await ftts.speak("Hello World, this is Flutter Campus.");
                          if(result == 1){
                              //speaking
                          }else{
                              //not speaking
                          }
                      }, 
                      child: const Text("Text to Speech"))
                ],
            ),
          )
       );
  }
}

{% endhighlight %}

#### **Resources**
- [Source Code][source-code]
- [speech_to_text_package][text-to-speech-package]

[source-code]: https://drive.google.com/file/d/18jhvBnboy_0QubL970m4ShuhlWTFGzv7/view?usp=sharing
[text-to-speech-package]: https://pub.dev/packages/text_to_speech

#### **Conclusion**
In this vlog, you were able to create an app the convert speech to text. An app that will listen to the voice, analyze it and convert it to text.
Imagine the wide application you can apply this technology. Because of this, I leave you this quote. "Keep on dreaming and your dream will always be dreamed."