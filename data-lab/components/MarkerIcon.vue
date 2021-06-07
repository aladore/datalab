<template>
  <section>
  <div class="marker_grabbable" :style="{left: -sliderDistance + 'px', top: -sliderDistance + 'px', right: -sliderDistance + 'px', bottom: -sliderDistance + 'px'}"></div>
  <div class="marker float">
    <svg
      x="0px"
      y="0px"
      :width="iconSize"
      :height="iconSize"
      viewBox="0 0 485.213 485.212"
      style="enable-background:new 0 0 485.213 485.212;"
      xml:space="preserve"
    >
      <g :fill="markerColor">
        <path
          d="M242.606,0C142.124,0,60.651,81.473,60.651,181.955s151.631,303.257,181.956,303.257
                 c30.326,0,181.955-202.775,181.955-303.257S343.089,0,242.606,0z M242.606,303.257c-66.9,0-121.302-54.433-121.302-121.302
                 S175.706,60.651,242.606,60.651c66.902,0,121.302,54.435,121.302,121.304S309.509,303.257,242.606,303.257z"
        ></path>
      </g>
    </svg>
    <div class="shadow"></div>
  </div>
  </section>
</template>
<script>
    export default {
        name: "App",
        props: {
            iconSize: Number,
            markerColor: String,
            sliderDistance: Number,
        }
    };
</script>



<style lang="scss" scoped>
  .shadow {
    width: 13px;
    height: 5px;
    border-radius: 50%;
    margin: 5px auto 0 auto;
  }
  .marker_grabbable {
    position: absolute;
    border-radius: 50%;
    background: rgba(255,255,255, 0);
    cursor: grab;
    &:active {
      cursor: grabbing;
      transform: translateY(-8px);
      .shadow {
        opacity: 1;
      }
    }
  }
  .marker {
    display: flex;
    /* font-size: 0; */
    align-items: center;
    flex-direction: column;
    position: absolute;
    left: 0px;
    right: 0;
    top: -21px;
    bottom: 0;
    cursor: grab;
    transition: 0.2s ease-in all;
    &:active {
      cursor: grabbing;
      transform: translateY(-8px);
      .shadow {
        opacity: 1;
      }
    }
  }
  .float {
    svg {
      //fill: #dd2c00;
      /*animation: hover 1.3s ease-in-out infinite alternate;*/
    }

    .shadow {
      opacity: 0;
      transition: 0.2s ease-in all;
      filter: blur(1px);
      background: rgba(0, 0, 0, 0.5);
      width: 13px;
      height: 5px;
      animation: shadow2 1.3s ease-in-out infinite alternate;
    }

    @keyframes hover {
      from {
        transform: translate3d(0, 0, 0);
      }
      to {
        transform: translate3d(0, -6px, 0);
      }
    }

    @keyframes shadow2 {
      from {
        transform: scale(0.95) translate3d(0, 0, 0);
      }
      to {
        transform: scale(1.2) translate3d(0, 0, 0);
      }
    }
  }
</style>
