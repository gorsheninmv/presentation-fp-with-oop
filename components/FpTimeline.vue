<style scoped>

    .timeline { 
     
        /* set a variable for the color */
        --timeline-color: #ff6600;
      
        position: relative;
        list-style: none;
        display: inline-flex;
        flex-wrap: nowrap;
        margin: 0;
        padding: 0;
        height: 240px;
    }  /* set here the height of the timeline */


    /* middle line */
    .timeline:before {
        content: "";
        position: absolute;
        top: calc(50% - 1px);
        width: 100%;
        height: 2px;
        background: var(--timeline-color); }


    .timeline > li { 
        min-width: 110px;
        align-self: flex-start; 
      }

    .timeline > :first-child {
      margin-left: 70px;
    }

    .timeline > :last-child {
      margin-right: 570px;
      margin-left: 50px;
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
        margin-left: 54px;
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
  <div style="font-size: 10pt; transform: scale(0.75); margin-left: -100px; margin-bottom: 30px;">
    <ul class="timeline">
      <li>
        <div>
          <time datetime="1983">1983</time>
          <p>Standard ML</p>      
        </div>
      </li>
      <li>
        <div>
          <time datetime="1990">1990</time>
          <p>Haskell</p>      
        </div>
      </li>
      <li>
        <div>
          <time datetime="1996">1996</time>
          <p>OCaml</p>      
        </div>
      </li>
      <li>
        <div>
          <time datetime="2005">2005</time>
          <p>F#</p>      
        </div>
      </li>
    </ul>
  </div>
</template>
