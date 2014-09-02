# Are You Here Yet

## Code
* [https://github.com/MrMaksimize/AreYouHereYet](https://github.com/MrMaksimize/AreYouHereYet)

## Links

* [Main Screen Screenshot - http://mrm-screen.s3.amazonaws.com/iOS%20Simulator%20Screen%20shot%20Sep%201%2C%202014%2C%2017.42.47.png](http://mrm-screen.s3.amazonaws.com/iOS%20Simulator%20Screen%20shot%20Sep%201%2C%202014%2C%2017.42.47.png)
* [Adding People To Notify - http://mrm-screen.s3.amazonaws.com/iOS%20Simulator%20Screen%20shot%20Sep%201%2C%202014%2C%2017.45.17.png](http://mrm-screen.s3.amazonaws.com/iOS%20Simulator%20Screen%20shot%20Sep%201%2C%202014%2C%2017.45.17.png)


## Info
Texting and driving is dangerous, but common.  I decided to write an app that would effectively do it for you.  **Are You Here Yet** is a side project I've worked with on and off for quite a while.  The idea is:

1. You open the app.
2. You pick from your address book who do you want to notify as you get closer.
3. You start driving.
4. The app will send a text message to those people as you are getting closer.

The architecture is pretty simple.  I'm using the Google Distance Matrix API to determine how far you are from your destination, and I'm using Parse as a pass through to the Twilio API to send text messages.

I have written it originally for iOS 6, and will probably break if you try to run it on top of the current iOS7 SDK.  It definitely needs lots more polish and usability testing, and I'm hoping, once I have some time, to rework it in Swift.