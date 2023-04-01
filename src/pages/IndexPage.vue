<template>
  <div class="index-page">
    <a-input-search
      v-model:value="searchParams.text"
      placeholder="input search text"
      enter-button="Search"
      size="large"
      @search="onSearch"
    />
    <MyDivider />
    <a-tabs v-model:activeKey="activeKey" @change="onTabChange">
      <a-tab-pane key="post" tab="文章">
        <PostList :post-list="postList" />
      </a-tab-pane>
      <a-tab-pane key="picture" tab="图片">
        <PictureList />
      </a-tab-pane>
      <a-tab-pane key="user" tab="用户">
        <UserList :user-list="userList" />
      </a-tab-pane>
    </a-tabs>
  </div>
</template>

<script setup lang="ts">
import { ref, watchEffect } from "vue";
import PostList from "@/components/PostList.vue";
import PictureList from "@/components/PictureList.vue";
import UserList from "@/components/UserList.vue";
import MyDivider from "@/components/MyDivider.vue";
import { useRoute, useRouter } from "vue-router";
import fusoAxios from "@/plugins/myAxios";

const postList = ref([]);
fusoAxios.post("/post/list/page/vo", {}).then((res: any) => {
  postList.value = res.records;
});

const userList = ref([]);
fusoAxios.post("/user/list/page/vo", {}).then((res: any) => {
  userList.value = res.records;
});

const router = useRouter();
const route = useRoute();
const activeKey = route.params.category;

const initSearchParams = {
  text: "",
  pageSize: 10,
  pageNum: 1,
};
const searchParams = ref(initSearchParams);

watchEffect(() => {
  searchParams.value = {
    ...initSearchParams,
    text: route.query.category,
  } as any;
});

const onSearch = (value: string) => {
  alert(value);
  // 搜索的内容填充到url上,当用户刷新页面时,能够从url还原之前的搜索状态
  router.push({
    query: searchParams.value,
  });
};
// 点击tabs栏时,搜索的内容和tabs对应的url共同填充到url上,当用户刷新页面时,能够从url还原之前的搜索状态
const onTabChange = (key: string) => {
  router.push({
    path: `/${key}`,
    query: searchParams.value,
  });
};
</script>
