<script setup>
  import Pace from 'pace-js'
  import { onMounted } from 'vue'
  import Cookies from "js-cookie"
  import "@/assets/paces/pace.css"
  import { RouterView } from 'vue-router'
  import siteConfig from "../docs/main.js"
  import { ElNotification } from 'element-plus'
  import { Bell } from '@element-plus/icons-vue'

  document.body.style.setProperty('--dominColor',siteConfig.style.site_dominColor);
  document.body.style.setProperty('--buttoColor',siteConfig.style.site_buttoColor);
  document.body.style.setProperty('--dotted_color1',siteConfig.style.dotted_color1);
  document.body.style.setProperty('--dotted_color2',siteConfig.style.dotted_color2);

  onMounted(() => {
    if (siteConfig.global.cook_notice) {
      if (Cookies.get('policy') !== 'true') {
        Cookies.set('policy', true ,{ expires: 30 });
        ElNotification({ icon: Bell,duration: 3000,title: '协议提醒助手',position: 'bottom-right',dangerouslyUseHTMLString: true,message: siteConfig.global.cook_message });
      }
    }
  });
</script>

<template>
  <RouterView />
</template>

<style>
  .el-icon {
    margin: 0.175em 0.1em;
    width: 0.75em!important;
    height: 0.75em!important;
  }
</style>