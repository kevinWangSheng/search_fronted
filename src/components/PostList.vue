<template>
  文章列表
  <img :src="gege" class="gege" />
  <a-list item-layout="horizontal" :data-source="props.postList">
    <template #renderItem="{ item }">
      <a-list-item>
        <a-list-item-meta >
          <template #title>
            <a :href="item.url">
              <span v-html="highlight(item.title)"></span>
            </a>
          </template>
          <template #description>
            <span v-html="highlight(item.content)"></span>
          </template>
          <template #avatar>
            <a-avatar :src="gege" />
          </template>
        </a-list-item-meta>
      </a-list-item>
    </template>
  </a-list>
</template>

<script setup lang="ts">
import gege from "../assets/gege.png";
import { withDefaults, defineProps } from "vue";

function highlight(text: string) {
  // 替换 <em> 标签中的内容为搜索关键字
  const highlightedText = text.replace(
    /<em>(.*?)<\/em>/g,
    `<span class="highlight">$1</span>`
  );
  // 添加 CSS 样式
  const css =
    '.highlight { background-color: yellow; font-weight: bold; }';
  return `<style>${css}</style>${highlightedText}`;
}


interface Props {
  postList: any[],
}

const props = withDefaults(defineProps<Props>(), {
  postList: () => [],
  results: {
    type: Array,
    default: () => [],
  },
  query: {
    type: String,
    default: "",
  },
});


</script>

<style scoped>
.gege {
  width: 20px;
}
.highlight {
  background-color: red;
  font-weight: bold;
}
</style>
