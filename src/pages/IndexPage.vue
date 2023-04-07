<template>
<!--  这里的下面这个v-model:value作为监听searchText，当searchText发生改变的时候，当OnSearch点击的时候，就会改变searchText-->
  <div class="index-page">
    <a-input-search
      v-model:value="searchText"
      placeholder="input search text"
      enter-button="Search"
      size="large"
      @search="onSearch"
    />

    <MyDivider />
    <a-tabs v-model:activeKey="activeKey" @change="onTabChange">
      <a-tab-pane key="post" tab="文章">
        <PostList :post-list="postList"/>
      </a-tab-pane>
      <a-tab-pane key="picture" tab="图片">
        <PictureList :picture-list="pictureList"/>
      </a-tab-pane>
      <a-tab-pane key="user" tab="用户">
        <UserList :user-list="userList"/>
      </a-tab-pane>
    </a-tabs>
  </div>
</template>

<script setup lang="ts">
import { ref, watchEffect } from "vue";
import PostList from "@/components/PostList.vue";
import PictureList from "@/components/PictureList.vue";
import { useRoute, useRouter } from "vue-router";
import MyDivider from "@/components/MyDivider.vue";
import UserList from "@/components/UserList.vue";
import MyAxios from "@/plugins/MyAxios";
import { debounce, throttle } from "lodash";


const pictureList = ref([]);
const postList = ref([]);
const userList = ref([]);
const router = useRouter();
const route = useRoute();
// 搜索的类别
const activeKey = route.params.category;
const searchText = ref(route.query.text);


const loadData = (params: any)=>{
  // alert(111);
  const { type } = params;
  const query = {
    ...params,
    searchText: params.text,
  };
  MyAxios.post("/search/all",query).then((res: any)=>{
    if(type==="picture"){
      pictureList.value = res.dataList;
    }else if(type==="post"){
      postList.value = res.dataList;
    }else if(type==="user"){
      userList.value = res.dataList;
    }else{
      userList.value = res.userList;
      postList.value = res.postList;
      pictureList.value = res.pictureList;
    }
  });
}
// const throttledLoadData => throttle((loadDataParms:any)=>{
//   loadData(loadDataParms);
// },500) as (loadDataParms:any)=> void;

const throttledLoadData = throttle((params: any) => {
  loadData(params);
}, 500) as (params: any) => void;

const debouncedAndThrottle = debounce((params: any)=>{
  throttledLoadData(params);
},500);

const initSearchParam = {
  type: "post",
  text: "",
  pageSize: 10,
  pageNum: 1
};
const searchParams = ref(initSearchParam);
//使用路由进行监听输入的内容，并且同步到searchParam中
watchEffect(()=>{
  // 默认第一次发送的时候发送的是post请求
  const categories = route.params.category || "post";
  searchParams.value = {
    ...initSearchParam,
    text: route.query.text,
    type: categories
  }as any
  debouncedAndThrottle(searchParams.value);
  // loadData(searchParams.value);
});


const onSearch = (value: string) => {
  router.push({
    query: {
      ...searchParams.value,
      text: value,
    }
  });
};


const onTabChange = (key : string) => {
  router.push({
      path: `/${key}`,
      query: searchParams.value,
    });
}
</script>
