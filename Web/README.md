![img](header.png)

 # SportBuff Web SDK Documentation

This document contains information about the Sport Buff Web SDK setup, features and configuration options

### **Getting Started**

Our SDK is compatible with the following Browsers:

 ```
    chrome 70+
    edge 18+
    firefox 68+
    opera 68+
    safari 13
 ```

**This gives us a Browser compatibility (based on the browserslist standard) > 0.5%**

The SportBuff SDK does support any custom Video or Media player and also provides out of the box configurations for embedded **YouTube** and **Twitch.tv** media players.

## SDK Integration
You should add our SDK Library from the given CDN and add into your site's header section `<head> </head>`.

Example:
```html
<html lang="en">
    <head>
        ...
        <script src="https://buffup-public.s3.eu-west-2.amazonaws.com/webSDK/1.0.0/buffSDK.min.js"></script>
        ...
    </head>
    ...
```

## **Using the SDK**

Once the SDK is loaded, you can initialize it on any media player by using the following method:

```javascript
    BuffVideo({
        overlayQuerySelector:      // !Required: string
        token:                     // !Required: string
        pluginType:                // !Optional: string 'default' | 'youtube' | 'twitch'
        startTime:                 // !Optional: Date (2020-08-28T14:48:13Z)
        streamTitle:               // !Required: string
        streamId:                  // !Required: number
        customization:{            // !Optional: every fields are optional
                webController:                      // boolean
                autoHideMenu:                       // boolean
                menuPosition:                       // string 'left' | 'center' | 'right'
                buffPosition:                       // string 'left' | 'center' | 'right'
                buffAnimationIn:                    // string 'bounceInUp'
                buffAnimationOut:                   // string 'bounceOutDown'
                menuXoffset:                        // number
                menuYoffset:                        // number
                buffXoffset:                        // number
                buffYoffset:                        // number
      					hideLeaderBoardPictures:            // boolean
                labels:{
                    label_ranking:                  // string
                    label_now:                      // string
                    label_global:                   // string 
                }
            }
    });
```

##### overlayQuerySelector: 
Standard Javascript `document.querySelector()` where the SDK should render the BuffUP overlay e.g. `#videoPlayer` , `.videoPlayer` 

##### token: 
The JWT token that you have retrieved from our Backend using our Rest API *(See the API Documentation section for details)*

##### pluginType:
You should only provide this value if you are using our SDK over a Youtube or Twitch player

##### startTime:
An optional time, in **UTC** which is used to synchronize the `mediaCurrentTime` with our Buff's (**only needed if the implementation of the player is allowing the user to timeShift** )

##### streamTitle / streamId:
A required parameter with either the Event Title or the Event Id (**preferred**) or both.

These values are used to match the current stream with our backend provided streams in order to keep separate leaderboards per event.

##### customization:
Customizable features may vary based on SDK version or client needs.

## Using the SDK

In case of a continuous live stream where events are changing, you will need to update the event name and/or id using the following method

```javascript
    setBuffVideo({
        startTime:                 // !Optional: Date (2020-08-28T14:48:13Z)
        streamTitle:               // !Required: string
        streamId:                  // !Required: number
    });
```

#### 

## Examples of different integration scenarios

#### HTML5 example

```html
...
<div class=playerWrapper>
        <video id="video" playsinline="" class="__video-element" src="blob:./videoStream">
        </video>
    <div class="buff-overlay">

    </div> 
</div>
...

<script>
    // Be sure that BuffSDK loaded before initialization
    window.onload = function(){
      BuffVideo({
          overlayQuerySelector: ".buff-overlay",
          token: "IkpXVCJ9.eyJzgRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxeJf36POk6yJV",
          startTime: "2020-08-28T14:48:13Z",
          streamTitle: "World cup final 2022",
          streamId: 1337,
      });
    };
</script>
...

```

------

#### Youtube example

```html
...
<div class=playerElement>
    <iframe
    src="https://www.youtube.com/embed/bTqsdafFSmY?origin=https://plyr.io&amp;iv_load_policy=3&amp;modestbranding=1&amp;playsinline=1&amp;showinfo=0&amp;rel=0&amp;enablejsapi=1"
    ></iframe>
</div>
...

<script>
    // Be sure that BuffSDK is loaded before initialization
    window.onload = function(){
      BuffVideo({
          overlayQuerySelector: ".playerElement",
          token: "JWT USER TOKEN",
          pluginType: "youtube",
          streamId: 11338,
      });
    };
</script>
...

```

------

### Load new stream listener

```html
<script>
    ...
    // Be sure that BuffSDK is loaded before initialization
    window.onload = function(){
      BuffVideo({
          overlayQuerySelector: ".playerElement",
          token: "JWT USER TOKEN",
          pluginType: "youtube",
          streamId: 11338,
      });
    };

    setBuffVideo({
        streamId: 11339,
    })
    ...
</script>


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



**The current base URL** is `https://sdk-api-staging.buffup.net/api`

------

#### Example curl requests

```json
curl --location --request POST 'https://sdk-api-staging.buffup.net/api/v1/auth/client' \
--header 'Content-Type: application/json' \
--data-raw '{
	"key": "SDK KEY",
	"secret": "SDK SECRET"
}'
```

```json
curl --location --request POST 'https://sdk-api-staging.buffup.net/api/v1/auth/uuid' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer SDK_CLIENT_TOKEN' \
--data-raw '{
	"uuid": "123-456-789-0000"
}'
```

