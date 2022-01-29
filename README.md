## VueFetti

This is a port of the React [dom-confetti](https://github.com/daniel-lundin/dom-confetti) by [Daniel Lundin](https://github.com/daniel-lundin) for Vue 2.

<p align="center">
  <img src=".github/vuefetti-demo.gif">
</p>

### Usage

Import, register component and set options

```js
import Vue from 'vue';
import VueFetti from 'vuefetti';

export default {
    ...
    components: {
        VueFetti
    },
    ...
    data() {
        return {
            options: {
                angle: 90,
                spread: 360,
                startVelocity: 40,
                elementCount: 70,
                dragFriction: 0.12,
                duration: 3000,
                stagger: 3,
                width: "10px",
                height: "10px",
                perspective: "500px",
                colors: ["#a864fd", "#29cdff", "#78ff44", "#ff718d", "#fdff6a"],
            },
            visible: Vue.observable(false)
        };
    },
    ...
}
```

Add a method to toggle confetti:

```js
methods: {
    async explode() {
      this.visible = false;
      await Vue.nextTick();
      this.visible = true;
    },
},
```

Add to your template

```html
<div>
  <VueFetti v-if="visible" :options="vfconfig"></VueFetti>
  <button @click="explode">Go time</button>
</div>
```
