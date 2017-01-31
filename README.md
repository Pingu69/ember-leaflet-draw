# Ember-leaflet-draw

Provides feature drawing functionality for [Ember-Leaflet](http://ember-leaflet.com), an Ember Addon for [Leaflet](http://leafletjs.com) interactive maps.

This plugin is based on the JS library [Leaflet Draw](https://github.com/Leaflet/Leaflet.draw) and basically wraps it into ember component. It can also be used solely to handle all the dependencies and build process (if you want to control Leaflet Draw library on your own).

## Installation

* `ember install ember-leaflet-draw`

## Using the plugin

#### Basic Use
For basic use, drop the `{{draw-control}}` inside `{{leaflet-map}}`
```js
{{#leaflet-map lat=lat lng=lng zoom=zoom as |layers|}}
  {{layers.tile url="http://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png"}}
  {{draw-control}} // Basic usage, accepting all default options
{{/leaflet-map}}
```

#### Configuring with the available options

This component wraps the [Leaflet Draw](https://github.com/Leaflet/Leaflet.draw) library and exposes the same `options` (`position`, `draw`, and ~~`edit`~~), along with two additional options (`enableEditing` & `showDrawingLayer`). Support for the passthrough `edit` option from Leaflet Draw may be implemented in the near future.

| Option           | Default   |
| ---------------- |:---------:|
| position         | "topleft" |
| draw             | [See Docs](https://leaflet.github.io/Leaflet.draw/docs/leaflet-draw-latest.html#drawoptions)  |
| enableEditing    | true      |
| position         | "topleft" |
| showDrawingLayer | true      |

```js
// Example changing the position option, to display the control in the top right
{{draw-control position="topright"}}
```

___Current workaround if you need to be able to edit existing "marker" locations...___

You can currently edit the position of a `{{marker-layer}}` within Ember-Leaflet by setting the draggable option to `true` and using the `onDragend` action to capture the markers new location (see example below).
```js
// Tempalte.hbs
{{marker-layer lat=model.lat lng=model.lng draggable=true onDragend=(action "updateLocation" model)}}

// Controller.js/Component.js
  ...
  actions: {
      updateLocation(model, event) {
        let location = event.target.getLatLng();
        Ember.setProperties(model, {
          lat: location.lat,
          lng: location.lng
        });
      }
    }
  ...
```
#### Working with Events/Actions
The `events` (`draw:edited`, `draw:editmove`, `draw:editstart`, etc. ) have been exposed to the component as actions. The action name follows the same meaning as the original events, but cleans them up a bit (mainly dropping the colon).

A few examples of the new format are:

  - `draw:edited` sends an action named `onDrawEdited`
  - `draw:editmove` sends an action named `onDrawEditmove`
  - `draw:edited` sends an action named `onDrawEditstart`

  - *They all follow this pattern '`onDraw`' + '`{F}istletterofremainingstring`'*

For a full list of the Events and other API features of the underlying Leaflet Draw library, checkout the [Leaflet Draw API Documentation](https://leaflet.github.io/Leaflet.draw/docs/leaflet-draw-latest.html)


*Installing this plugin will pull in all the dependencies necessary to begin using Leaflet Draw, by extending leaflet. If you want total control to do your own thing, this may be all you need to easily bring in all dependencies and ensure everything is wired. You have access to `L.Draw` and `L.DrawToolbar` to do you own thing (for more advanced use cases).*

#### Production Builds
In your `ember-cli-build.js` add the following snippet:
```js
  var app = new EmberApp(defaults, {
    // Add options here
    fingerprint: {
      exclude: [
        // You should already have these from using Ember-Leaflet
        'images/layers-2x.png',
        'images/layers.png',
        'images/marker-icon-2x.png',
        'images/marker-icon.png',
        'images/marker-shadow.png',
        // These are additional images used by Ember-Leaflet-Draw
        'images/spritesheet-2x.png',
        'images/spritesheet.png',
        'images/spritesheet.svg'
      ]
    }
  });
```

*Ember-Cli does fingerprinting (appending an md5 checksum to the end of every file) for production builds by default (http://ember-cli.com/user-guide/#fingerprinting-and-cdn-urls). Exclude the assets you need so that your production build produces them correctly.*


## Running Tests

* `npm test` (Runs `ember try:each` to test your addon against multiple Ember versions)
* `ember test`
* `ember test --server`

## Building

* `ember build`

For more information on using ember-cli, visit [http://ember-cli.com/](http://ember-cli.com/).
