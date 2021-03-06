## 1.2.0

### Multiple interactions

Multiple interactions have been enabled by default. For example:

```javascript
interact('.drag-element').draggable({
    enabled: true,
 // max          : Infinity,  // default
 // maxPerElement: 1,         // default
});
```

will allow multiple `.drag-element` to be dragged simultaneously without having
to explicitly set <code>max:&nbsp;integerGreaterThan1</code>. The default
`maxPerElement` value is still 1 so only one drag would be able to happen on
each `.drag-element` unless the `maxPerElement` is changed.

If you don't want multiple interactions, call `interact.maxInteractions(1)`.

### Snapping

#### Unified snap modes
Snap modes have been
[unified](https://github.com/taye/interact.js/pull/127). A `targets` array
now holds all the snap objects and functions for snapping.
`interact.createSnapGrid(gridObject)` returns a function that snaps to the
dimensions of the given grid.

#### `relativePoints` and `origin`

```javascript
interact(target).draggable({
  snap: {
    targets: [ {x: 300, y: 300} ],
    relativePoints: [
      { x: 0, y: 0 },  // snap relative to the top left of the element
      { x: 1, y: 1 },  // and also to the bottom right
    ],  

    // offset the snap target coordinates
    // can be an object with x/y or 'startCoords'
    offset: { x: 50, y: 50 }
  }
});
```

#### snap function interaction arg

The current `Interaction` is now passed as the third parameter to snap functions.

```javascript
interact(target).draggable({
  snap: {
    targets: [ function (x, y, interaction) {
      if (!interaction.dropTarget) {
        return { x: 0, y: 0 };
      }
    } ]
  });
```

#### snap.relativePoints and offset

The `snap.relativePoints` array succeeds the snap.elementOriign object. But
backwards compatibility with `elementOrigin` and the old snapping interface is
maintained.

`snap.offset` lets you offset all snap target coords.

See [this PR](https://github.com/taye/interact.js/pull/133) for more info.

#### slight change to snap range calculation

Snapping now occurs if the distance to the snap target is [less than or
equal](https://github.com/taye/interact.js/commit/430c28c) to the target's
range.

### Inertia

`inertia.zeroResumeDelta` is now `true` by default.

### Per-action settings

Snap, restrict, inertia, autoScroll can be different for drag, restrict and
gesture. See [PR 115](https://github.com/taye/interact.js/pull/115).

Methods for these settings on the `interact` object (`interact.snap()`,
`interact.autoScroll()`, etc.) have been removed.

### Space-separated string and array event list and eventType:listener object

```javascript
function logEventType (event) {
  console.log(event.type, event.target);
}

interact(target).on('down tap dragstart gestureend', logEventType);

interact(target).on(['move', 'resizestart'], logEventType);

interact(target).on({
  dragmove: logEvent,
  keydown : logEvent
});
```

### Interactable actionChecker

The expected return value from an action checker has changed from a string to
an object. The object should have a `name` and can also have an `axis`
property. For example, to resize horizontally:

```javascript
interact(target).resizeable(true)
  .actionChecker(function (pointer, defaultAction, interactable, element) {
    return {
      name: 'resize',
      axis: 'x',
    };
  });
```

### Plain drop event objects

All drop-related events are [now plain
objects](https://github.com/taye/interact.js/issues/122). The related drag
events are referenced in their `dragEvent` property.

### Interactable.preventDefault('always' || 'never' || 'auto')

The method takes one of the above string values. It will still accept
`true`/`false` parameters which are changed to `'always'`/`'never'`.

## 1.1.3

### Better Events

Adding a function as a listener for an InteractEvent or pointerEvent type
multiple times will cause that function to be fired multiple times for the
event. Previously, adding the event type + function combination again had no
effect.

Added new event types [down, move, up, cancel,
hold](https://github.com/taye/interact.js/pull/101).

Tap and doubletap with multiple pointers was improved.

Added a workaround for IE8's unusual [dblclick event
sequence](http://www.quirksmode.org/dom/events/click.html) so that doubletap
events are fired.

Fixed a [tapping issue](https://github.com/taye/interact.js/issues/104) on
Windows Phone/RT.

Fixed a bug that caused the origins of all elements with tap listeners to be
subtracted successively as a tap event propagated.

[Fixed delegated events](https://github.com/taye/interact.js/commit/e972154)
when different contexts have been used.

### iFrames

[Added basic support](https://github.com/taye/interact.js/pull/98) for sharing
one instance of interact.js between multiplie windows/frames. There are still
some issues.
