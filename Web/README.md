
 # BuffUp HTML5 SDK

 ### Browser compatibility (from browserslist standard > 0.5%):
 ```
    chrome 70+
    edge 18+
    firefox 68+
    opera 68+
    safari 13
 ```

### About

BuffUp SDK could be implemented into any custom Video or Media player and also provide pre-defined plugins for embedded Youtube and Twitch.tv players.

### First step
As a first you should load our SDK script from the given CDN and add into your site `<head> </head>`.

Example:
```html
<html lang="en">
    <head>
        ...
        <script src="https://buffup-public.s3.eu-west-2.amazonaws.com/web/buffsdk.min.js"></script>
        ...
    </head>
    ...
```

### Integration
Once our BuffUp SDK loaded, you can initialize into any media player with call the following function.

```javascript
    BuffVideo({
        overlayQuerySelector:      // !Required: string
        token:                     // !Required: string
        pluginType:                // !Optional: string 'default' | 'youtube' | 'twitch'
        startTime:                 // !Optional: Date (2020-08-28T14:48:13Z)
        streamTitle:               // !Required: string
        streamId:                  // !Required: number
    });
```

##### overlayQuerySelector: 
Standard Javascript `document.querySelector()` where the SDK should render the BuffUP overlay e.g. `#videoPlayer` , `.videoPlayer` 

##### token: 
Bearer token provided by the back-end, for more information please read the back-end implementation.

##### pluginType:
You should only provide this value in case of Youtube or Twitch player integration.

##### startTime:
UTC time used to sync `mediaCurrentTime` with Buffs timings. 

##### streamTitle / streamId:
These values used to match your media with our back-end. Either can be used or both, recommended `streamId` as it always unique.


### Examples for integration:

#### HTML5 example:

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

#### Youtube example:

```html
...
<div class=playerElement>
    <iframe
    src="https://www.youtube.com/embed/bTqsdafFSmY?origin=https://plyr.io&amp;iv_load_policy=3&amp;modestbranding=1&amp;playsinline=1&amp;showinfo=0&amp;rel=0&amp;enablejsapi=1"
    ></iframe>
</div>
...

<script>
    // Be sure that BuffSDK loaded before initialization
    window.onload = function(){
      BuffVideo({
          overlayQuerySelector: ".playerElement",
          token: "IkpXVCJ9.eyJzgRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxeJf36POk6yJV",
          pluginType: "youtube",
          streamId: 11338,
      });
    };
</script>
...

```

### Load new stream listener:

BuffUp SDK allow to switch between streams without reloading the page.

```javascript
    setBuffVideo({
        startTime:                 // !Optional: Date (2020-08-28T14:48:13Z)
        streamTitle:               // !Required: string
        streamId:                  // !Required: number
    });
```

#### HTML5 example:

```html
<script>
    ...
    // Be sure that BuffSDK loaded before initialization
    window.onload = function(){
      BuffVideo({
          overlayQuerySelector: ".playerElement",
          token: "IkpXVCJ9.eyJzgRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxeJf36POk6yJV",
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