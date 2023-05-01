---
layout: post
title:  "Speech to Text in Flutter"
date:   2023-04-26 11:22:58 +0800
categories: flutter
image: /assets/images/speech_to_text_image5.png
---


Are you tired of always typing? Whenever you want to create document, search a movie on Netflix, search a video on YouTube on your smart TV or just simply chat your friend on your phone. Don’t worry there a new Technology now to convert your speech into text. This is the Speech Recognition.

 Speech Recognition, also known as `Speech-to-Text`, is the capability of software to listen to words spoken aloud and translate them into readable text or perform actions based on specific keywords listened from the words it heard. 

This technology makes typing more faster and perform some commands more quickly since it typically base it on you speaking rather than you typing. And because speaking is faster faster than typing. This will create a friendly user experience for users.

But of course, there are still advantages and disadvantages

#### ADVANTAGE
* Talking is much faster than typing.
* You can dictate a document three times faster than you can type it.
* It allows for hands-free work.

#### DISADVANTAGE
* Background Noise, accent, and numerous speaker affect the accuracy
* It will affect you physically specially your voice when using long term
* Money, you have to invest on Speech Recognition software
* Speaking is loud and invites noise to others.

### **Topics**
- **Setup the project**
- **Add the dependencies**
- **Enable permissions**
- **Set minimum SDK on Android**
- **Create the UI**



### **Setup the project**

Let’s get started by creating our Flutter project. Open and run the command below in your terminal to create and generate a Flutter project.

{% highlight ruby %}
flutter create speech_to_text_app
{% endhighlight %}

Then navigate to the project folder,
{% highlight ruby %}
cd speech_to_text_app
{% endhighlight %}


### **Add the dependencies**

Add the dependencies inside the `pubspec.yaml` file.
The contents inside the file should look like this:

{% highlight ruby %}
dependencies:
 flutter:
   sdk: flutter
 cupertino_icons: ^1.0.2
 speech_to_text: ^6.1.1
 highlight_text: ^1.6.0
 avatar_glow: ^2.0.2

{% endhighlight %}

- `speech_to_text` is the dependency for converting the speech to text
- `highlight_text` is the dependency for highlighting a specific word that we set.
- `avatar_glow` is the dependency for making the button glow when we are listening in a speech.
- The rest are just default dependencies




### **Enable permission**

For the plugin to record audio, you need to give permission for both the IOS and Android platform. For this, you can update the platform-specific files.


**For IOS**

Find the `info.plist` file at the `your_project/ios/Runner/info.plist` and add the following permissions:

{% highlight ruby %}
<key>NSMicrophoneUsageDescription</key>
<string>Use mic message</string>
<key>NSSpeechRecognitionUsageDescription</key>
<string>Use speech recognition</string>
{% endhighlight %}


- `NSSpeechRecognitionUsageDescription` - describe why your app uses speech recognition. This is called Privacy - Speech Recognition Usage Description in the visual editor.
- `NSMicrophoneUsageDescription` - describe why your app needs access to the microphone. This is called Privacy - Microphone Usage Description in the visual editor.


**For Android**

Head over to the `android/app/src/main/AndroidManifest.xml` file and paste the code below before the `<application` tag.

{% highlight ruby %}
package="com.example.speech_to_text_app">
   <uses-permission android:name="android.permission.RECORD_AUDIO"/>
   <uses-permission android:name="android.permission.INTERNET"/>
  <application
       android:label="speech_to_text_app"
       android:name="${applicationName}"
       android:icon="@mipmap/ic_launcher">

{% endhighlight %}

- `android.permission.RECORD_AUDIO` - this permission is required for microphone access.
- `android.permission.INTERNET` - this permission is required because speech recognition may use remote services.



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


### **Create the UI**

Head over to the `main.dart` and delete the entire default code first. Now, import the dependencies.

{% highlight ruby %}
    import 'package:flutter/material.dart';
    import 'package:avatar_glow/avatar_glow.dart';
    import 'package:highlight_text/highlight_text.dart';
    import 'package:speech_to_text/speech_to_text.dart' as stt;
{% endhighlight %}

Add the `MyApp class` and in the override function `build`, return a MaterialApp with home: `SpeechScreen()`.

{% highlight ruby %}

void main() {
 runApp(MyApp());
}

