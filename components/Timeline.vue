<script setup lang="ts">
import { ref, computed, $slidev } from 'vue'

const props = defineProps({
  count: {
    default: 0,
  },
  showall: {
    default: false,
  }
})

const showall = ref(props.showall)
const isRust = computed(() => !showall.value && $slidev.nav.clicks < 1)
const isKotlin = computed(() => !showall.value && $slidev.nav.clicks < 2)
const isCsharp = computed(() => !showall.value && $slidev.nav.clicks < 3)
const isGo = computed(() => !showall.value && $slidev.nav.clicks < 4)
</script>

<style scoped>

    .invisible {
      visibility: hidden;
    }

    .timeline { 
     
        /* set a variable for the color */
        --timeline-color: #ff6600;
      
        position: relative;
        list-style: none;
        display: inline-flex;
        flex-wrap: nowrap;
        margin: 0;
        padding: 0;
        height: 240px; }  /* set here the height of the timeline */


    /* middle line */
    .timeline:before {
        content: "";
        position: absolute;
        top: calc(50% - 1px);
        width: 100%;
        height: 2px;
        background: var(--timeline-color); }


    .timeline > li { 
        min-width: 130px;
        align-self: flex-start; 
      }

    .timeline > :first-child { 
        margin-right: 330px;
      }


    /*  event in even position are bottom-aligned */
    .timeline li:nth-child(2n) { align-self: flex-end;  }


    .timeline div {
        position: relative;
        padding: 10px;
        border: 2px var(--timeline-color) solid; }


    /* style for the dot over the timeline */
    .timeline > li:before {
        content: "";
        position: absolute;
        top: 50%;
        margin-left: 64px;
        transform: translate(-50%, -50%);
        border: 1px #ff6600 solid;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background:  var(--timeline-color);      
    }


    /* style for the event arrow */
    .timeline div:before {
        content: "";
        position: absolute;
        left: 50%;
        top: 100%;
        width: 20px;
        height: 20px;
        transform: translate(-50%, -1px) rotateZ(45deg);
        background: #fff;
    }


    /* position of the event arrow in odd position */
    .timeline li:nth-child(2n - 1) div:before {
        top: 100%;
        margin-top: -8px;
        border-right: 2px var(--timeline-color) solid;
        border-bottom: 2px var(--timeline-color) solid; }


    /* position of the event arrow in even position */
    .timeline li:nth-child(2n) div:before {
        top: 0;
        margin-top: -10px;
        border-left: 2px var(--timeline-color) solid;
        border-top: 2px var(--timeline-color) solid; }

</style>

<template>
  <div style="font-size: 10pt; transform: scale(0.75); margin-left: -100px;">
    <ul class="timeline">
      <li>
        <div>
          <time datetime="1965">1965</time>
          <p>ALGOL W</p>      
        </div>
      </li>
      <li :class="{ invisible: isRust }">
        <div>
          <time datetime="2006">2006</time>
          <p>Rust</p>      
        </div>
      </li>
      <li>
        <div>
          <time datetime="2009-03-11">March 11, 2009</time>
          <p>QCon</p>      
        </div>
      </li>
      <li :class="{ invisible: isGo }">
        <div>
          <time datetime="2009-10-10">Nov 10, 2009</time>
          <p>Go</p>      
        </div>
      </li>
      <li :class="{ invisible: isKotlin }">
        <div>
          <time datetime="2011">2011</time>
          <p>Kotlin</p>      
        </div>
      </li>
      <li :class="{ invisible: isCsharp }">
        <div>
          <time datetime="2019">2019</time>
          <ul style="list-style:none; padding: 18px 0 0 0; font-size: 8pt;">
            <li>C# 8.0</li>      
            <li>Nullable ref types</li>      
          </ul>
        </div>
      </li>
    </ul>
  </div>
</template>
