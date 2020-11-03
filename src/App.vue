<template>
  <div id="app">
    <search-location />
</div>
</template>

<script>
import SearchLocation from './components/SearchLocation';
export default {
  components: {SearchLocation},
  name: 'App'
}
</script>

<style>
body {
  background-color: #20222a !important;
  color: #dadada !important;
  font-family: "Lato",sans-serif !important;
}
</style>