class MyApp extends StatelessWidget {
 @override
 Widget build(BuildContext context) {
   return MaterialApp(
     title: 'Flutter Voice',
     debugShowCheckedModeBanner: false,
     theme: ThemeData(
       primarySwatch: Colors.red,
       visualDensity: Visual Density.adaptivePlatformDensity,
     ),
     home: SpeechScreen(),
   );
 }
}
{% endhighlight %}

Create the Stateful Widget **SpeechScreen Class**.

{% highlight ruby %}

class SpeechScreen extends StatefulWidget {
 @override
 _SpeechScreenState createState() => _SpeechScreenState();
}

class _SpeechScreenState extends State<SpeechScreen> {}

{% endhighlight %}

Inside the **_SpeechScreenState**, add the important variables and Add Highlighted Text variables.


{% highlight ruby %}

late stt.SpeechToText _speech;
bool _isListening = false;
String _text = 'Press the button and start speaking';
double _confidence = 1.0;

final Map<String, HighlightedWord> _highlights = {
'like': HighlightedWord(
    onTap: () => print('like'),
    textStyle: const TextStyle(
    color: Colors.blueAccent,
    fontWeight: FontWeight.bold,
    ),
),
'comment': HighlightedWord(
    onTap: () => print('comment'),
    textStyle: const TextStyle(
    color: Colors.green,
    fontWeight: FontWeight.bold,
    ),
),
};

{% endhighlight %}

- `_speech` is where we instantiate the SpeechToText object
- `_isListening` is a boolean variable where we base our SpeechToText object if it will start to listen or stop listening.
- `_text` is String object is where we set the word listened by the SpeechToText object that is converted to text.
- `_confidence` Is the value of accuracy of the SpeechToText object of his conversion to text.
- `_highlights` will highlight the specific word the we set. For this one we set the **like** and **comment**. If we speak the word like it will be displayed with a font color of blueAccent. If we speak the word comment, it will be displayed with a font color of green.

Override initState function. Every time `initState()` is called. The SpeectToText Object is initialized.

{% highlight ruby %}
 @override
 void initState() {
   super.initState();
   _speech = stt.SpeechToText();
 }

{% endhighlight %}

Now, override the `build` function


{% highlight ruby %}
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Confidence: ${(_confidence * 100.0).toStringAsFixed(1)}%'),
      ),
      floatingActionButtonLocation: FloatingActionButtonLocation.centerFloat,
      floatingActionButton: AvatarGlow(
        animate: _isListening,
        glowColor: Theme.of(context).primaryColor,
        endRadius: 75.0,
        duration: const Duration(milliseconds: 2000),
        repeatPauseDuration: const Duration(milliseconds: 100),
        repeat: true,
        child: FloatingActionButton(
          onPressed: _listen,
          child: Icon(_isListening ? Icons.mic : Icons.mic_none),
        ),
      ),
      body: SingleChildScrollView(
        reverse: true,
        child: Container(
          padding: const EdgeInsets.fromLTRB(30.0, 30.0, 30.0, 150.0),
          child: TextHighlight(
            text: _text,
            words: _highlights,
            textStyle: const TextStyle(
              fontSize: 32.0,
              color: Colors.black,
              fontWeight: FontWeight.w400,
            ),
          ),
        ),
      ),
    );
  }

{% endhighlight %}

- The **title** of the AppBar will display the **confidence** of the SpeechToText object multiplied in **100** to display as percentage.
- To display the text we use **TextHighlight** instead of ordinary Text Object. So that we can highlight a specific word we set.
- We used **AvatarGlow** for the Button to indicate when SpeechToText object is already listening on stopped listening. When it is glowing, it means you can talk now because it is already listening. If it is not glowing, it is not listening. You must tap it to listen.


Add the `listen()` function

{% highlight ruby %}
void _listen() async {
   if (!_isListening) {
     bool available = await _speech.initialize(
       onStatus: (val) {
         (val == 'notListening') || (val == 'done') ? stopSpeechListening() :  debugPrint('onStatus: $val');
       },
       onError: (val) => debugPrint('onError: $val'),
     );
     if (available) {
       setState(() => _isListening = true);
       _speech.listen(
         onResult: (val) => setState(() {
           _text = val.recognizedWords;
           if (val.hasConfidenceRating && val.confidence > 0) {
             _confidence = val.confidence;
           }
         }),
       );
     }
   } else {
     stopSpeechListening();
   }
 }

{% endhighlight %}

And Finally, implement the `stopSpeechListening()`  function

