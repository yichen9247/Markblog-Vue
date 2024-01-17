<script setup>
    import Cookies from "js-cookie"
    import { ref, onMounted, watch } from 'vue'
    import "@/views/pages/assets/ArticleView.css"
    import headerview from "@/views/headerview.vue"
    import siteConfig from "../../../docs/main.js"
    import footerview from "@/views/footerview.vue"
    import { useRoute, useRouter } from 'vue-router'
    import renderMarkdown from "@/scripts/markdown.js"
    import FriendView from '@/views/pages/FriendView.vue'
    import ProjecView from '@/views/pages/ProjecView.vue'
    
    const route = useRoute();
    const router = useRouter();
    const htmlContent = ref('');
    const config = ref(route.meta.config);
    var filePath = '../../../docs/' + config.value.path + '/README.md';

    const checkSiteHref = () => {
        let links = document.getElementsByTagName('a');  
        for (var i = 0; i < links.length; i++) {
            if (links[i].href.includes(window.location.host)) {
                links[i].addEventListener('click',function(e) {
                    e.preventDefault();
                    e.stopPropagation();
                    router.push(new URL(this.href).pathname);
                });
            }
        };
    }

    /**
     * Markdown渲染的图片灯箱效果
    */

    const checkImageClick = () => {
        let images = document.querySelectorAll("img.post-block-image");
        for (var i = 0; i < images.length; i++) {
            images[i].addEventListener('click', function(e) {
                this.focus();
                this.classList.add("post-block-image-full");
                this.onblur = () => this.classList.remove("post-block-image-full");
            });
        };
    }

    router.beforeEach((to, from, next) => {
        next();
        setTimeout(() => {
            config.value = route.meta.config;
            if (route.meta.type === 'post') {
                filePath = '../../../docs/' + config.value.path + '/README.md';
                fetch(filePath)
                .then(response => response.text())
                .then(data => toRenderMarkdown(data))
                .catch(error => console.error('Error fetching file:', error));
                setTimeout(() => checkImageClick(),1000);
            }
        },1);
    });

    watch(config,(newValue,oldValue) => {
        document.title = config.value.name + ' - ' + siteConfig.global.site_title;
    });
    
    onMounted(() => {
        if (route.meta.type === 'post') {
            fetch(filePath)
            .then(response => response.text())
            .then(data => toRenderMarkdown(data))
            .catch(error => console.error('Error fetching file:', error));
            setTimeout(() => checkImageClick(),1000);
        }
        checkSiteHref();
    });

    document.title = config.value.name + ' - ' + siteConfig.global.site_title;
    const toRenderMarkdown = (data) => {
        if (config.path !== false) {
            htmlContent.value = renderMarkdown(data);
        }
    }
</script>

<template>
    <div id="profile" :style="{ animation: 'article 1s' }" v-if="config.path !== false" :data-theme="Cookies.get('darkTheme') !== 'true' ? 'default' : 'dark'">
        <headerview class="active"/>
        <div id="article-head">
            <img class="cover-bg" :src="config.image"/>
            <h1 class="cover-title">{{ config.name }}</h1>
        </div>
        <div id="markdown" :class="route.meta.type === 'post' ? 'markdown-post' : 'markdown-page'">
            <div class="markdown-body" :style="{ minHeight: '100%'  }">
                <section id="section" v-if="route.meta.type === 'post'" v-html="htmlContent"></section>
                <FriendView v-if="route.meta.type === 'link'"/>
                <ProjecView v-if="route.meta.type === 'repo'"/>
                <el-divider/>
                <footerview />
            </div>
        </div>
    </div>
</template>