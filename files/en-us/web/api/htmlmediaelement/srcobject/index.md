---
title: HTMLMediaElement.srcObject
slug: Web/API/HTMLMediaElement/srcObject
page-type: web-api-instance-property
tags:
  - API
  - HTML
  - HTML DOM
  - HTMLMediaElement
  - Media
  - Property
  - Reference
  - srcObject
browser-compat: api.HTMLMediaElement.srcObject
---

{{APIRef("HTML DOM")}}

The **`srcObject`** property of the
{{domxref("HTMLMediaElement")}} interface sets or returns the object which serves as
the source of the media associated with the {{domxref("HTMLMediaElement")}}.

The object can be a {{domxref("MediaStream")}}, a {{domxref("MediaSource")}}, a
{{domxref("Blob")}}, or a {{domxref("File")}} (which inherits from `Blob`).

> **Note:** As of March 2020, only Safari supports setting objects other
> than `MediaStream`. Until other browsers catch up, for
> `MediaSource`, `Blob` and `File`, consider falling
> back to creating a URL with {{domxref("URL.createObjectURL()")}} and assign it to
> {{domxref("HTMLMediaElement.src")}}. See below for an example.

## Value

A {{domxref('MediaStream')}}, {{domxref('MediaSource')}}, {{domxref('Blob')}}, or
{{domxref('File')}} object (though see the compatibility table for what is actually
supported).

## Usage notes

Older versions of the Media Source specification required using
{{domxref("URL.createObjectURL", "createObjectURL()")}} to create an object URL then
setting {{domxref("HTMLMediaElement.src", "src")}} to that URL. Now you can just set
`srcObject` to the {{domxref("MediaStream")}} directly.

## Examples

### Basic example

In this example, a {{domxref("MediaStream")}} from a camera is assigned to a
newly-created {{HTMLElement("video")}} element.

```js
const mediaStream = await navigator.mediaDevices.getUserMedia({video: true});
const video = document.createElement('video');
video.srcObject = mediaStream;
```

In this example, a new {{domxref('MediaSource')}} is assigned to a newly-created
{{HTMLElement("video")}} element.

```js
const mediaSource = new MediaSource();
const video = document.createElement('video');
video.srcObject = mediaSource;
```

### Supporting fallback to the src property

The examples below support older browser versions that require you to create an object
URL and assign it to `src` if `srcObject` isn't supported.

First, a {{domxref("MediaStream")}} from a camera is assigned to a newly-created
{{HTMLElement("video")}} element, with fallback for older browsers.

```js
const mediaStream = await navigator.mediaDevices.getUserMedia({video: true});
const video = document.createElement('video');
if ('srcObject' in video) {
  video.srcObject = mediaStream;
} else {
  // Avoid using this in new browsers, as it is going away.
  video.src = URL.createObjectURL(mediaStream);
}
```

Second, a new {{domxref('MediaSource')}} is assigned to a newly-created
{{HTMLElement("video")}} element, with fallback for older browsers and browsers that
don't yet support assignment of {{domxref('MediaSource')}} directly.

```js
const mediaSource = new MediaSource();
const video = document.createElement('video');
// Older browsers may not have srcObject
if ('srcObject' in video) {
  try {
    video.srcObject = mediaSource;
  } catch (err) {
    if (err.name !== "TypeError") {
      throw err;
    }
    // Even if they do, they may only support MediaStream
    video.src = URL.createObjectURL(mediaSource);
  }
} else {
  video.src = URL.createObjectURL(mediaSource);
}
```

### Constructuring a `MediaSource` in a worker and passing it to the main thread to play

The {{domxref("MediaSource.handle")}} property can be accessed inside a dedicated worker and the resulting {{domxref("MediaSourceHandle")}} object is then transferred over to the main thread via a {{domxref("DedicatedWorkerGlobalScope.postMessage()", "postMessage()")}} call:

```js
let mediaSource = new MediaSource();
let handle = mediaSource.handle;
postMessage({arg: handle}, [handle]);

// Fetch the media, buffer it, and pass it into the MediaSource
```

Over in the main thread, we receive the handle via a {{domxref("Worker.message_event", "message")}} event handler, attach it to a {{htmlelement("video")}} via its {{domxref("HTMLMediaElement.srcObject")}} property, and {{domxref("HTMLMediaElement.play()", "play")}} the video:

```js
worker.addEventListener('message', (msg) => {
  video.srcObject = msg.data.arg;
  video.play();
})
```

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}
