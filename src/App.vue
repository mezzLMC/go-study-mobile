<template>
  <hero-banner />
  <reservation />
</template>

<script>
import "./component.css"
import HeroBanner from "./HeroBannerPage.vue"
import Reservation from "./reservationPage.vue"

export default {
  components: {
      "hero-banner": HeroBanner,
      "reservation": Reservation,
  }
}
</script>

<style>
*{
    --purple-dark: #6B4EFF;
    --purle-primary: #C6C4FF;
    --purple-light: #9990FF;
    --shadow-small: 0px 0px 1px rgba(20, 20, 20, 0.04), 0px 0px 8px rgba(20, 20, 20, 0.08);
    --shadow-medium: 0px 0px 1px rgba(20, 20, 20, 0.08), 0px 1px 8px 2px rgba(20, 20, 20, 0.08);
    --shadow-large: 0px 1px 24px 8px rgba(20, 20, 20, 0.08);
}
#app{
  width: 100%;
  height: 100%;
  position: absolute;
  left: 0;
  top: 0;
}
</style>
