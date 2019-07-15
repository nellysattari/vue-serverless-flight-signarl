<!-- <img src="../assets/flight2.jpg" class="img-responsive" alt="Responsive image"> -->
<template>
  <div class="home container text-center">
       <Flights/>
  </div>
</template>

<script>
// @ is an alias to /src
import Flights from '@/components/Flights.vue'

export default {
  name: 'home',
  components: {
    Flights
  }
}
</script>
<style  scoped>
@import "https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css";
@import "https://use.fontawesome.com/releases/v5.2.0/css/all.css";
.img-responsive{
  width: 100%
}
</style>
