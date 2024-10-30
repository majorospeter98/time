<template>
<home-page></home-page>
</template>

<script>
import HomePage from './components/HomePage.vue'

export default {
  name: 'App',
  components: {
   HomePage
  }
}
</script>

<style>
*{
  padding:0;
  margin:0;
  box-sizing: border-box;
  scroll-behavior: smooth;
  font-family: "Poppins", sans-serif;
  font-weight: 400;
  font-style: normal;
  line-height: 1.6;

}
:root{
 scrollbar-color: red ;
 }


</style>
