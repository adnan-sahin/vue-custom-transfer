<template>
  <div id="app">
    <VueCustomTransfer />
  </div>
</template>

<script>
import VueCustomTransfer from './components/VueCustomTransfer';
export default {
  name: 'App',
  components: {
    VueCustomTransfer,
  },
};
</script>

<style>
#app {
  font-family: 'Roboto', sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
