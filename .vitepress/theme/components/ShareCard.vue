<template>
  <div class="shareCard">
    <h1 class="title">
      <span>欢迎来到 Devin Zhong 的博客</span>
    </h1>
    <p class="description">
      心有猛虎，细嗅蔷薇。
    </p>
  </div>
</template>

<script lang="ts" setup></script>
<style scoped>
.shareCard {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 0 15px;
  text-align: center;
}
.title {
  display: flex;
  align-items: center;
  font-size: 2.2em;
  font-weight: bold;
}
.description {
  margin: 1em 0;
}
</style>
