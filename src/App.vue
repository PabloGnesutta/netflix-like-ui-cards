<template>
  <div id="app">
    <router-view />
  </div>
</template>

<style lang="scss">
* {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
  color: #f4f4f4;
}

img,
video {
  width: 100%;
  display: block;
}

.highlight {
  color: coral;
}

.relative {
  position: relative;
}

.backdrop {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.753);
  z-index: 15;
}

$animationDuration: 0.3s;
$opacitySlowerDuration: 4s;

.opacity-enter-active {
  animation: opacity-in $animationDuration ease-out forwards;
}

.opacity-leave-active {
  animation: opacity-out $animationDuration ease-out forwards;
}

@keyframes opacity-in {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes opacity-out {
  from {
    opacity: 1;
  }
  to {
    opacity: 0;
  }
}

//opacity slower

.opacity-slower-enter-active {
  animation: opacity-slower-in $opacitySlowerDuration ease-out forwards;
}

.opacity-slower-leave-active {
  animation: opacity-slower-out $opacitySlowerDuration ease-out forwards;
}

@keyframes opacity-slower-in {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes opacity-slower-out {
  from {
    opacity: 1;
  }
  to {
    opacity: 0;
  }
}
</style>
