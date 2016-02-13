# Effects.JS: Visual Effect Library for Rocky.JS

*This library requires modified version of Rocky.JS with exposed framebuffer as described [here](http://codecorner.galanter.net/2016/02/11/fire-on-high-or-framebuffer-in-rocky-js/">)*

####Initialization
````javascript
var effectHub = Effects.getEffectHub(rocky, 144, 168);
````
Creates an effect hub - main object that manages effects. Parameters are Rocky object and canvas size
####Adding effects
````javascript
effectHub.addEffect(<effect type>, bounds, [param]);
````
&lt;effect type&gt; is one of the predefined effect types (see below), bounds is an object representing effect boundaries in format {x:x, y:y, w:w, h:h}. [param] is an optional parameter that is required for some effects.
####Render effects
````javascript
effectHub.renderEffects();
````
This code should be placed inside of Rocky update proc, it will render all added effects in order they were added
####Available effects
Currently following effects are available:

**Vertical Mirror** will create a vertical mirror image of selected area. Example usadge:
````javascript
effectHub.addEffect(Effects.EFFECT_VERTICAL_MIRROR, {x:4, y:0, w:140, h:42});
````
**Invert** will invert all colors of selected area. Example usadge:
````javascript
effectHub.addEffect(Effects.EFFECT_INVERT, { x: 2, y: 42, w: 140, h: 42 });
````
**Rotate 90 degrees** will rotate selected area 90 degrees left or rigt. Example usadge:
````javascript
effectHub.addEffect(Effects.EFFECT_ROTATE_90_DEGREES, { x: 95, y: 83, w: 48, h: 48 }, false);
````
Last parameter is a boolean, indicating weather we rotaing area right (true = rotating right, false = rotating left)

**Outline** create outline of a given color and width of anything (text or image) of specified color. Last parameter is an object:
````
{ 
  offset_x: <horizontal width of outline>, 
  offset_y: <vertical width of outline>, 
  orig_color: <color of image/text on the screen to create outline around>, 
  offset_color: <color of the outline> 
}
````
Example usadge:
````javascript
effectHub.addEffect(Effects.EFFECT_OUTLINE, { x: 0, y: 42 * 3, w: 144, h: 42 }, 
                    { offset_x: 1, offset_y: 1, orig_color: GColorGreen, offset_color: GColorYellow });
````
This will create effect of yellow outline around green areas of width (1,1).

**Horizontal Mirror** will create a horizontal mirror image of selected area. Example usadge:
````javascript
effectHub.addEffect(Effects.EFFECT_HORIZONTAL_MIRROR, { x: 0, y: 83, w: 144-48, h: 48 });
````
**Complete example**
````javascript
var rocky = Rocky.bindCanvas(document.getElementById("pebble"));
rocky.export_global_c_symbols();
 
rocky.update_proc = function (ctx, bounds) {

    // your text and images render here
    //...
    /////

    //rendering effects
    effectHub.renderEffects();

 };

//creating effect hub based on rocky object and canvas size 
var effectHub = Effects.getEffectHub(rocky, 144, 168);

//adding effects
effectHub.addEffect(Effects.EFFECT_VERTICAL_MIRROR, {x:4, y:0, w:140, h:42});
effectHub.addEffect(Effects.EFFECT_INVERT, { x: 2, y: 42, w: 140, h: 42 });
effectHub.addEffect(Effects.EFFECT_ROTATE_90_DEGREES, { x: 95, y: 83, w: 48, h: 48 }, false);
effectHub.addEffect(Effects.EFFECT_OUTLINE, { x: 0, y: 42 * 3, w: 144, h: 42 }, 
              { offset_x: 1, offset_y: 1, orig_color: GColorGreen, offset_color: GColorYellow });
effectHub.addEffect(Effects.EFFECT_HORIZONTAL_MIRROR, { x: 0, y: 83, w: 144-48, h: 48 });

setInterval(function () {
     rocky.mark_dirty();
}, 1000);
````
You can see this in action [here](http://codecorner.galanter.net/pebble/rockyjs/effectsjs/effectsjs.html)

####Advanced usage
If you assign refrence to boundary object to a variable prior to passing it to <code>addEffect()</code> method as parameter - you can manipulate the boundary making your effect mobile. For example this code will move effect area horizontally:
````javascript
var bounds = {x:4, y:0, w:140, h:42};
effectHub.addEffect(Effects.EFFECT_VERTICAL_MIRROR, bounds});

setInterval(function () {
    bounds.x +=10;
    if (bounds.x >= 144) bounds = 0
    rocky.mark_dirty();
}, 1000);
````
