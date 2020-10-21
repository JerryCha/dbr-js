<template>
  <div id="app">
    <div class="navbar">
      <div class="navbar navbar-flex flex-space-between">
        <div id="logo-group">
          <img :src="require('./assets/dbr-logo.svg')" style="width: 48px;">
          <h1 style="display: inline;">Dynamic Barcode Reader</h1>
        </div>
      </div>
    </div>
    <div id="app-container">
      <Dbr :license="'t0076xQAAAAcFqMcZtCGB5D8vRTBXad8V2NKTE1zGUqtblrPsHlabRNIjl4TJKnCzvlxXMuZJFyFRJ853pRcbQcUgDexIgHm1DMIfaDAqcg=='"/>
    </div>
  </div>
</template>

<script>
import Dbr from '@/components/Dbr.vue'

export default {
  name: 'App',
  components: {
    Dbr
  }
}
</script>

<style>
.navbar {
  box-sizing: border-box;
  width: 100%;
  height: 64px;
  background-color: #2c3e50;
  color: white;
  margin-top: 0%;
}
.navbar .navbar-flex {
  display: flex;
  padding: 0rem 4rem;
}
.flex-space-between {
  align-items: center;
  justify-content: space-between;
}
#app-container {
  /* text-align: center; */
  margin: 24px 60px;
}
</style>
