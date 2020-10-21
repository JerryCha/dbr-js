<template>
  <div class="result-card">
    <p class="format-text">Format: {{ format }}</p>
    <p class="result-text">Text: {{ text }}</p>
  </div>
</template>

<script>
export default {
  name: 'result-card',
  props: {
    format: String,
    text: String
  }
}
</script>

<style scoped>
.result-card {
  min-width: 300px;
  max-width: 500px;
  min-height: 100px;
  border: 1.5px solid #2c3535c4;
  border-radius: 8px;
  padding: 12px 24px;
  margin-bottom: 1rem;
  margin-right: 1rem;
}
.format-text {
  font-size: 1.6rem;
}
.result-text {
  font-size: 1.6rem;
  /* font-weight: 700; */
}
</style>