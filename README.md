# Xmas session :gift:
# A-Frame demos for Oculus Quest 2

A-Frame is compatible with most of the VR headsets, with the Meta Oculus Quest 2 for instance. This short tutorial explains
how to deal with controllers.

First of all, you need to embed few libraries to make it works :

```html
<!-- A-Frame JavaScript library -->
<script src="https://aframe.io/releases/1.1.0/aframe.min.js"></script>
<!-- A-Frame physics system -->
<script
    src="https://cdn.jsdelivr.net/gh/n5ro/aframe-physics-system@v4.0.1/dist/aframe-physics-system.min.js"></script>
<!-- A-Frame extras -->
<script src="https://cdn.jsdelivr.net/gh/donmccurdy/aframe-extras@v6.1.0/dist/aframe-extras.min.js"></script>
```

## Controllers

To display controllers and use them to interact with the other
objects, you need to add these two entities to your `a-scene`
element.

```html
<!-- Left hand -->
<a-entity id="leftController" static-body="shape: sphere; sphereRadius: 0.02;"
    oculus-touch-controls="hand: left" sphere-collider="objects: .throwable" grab>
</a-entity>

<!-- Right hand -->
<a-entity id="rightController" static-body="shape: sphere; sphereRadius: 0.02;"
    oculus-touch-controls="hand: right" sphere-collider="objects: .throwable" grab>
</a-entity>
```
The `sphereRadius` property is used for collision detection. The `grab` attribute allows you to grab objects with the controller front trigger. Every object with the class `throwable` can be catched.

```html
<a-sphere id="sphere1" class="throwable" dynamic-body position="-0.5 0.11 -0.5" radius="0.10" color="#4CC3D9">
</a-sphere>
```

## `collide` event handling

If you want to trigger actions when a collision occurs, you
have to add an event listener with JavaScript.

After the closing tag `</a-scene>`, insert the following code : 

```html
<script>
// Add a collide event listener to the object with id="box1"    
document.querySelector('#box1').addEventListener('collide', (e) => {
    /*
    e.detail.target.el is the #box1 element
    e.detail.body.el is the other object in collision
    */
    if (e.detail.body.el.id === 'sphere1') {
        e.detail.target.el.setAttribute('visible', 'false');
        e.detail.body.el.setAttribute('visible', 'false');
    }
});
</script>
```
Try the [DEMO](https://oliviernocent.github.io/xmas-oculus/grab.html)

## Moving a `dynamic-body` using constraints

Collisions only occurs between static and dynamic bodies. Since
the controllers are managed as static bodies, you have to deal 
with dynamic bodies for interacting with them.

But dynamic bodies can't be animated with the `animation` attribute. For that, you need to uses **constraints**.

An entity called *anchor* is animated using the `animation`
attribute. The dynamic object is then linked to the anchor using the `constraint` attribute :

```html
<a-box id="anchor1" width="0.01" height="0.01" depth="0.01" static-body
    animation="property: position; from: 0 0.5 -0.5; to: 0 2 -0.5; easing: easeInOutCubic; dur: 4000; dir: alternate; loop: true">
</a-box>
<a-sphere id="sphere1" position="0 0.5 -0.5" radius="0.10" color="#4CC3D9" dynamic-body="shape: sphere"
    constraint="type: distance; target: #anchor1; distance: 0.001; collideConnected: false">
</a-sphere>
```
Try the [DEMO](https://oliviernocent.github.io/xmas-oculus/collide.html)

## Random animations

You can assign several animations to a given object with the attributes `animation`, 
`animation__2`, `animation__3`, ... But if you want to trigger animations randomly, 
you have to add a bit of JavaScript to your HTML document.

```html
<script>
    window.addEventListener('DOMContentLoaded', () => {
        let cube = document.querySelector('#cube');

        function applyAnimation() {
            // Pick a random number in {0, 1, 2}
            let c = Math.floor(Math.random() * 3);
            // Assign two animations to the object with id="#cube" according to the random number c
            switch (c) {
                case 0:
                    cube.setAttribute('animation', 'property: position; from: 0 1.5 -1; to: 0 2.5 -1; dur: 1500; easing: easeInOutCubic');
                    cube.setAttribute('animation__2', 'property: position; from: 0 2.5 -1; to: 0 1.5 -1; dur: 1500; easing: easeInOutCubic; delay: 1500');
                    break;
                case 1:
                    cube.setAttribute('animation', 'property: opacity; from: 1; to: 0; dur: 1500; easing: easeInOutCubic');
                    cube.setAttribute('animation__2', 'property: opacity; from: 0; to: 1; dur: 1500; easing: easeInOutCubic; delay: 1500');
                    break;
                case 2:
                    cube.setAttribute('animation', 'property: rotation; from: 0 0 0; to: 0 360 0; dur: 1500; easing: easeInOutCubic');
                    cube.setAttribute('animation__2', 'property: rotation; from: 0 0 0; to: 360 0 0; dur: 1500; easing: easeInOutCubic; delay: 1500');
                    break;
            }
        }

        // Apply a new animation every 4 seconds
        window.setInterval(applyAnimation, 4000);
    });
</script>
```

Try the [DEMO](https://oliviernocent.github.io/xmas-oculus/random.html)


