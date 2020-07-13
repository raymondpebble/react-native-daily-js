# react-native-daily-js

The Daily.co JavaScript library for React Native.

## Minimum OS/SDK versions

This package introduces some constraints on what OS/SDK versions your project can support:

- **iOS**: Deployment target >= 10.0
- **Android**: `minSdkVersion` >= 23

## Installation

Install `react-native-daily-js` along with its peer dependencies:

```bash
npm i @daily-co/react-native-daily-js react-native-webrtc @react-native-community/async-storage
```

Then, follow the below steps to set up your native project on each platform. **Note that these steps assume you're using a version of React Native that supports autolinking (>= 60).**

### iOS

Update the `platform` in your `Podfile`, since `react-native-webrtc` only works on iOS 10 and above:

```ruby
platform :ios, '10.0'
```

Then run:

```bash
npx pod-install
```

Finally, open Xcode, and in your project's `Info.plist` file add two new rows with the following keys:

- `NSCameraUsageDescription`
- `NSMicrophoneUsageDescription`

For their values, provide user-facing strings explaining why your app is asking for camera and microphone access. **Note that, without these, the app will simply crash silently.**

### Android

Add the following permissions to `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera" />
<uses-feature android:name="android.hardware.camera.autofocus"/>

<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```

Update your `minSdkVersion` in your top-level `build.gradle` file:

```groovy
minSdkVersion = 23
```

(If you run into any issues, refer to [Github issues](https://github.com/react-native-webrtc/react-native-webrtc/issues/720) like [these](https://github.com/jitsi/jitsi-meet/issues/4778), or the `react-native-webrtc` [installation docs](https://github.com/react-native-webrtc/react-native-webrtc/blob/master/Documentation/AndroidInstallation.md), which walk you through a more complicated process. The simpler process laid out above seems to work in a vanilla modern React Native CLI-based setup).

## Usage

`react-native-daily-js` is the React Native counterpart to `daily-js`, and can be used in pretty much the same way to add video calls to your apps. [Complete documentation for `daily-js` can be found here](https://docs.daily.co/reference#using-the-dailyco-front-end-library).

```ts
import Daily from '@daily-co/react-native-daily-js';

// ...

// Start joining a call
const call = Daily.createCallObject();
call.join({ url: 'https://your-team.daily.co/allhands' });

// Listen for events signaling changes to participants or their audio or video.
// This includes the local participant.
const events: DailyEvent[] = [
  'participant-joined',
  'participant-updated',
  'participant-left',
];
for (const event of events) {
  callObject.on(event, () => {
    for (const participant of Object.values(callObject.participants())) {
      console.log('---');
      console.log(`participant ${participant.user_id}:`);
      if (participant.local) {
        console.log('is local');
      }
      if (participant.audio) {
        console.log('audio enabled', participant.audioTrack);
      }
      if (participant.video) {
        console.log('video enabled', participant.videoTrack);
      }
    }
  });
}
```

```tsx
import { DailyMediaView } from '@daily-co/react-native-daily-js';

// ...

<DailyMediaView
  videoTrack={participant.videoTrack}
  audioTrack={participant.audioTrack}
  mirror={participant.local}
  zOrder={participant.local ? 1 : 0}
  style={someStyle}
/>;
```

[See this blog post for a more thorough walkthrough of structuring a React video-chat app powered by Daily.co](https://www.daily.co/blog/building-a-custom-video-chat-app-with-react). It's focused on React web, but most of it should also apply to your React Native app.
