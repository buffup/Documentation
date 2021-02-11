![img](header.png)



# SportBuff Android SDK Documentation

This document contains information about the Sport Buff Android SDK setup, features and configuration options

**Getting Started**

The SDK is available for Android with support to SDK version 17 and newer

## SDK Integration
#### Gradle

Add it in your root build.gradle at the end of repositories:

```kotlin
allprojects {
		repositories {
			...
			maven { url 'https://jitpack.io' }
		}
	}
```


Add this line to your build.gradle file:

```kotlin
implementation 'com.buffup:buffsdk:1.0.25'
```

## **Using the SDK**

##### *Option A*

1. Add the following line to your Application class to initialise SDK and authenticate SDK with the SDK Key/Secret pair you will receive from us:

   ```kotlin
   BuffSdk.init(applicationContext: Context, key: String, secret: String)
   ```

   ##### applicationContext: 

   The app context 

   ##### key: 

   The SDK key you've received from us

   ##### secret:

   The SDK secret you've received from us
   
2. Sign in the user with the following method:

   ```kotlin
   BuffSdk.signIn(userId: String, email: String, photoUrl: String)
   ```

   ##### userId: 

   **Required** A unique identifier for the current user, which will always be the same for this user

   ##### email: 

   ***Optional*** The user's email address

   ##### photoUrl:

   ***Optional*** A url to the users profile picture

##### applicationContext: 
The app context 

##### key: 
The SDK key you've received from us

##### secret:
The SDK secret you've received from us

------

##### *Option B*

1. Add the following line to your Application class to initialise SDK:

   ```kotlin
   BuffSdk.init(applicationContext: Context)
   ```

   ##### applicationContext: 

   The app context 
   
2. Authenticate the user using our Rest API, in your own backend and then sign in the user with the following method:

   ```kotlin
   BuffSdk.signIn(token: String)
   ```

   ##### token: 

   **Required** The user JWT Token you've received from our Backend API service

   *The procedure is explained in the API Documentation section*




## Configuring the SDK

Our SDK provides a custom View named **BuffView**, which is responsible for displaying all UI elements on top of your video view.

Add this View on top of your video view then configure the placement of the elements in the BuffView, by using the Styleable properties of the view:

For example:
```
    <com.buffup.sdk.ui.BuffView
        android:id="@+id/buffView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:buffsDisplayType="cards"
        app:buffsVisibility="left"
        app:stateVisibility="true">

        <com.google.android.exoplayer2.ui.PlayerView
            android:id="@+id/playerView"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:resize_mode="zoom"
            app:use_controller="true" />

    </com.buffup.sdk.ui.BuffView>
```

##### **buffsVisibility**

Controls the **horizontal axis alignment** of the Buff Questions and Answers and has the following three options:

- left
- center
- right

------



##### **app:userPointsVisibility**

Controls the **horizontal axis alignment** of the Buff User Points View and has the following three options:

- left
- center
- right

------



##### **menuVisibility**

Controls the **location** of the Menu Button and has the following three options:

- left
- center
- right
- gone

------



##### **menuIcon**

Allows you to define a custom Menu Icon Asset

------



##### app:buffsBottomMargin

##### app:buffsSideMargin

Allows you to define custom margins for the buff's location

------



##### **app:pointsTopMargin** 

##### **app:pointsSideMargin**

Allows you to define custom margins for the User Points location




## Setting up the Stream in the SDK

In order for us to be able to serve the appropriate Buff's for the given stream, we need to receive the Stream information.

There are two way to achieve this, call any of the following method, when the stream is initialized on your video player 

***Option-1***

```kotlin
BuffView.setStreamTitle(streamTitle: String, timestamp: Long)
```

**streamTitle**

The name of the Stream you are going to be showing to the user

**timestamp**

A value representing the timestamp of the beginning of the stream in UTC timezone

***Option-2***

```kotlin
BuffView.initWithProviderId(providerId: String, startTimeStamp: Long?)
```

**providerId**

The id of the Stream Provider that you plan to show to users

**startTimeStamp**

A value representing the timestamp of the beginning of the stream in UTC timezone

***Option-3***

```kotlin
BuffView.initWithStreamId(streamId: String, startTimeStamp: Long?)
```

**streamId**

The id of the stream you are going to be showing to the user

**startTimeStamp**

A value representing the timestamp of the beginning of the stream in UTC timezone



**In case of a continuous stream where the events are changing, you need to reuse the above method each time the stream event changes**


Finally you need to be sending to our SDK updates on the video progress at regular intervals, using the following method:

```kotlin
BuffView.setVideoProgress(position: Long)
```

## Enable profile picture profile update
In order to pass a selected user picture to the Buff SDK add the following: 

```kotlin
    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        buffView.onActivityResult(requestCode, resultCode, data)
        super.onActivityResult(requestCode, resultCode, data)
    }
```


------


## API Documentation

We offer two REST API endpoints that should be used by the CBS (Client's Backend System) in order to authenticate a user and obtain a user JWT Token.

The authentication flow is as follows:

1. You obtain an **SDK Client Token**, using your SDK Key/Secret pair that we provided you with and making a **POST** request on the following endpoint:

   `/v1/auth/client`

```json
Request Body
{
  "key": "string",
  "secret": "string"
}
```

You should receive a JSON payload in the following format with your **SDK Client Token**:

```json
{
  "id": int,
  "token": "string"
}
```



2. You obtain a **User Client Token**, by using your SDK Client Token to authenticate and making a **POST** request on the following endpoint:

`/v1/auth/uuid`

```json
Request Body
{
  "uuid": "128 bit UUID String",
  "email": "string",
  "first_name": "string",
  "last_name": "string",
  "username": "string"
}
```

The **uuid** is the only required parameter. **email, first_name, last_name and username** are *optional parameters*.



You should receive a JSON payload in the following format with the **User JWT Token**:

```json
{
  "id": int,
  "token": "string"
}
```



**The current base URL** is `https://sdk.prod.buffup.net/api/v1`

------

#### Example curl requests

```json
curl --location --request POST 'https://sdk.prod.buffup.net/api/v1/auth/client' \
--header 'Content-Type: application/json' \
--data-raw '{
	"key": "SDK KEY",
	"secret": "SDK SECRET"
}'
```

```json
curl --location --request POST 'https://sdk.prod.buffup.net/api/v1/auth/uuid' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer SDK_CLIENT_TOKEN' \
--data-raw '{
	"uuid": "123-456-789-0000"
}'
```

