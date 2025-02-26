<template>
  <ul flex flex-col>
    <!-- 标题 -->
      <slot></slot>
      <!-- 循环buttonList -->
      <li v-for="(item, index) in buttonList" :key="index" m-1>
        <!-- fuc -->
        <button v-if="item.clickFuc" flex items-center w-full h-16 p-1 bg-deep-0 rounded-lg hover:bg-deep-2
          @click="item.clickFuc(item)" :class="item.isShow && 'bg-deep-3'">
          <component :is="item.icon" v-if="item.icon" w-8 mx-1 />
          <span sm font-2 mx-2>{{ item.name }}</span>
        </button>
        <!-- child MinPopover-->
        <MinPopover v-if="item.child" v-model="item.isShow">
          <ShowHidden>
            <div v-show="item.isShow" flex flex-col relative left-4 w-55 p-2 rounded-lg bg-deep-0 >
              <RouterLink :to=fun1Router.url v-for="fun1Router in item.child" :key="fun1Router.name" hover:bg-deep-2>
                {{ fun1Router.name }}
              </RouterLink>
            </div>
          </ShowHidden>
        </MinPopover>
      </li>
    </ul>
</template>
 <script setup lang='ts'>
/**
 * @version      :0.0
 * @author       :itild
 * @File         :CollapseBase.vue
 * @Time         :2025/02/26 20:46:07
 * @Email        :geolifestudy@gmail.com
 * @Copyright    :(C) 2025 itild. All rights reserved.
 * @parent       :todo
 * @summary      :简介
 * @description  :标准模板
 * 
 * 1 import 2 type 3 class 4 obj 5 vue
 * 6 watch 7 fuc 8 fetch 9 code_block
 */
////////////////////1_import_______////////////////////
defineProps(['buttonList'])
////////////////////2_type_________////////////////////
////////////////////3_class________////////////////////
////////////////////4_obj__________////////////////////
////////////////////5_vue__________////////////////////
onMounted(() => {
  //console.log('onMounted_debugger')
 })
////////////////////6_watch________////////////////////
////////////////////7_fuc__________////////////////////
////////////////////8_fetch________////////////////////
////////////////////9_code_block___////////////////////
////////////////////_______________////////////////////
</script>
<style scoped>
</style>