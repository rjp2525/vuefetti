<template>
  <div class="vuefetti"></div>
</template>
<script>
export default {
  name: "VueFetti",

  props: {
    options: {
      type: Object,
      default() {
        return {
          angle: 90,
          spread: 45,
          startVelocity: 45,
          elementCount: 50,
          width: "10px",
          height: "10px",
          colors: ["#a864fd", "#29cdff", "#78ff44", "#ff718d", "#fdff6a"],
          duration: 3000,
          stagger: 0,
          dragFriction: 0.1,
        };
      },
    },
  },

  methods: {
    createElements(root, elementCount, colors, width, height) {
      return Array.from({ length: elementCount }).map((_, index) => {
        const element = document.createElement("div");
        const color = colors[index % colors.length];

        element.style["background-color"] = color; // eslint-disable-line space-infix-ops
        element.style.width = width;
        element.style.height = height;
        element.style.position = "absolute";
        element.style.willChange = "transform, opacity";
        element.style.visibility = "hidden";
        root.appendChild(element);

        return element;
      });
    },

    randomPhysics(angle, spread, startVelocity) {
      const radAngle = angle * (Math.PI / 180);
      const radSpread = spread * (Math.PI / 180);
      return {
        x: 0,
        y: 0,
        z: 0,
        wobble: Math.random() * 10,
        wobbleSpeed: 0.1 + Math.random() * 0.1,
        velocity: startVelocity * 0.5 + Math.random() * startVelocity,
        angle2D: -radAngle + (0.5 * radSpread - Math.random() * radSpread),
        angle3D: -(Math.PI / 4) + Math.random() * (Math.PI / 2),
        tiltAngle: Math.random() * Math.PI,
        tiltAngleSpeed: 0.1 + Math.random() * 0.3,
      };
    },

    updateConfetti(confetti, progress, dragFriction, decay) {
      confetti.physics.x +=
        Math.cos(confetti.physics.angle2D) * confetti.physics.velocity;
      confetti.physics.y +=
        Math.sin(confetti.physics.angle2D) * confetti.physics.velocity;
      confetti.physics.z +=
        Math.sin(confetti.physics.angle3D) * confetti.physics.velocity;
      confetti.physics.wobble += confetti.physics.wobbleSpeed;
      // Backward compatibility
      if (decay) {
        confetti.physics.velocity *= decay;
      } else {
        confetti.physics.velocity -= confetti.physics.velocity * dragFriction;
      }
      confetti.physics.y += 3;
      confetti.physics.tiltAngle += confetti.physics.tiltAngleSpeed;

      const { x, y, z, tiltAngle, wobble } = confetti.physics;
      const wobbleX = x + 10 * Math.cos(wobble);
      const wobbleY = y + 10 * Math.sin(wobble);
      const transform = `translate3d(${wobbleX}px, ${wobbleY}px, ${z}px) rotate3d(1, 1, 1, ${tiltAngle}rad)`;

      confetti.element.style.visibility = "visible";
      confetti.element.style.transform = transform;
      confetti.element.style.opacity = 1 - progress;
    },

    animate(root, confettis, dragFriction, decay, duration, stagger) {
      let startTime;

      let self = this;
      return new Promise((resolve) => {
        function update(time) {
          if (!startTime) startTime = time;
          const elapsed = time - startTime;
          const progress =
            startTime === time ? 0 : (time - startTime) / duration;
          confettis
            .slice(0, Math.ceil(elapsed / stagger))
            .forEach((confetti) => {
              self.updateConfetti(confetti, progress, dragFriction, decay);
            });

          if (time - startTime < duration) {
            requestAnimationFrame(update);
          } else {
            confettis.forEach((confetti) => {
              if (confetti.element.parentNode === root) {
                return root.removeChild(confetti.element);
              }
            });
            resolve();
          }
        }

        requestAnimationFrame(update);
      });
    },

    backwardPatch(options) {
      if (!options.stagger && options.delay) {
        options.stagger = options.delay;
      }

      return options;
    },

    confetti(root) {
      const {
        elementCount,
        colors,
        width,
        height,
        angle,
        spread,
        startVelocity,
        decay,
        dragFriction,
        duration,
        stagger,
      } = Object.assign({}, this.options, this.backwardPatch(this.options));
      const elements = this.createElements(
        root,
        elementCount,
        colors,
        width,
        height
      );
      const fettis = elements.map((element) => ({
        element,
        physics: this.randomPhysics(angle, spread, startVelocity),
      }));
      return this.animate(root, fettis, dragFriction, decay, duration, stagger);
    },
  },

  mounted() {
    setTimeout(() => {
      this.confetti(this.$el);
    }, 330);
  },
};
</script>

<style>
.vuefetti {
  width: 1px;
  height: 1px;
}
</style>