{% highlight ruby %}
 void stopSpeechListening() {
   setState(() => _isListening = false);
   _speech.stop();
 }
{% endhighlight %}

So in all, here is the complete code.
{% highlight ruby %}
import 'package:flutter/material.dart';
import 'package:avatar_glow/avatar_glow.dart';
import 'package:highlight_text/highlight_text.dart';
import 'package:speech_to_text/speech_to_text.dart' as stt;

void main() {
 runApp(MyApp());
}

class MyApp extends StatelessWidget {
 @override
 Widget build(BuildContext context) {
   return MaterialApp(
     title: 'Flutter Voice',
     debugShowCheckedModeBanner: false,
     theme: ThemeData(
       primarySwatch: Colors.red,
       visualDensity: Visual Density.adaptivePlatformDensity,
     ),
     home: SpeechScreen(),
   );
 }
}

class SpeechScreen extends StatefulWidget {
 @override
 _SpeechScreenState createState() => _SpeechScreenState();
}

class _SpeechScreenState extends State<SpeechScreen> {

late stt.SpeechToText _speech;
bool _isListening = false;
String _text = 'Press the button and start speaking';
double _confidence = 1.0;

final Map<String, HighlightedWord> _highlights = {
'like': HighlightedWord(
    onTap: () => print('like'),
    textStyle: const TextStyle(
    color: Colors.blueAccent,
    fontWeight: FontWeight.bold,
    ),
),
'comment': HighlightedWord(
    onTap: () => print('comment'),
    textStyle: const TextStyle(
    color: Colors.green,
    fontWeight: FontWeight.bold,
    ),
),
};

 @override
 void initState() {
   super.initState();
   _speech = stt.SpeechToText();
 }

 @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Confidence: ${(_confidence * 100.0).toStringAsFixed(1)}%'),
      ),
      floatingActionButtonLocation: FloatingActionButtonLocation.centerFloat,
      floatingActionButton: AvatarGlow(
        animate: _isListening,
        glowColor: Theme.of(context).primaryColor,
        endRadius: 75.0,
        duration: const Duration(milliseconds: 2000),
        repeatPauseDuration: const Duration(milliseconds: 100),
        repeat: true,
        child: FloatingActionButton(
          onPressed: _listen,
          child: Icon(_isListening ? Icons.mic : Icons.mic_none),
        ),
      ),
      body: SingleChildScrollView(
        reverse: true,
        child: Container(
          padding: const EdgeInsets.fromLTRB(30.0, 30.0, 30.0, 150.0),
          child: TextHighlight(
            text: _text,
            words: _highlights,
            textStyle: const TextStyle(
              fontSize: 32.0,
              color: Colors.black,
              fontWeight: FontWeight.w400,
            ),
          ),
        ),
      ),
    );
  }

  void _listen() async {
   if (!_isListening) {
     bool available = await _speech.initialize(
       onStatus: (val) {
         (val == 'notListening') || (val == 'done') ? stopSpeechListening() :  debugPrint('onStatus: $val');
       },
       onError: (val) => debugPrint('onError: $val'),
     );
     if (available) {
       setState(() => _isListening = true);
       _speech.listen(
         onResult: (val) => setState(() {
           _text = val.recognizedWords;
           if (val.hasConfidenceRating && val.confidence > 0) {
             _confidence = val.confidence;
           }
         }),
       );
     }
   } else {
     stopSpeechListening();
   }
 }

  void stopSpeechListening() {
   setState(() => _isListening = false);
   _speech.stop();
 }

}

{% endhighlight %}

### **Resources**
- [Source Code][source-code]
- [speech_to_text_package][speech-to-text-package]
- [highlight_text_package][highlight-text-package]
- [avatar_glow_package][avatar-glow-package]

[source-code]: https://drive.google.com/file/d/1JuQ71BaWZsMuYD6xoi91a8gPnhUSPWaV/view?usp=sharing
[speech-to-text-package]: https://pub.dev/packages/speech_to_text
[highlight-text-package]: https://pub.dev/packages/highlight_text
[avatar-glow-package]: https://pub.dev/packages/avatar_glow

### **Conclusion**
In this vlog, you were able to create an app the convert speech to text. An app that will listen to the voice, analyze it and convert it to text.
Imagine the wide application you can apply this technology. Because of this, I leave you this quote. "Keep on dreaming and your dream will always be dreamed."


