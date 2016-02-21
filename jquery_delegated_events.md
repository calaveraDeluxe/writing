Events can be attached with  [`.on()`](http://api.jquery.com/on/) method in two different modes: `direct` and `delegated`.
Which mode is used is controlled implicitly by the number of arguments passed:

* `$('.child').on('click', someFunction)` uses direct mode
* `$('.container').on('click', '.child', someFunction)` uses delegated mode

bind & delegate

## Direct mode
In `direct` mode, the events are are bound directly to the elements matched by the jQuery selector. Consider the following HTML structure:

```html
<div class="container">
  <div class="child"></div>
  <div class="child"></div>
  <div class="child"></div>
</div>
```

When calling `$('.child').on('click', someFunction);`, one eventlistener is attached to every matched element (in this case, 3 eventlistener total). This is fine for many cases, but has two possible drawbacks:
* When the selector matches a lot of elements ataching an eventlistner to every single one of them might be slow
* When adding elements later via javascript, the handlers need to be bound to these elements too

## Delegated mode
In delegated mode, the listener is bound to parent element (the container) and an additional parameter is passed to indicate which elements we are interesed in. When an event occures inside the container and bubbles up to it, the following code is executed (abbreviated a bit, full source is at [event.js](https://github.com/jquery/jquery/blob/1de834672959636da8c06263c3530226b17a84c3/src/event.js#L359)):

```javascript
for ( ; cur !== this; cur = cur.parentNode || this ) { // #1

	matches = [];
	for ( i = 0; i < delegateCount; i++ ) { // #2
		handleObj = handlers[ i ]; // #3
	
		sel = handleObj.selector;
	
		if ( matches[ sel ] === undefined ) {
			matches[ sel ] = jQuery.find( sel, this, null, [ cur ] ).length; // #4
		}
		if ( matches[ sel ] ) {
			matches.push( handleObj ); // #5
		}
	}
	if ( matches.length ) {
		handlerQueue.push( { elem: cur, handlers: matches } ); // #6
	}
}
```

Lets disect it:

1. initially, `cur` is set to `event.target` ([a few lines above this](https://github.com/jquery/jquery/blob/1de834672959636da8c06263c3530226b17a84c3/src/event.js#L348)), the element that the event occured on initially. Every iteration `cur` is set to its own parent element, thereby travelling up the dom. The loop ends when it reaches the container.


jQuery tests every element between the event.target (the element the event originally occured on) and the container (but not the container itself). If the tested element matches the given selector, the handler bound to the container is executed, but with the current element as the target (making it appear as if the event occured at the element, not the container).
