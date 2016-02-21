# Event handling in jQuery

## Browser events
There a 2 event modes called `capturing` and `bubbling`.
**IE < 9** supports only event bubbling, whereas IE9+ and all major browsers support both. (1)
When attaching an eventlistener, you have to choose to which phase to attach it.

### Event order
First, all `capturing` handlers are executed, starting with the topmost element and going down until the element the event occured on is reached.  













(1 )  https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Legacy_Internet_Explorer_and_attachEvent
