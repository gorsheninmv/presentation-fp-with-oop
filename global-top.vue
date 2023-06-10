<template>
  <Scroll/>
  <footer class="absolute bottom-8 right-8">
    <br/>
    <small v-if="$slidev.nav.currentPage > 1" >
      <SlideCurrentNo/>
    </small>
    <br/>
  </footer>
</template>
