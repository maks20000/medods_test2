<template>
  <div id="app" class="container">
    <simon></simon>
  </div>
</template>

<script>
import simon from "./components/simon";
export default {
  components: {
    simon
  }
};
</script>

<style>
.container {
  max-width: 1200px;
  margin: 0 auto;
}
</style>
