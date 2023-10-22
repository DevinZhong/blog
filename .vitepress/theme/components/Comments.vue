<template>
  <div id="gitalk-container"></div>
</template>
<script lang="ts" setup>
import "gitalk/dist/gitalk.css";
import Gitalk from "gitalk";
import { onContentUpdated, useRouter } from "vitepress";

// const { route, go } = useRouter();
function deleteChild() {
  const element = document.querySelector("#gitalk-container");
  let child = element?.lastElementChild;
  while (child) {
    element?.removeChild(child);
    child = element?.lastElementChild;
  }
}
onContentUpdated(() => {
  // reset gittalk element for update
  deleteChild();
  const gitalk = new Gitalk({
    clientID: "4622f8e056618cdbc141",
    clientSecret: "e6c70b40b23ca305fee26bcd4780dfbab5b2997a",
    repo: "blog",
    owner: "DevinZhong",
    admin: ["DevinZhong"],
    id: location.pathname.substring(0, 50), // Ensure uniqueness and length less than 50
    language: "zh-CN",
    labels: ['Gitalk'],
    distractionFreeMode: true, // Facebook-like distraction free mode
  });
  gitalk.render("gitalk-container");
});
</script>
<style scoped></style>
