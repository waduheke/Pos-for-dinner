<template>
  <div id="app" class="clearfix">
    <leftNav ></leftNav>
    <router-view style="float: letf"></router-view>
  </div>
</template>

<script>
import leftNav from '@/components/common/leftNav'
export default {
  name: 'App',
  components: {
    leftNav
  }
}
</script>

<style>
.clearfix:after{
   content: "020";
   display: block;
   height: 0;
   clear: both;
   visibility: hidden;
}
</style>
