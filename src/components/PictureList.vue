<template>
  <a-list item-layout="horizontal" :grid="{ gutter: 16, xs: 1, sm: 2, md: 4, lg: 4, xl: 6, xxl: 3, xxxl: 2 }"
          :data-source="props.pictureList">
    <template #renderItem="{ item }">
      <a-list-item>
        <a-card hoverable style="width: 240px">
          <template #cover>
            <a :href="item.url" target="_blank">
              <img alt="example" class="gege" :src="item.url" />
            </a>
          </template>
          <a-card-meta :title="item.title">
          </a-card-meta>
        </a-card>
      </a-list-item>
    </template>
  </a-list>

</template>

<script setup lang="ts">
import { withDefaults, defineProps } from "vue";

interface Props {
  pictureList: any[],
}


const props = withDefaults(defineProps<Props>(), {
  pictureList: () => []
});
</script>

<style scoped>
.gege {
  object-fit: cover;
  max-width: 100%;
  height: 400px;
}
</style>
