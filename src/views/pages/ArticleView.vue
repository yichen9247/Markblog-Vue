<script setup>
    import Cookies from "js-cookie"
    import { ref, onMounted, watch } from 'vue'
    import "@/views/pages/assets/ArticleView.css"
    import siteConfig from "../../../docs/main.js"
    import footerview from "@/views/footerview.vue"
    import headerview from "@/views/headerview.vue"
    import { useRoute, useRouter } from 'vue-router'
    import renderMarkdown from "@/scripts/markdown.js"
    import NotFounds from '@/views/pages/NotFounds.vue'
    import ArticleFoot from "@/views/pages/ArticleFoot.vue"
    
    const postWords = ref(0);
    const route = useRoute();
    const router = useRouter();
    const htmlContent = ref('');
    const config = ref(route.meta.config);
    let filePath = '../../../docs/' + config.value.path + '/README.md';

    const checkSiteHref = () => {
        let links = document.getElementsByTagName('a');  
        for (var i = 0; i < links.length; i++) {
            if (links[i].href.includes(window.location.host)) {
                links[i].addEventListener('click', function(e) {
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
                let clone = this.parentNode.cloneNode(true);
                clone.childNodes[0].classList.add("post-block-image-full");
                this.parentNode.parentNode.appendChild(clone);
                clone.childNodes[0].focus();
                clone.childNodes[0].onblur = () => {
                    this.parentNode.parentNode.removeChild(clone)
                };
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
        },0);
    });

    onMounted(() => {
        fetch(filePath)
        .then(response => response.text())  
        .then(data => toRenderMarkdown(data))  
        .catch(error => console.error('Error fetching file:', error));
        checkSiteHref();
        setTimeout(() => checkImageClick(),1000);
    });

    watch(config,(newValue,oldValue) => {
        document.title = config.value.name + ' - ' + siteConfig.global.site_title;
    });

    document.title = config.value.name + ' - ' + siteConfig.global.site_title;
    const toRenderMarkdown = (data) => {
        if (config.value.path !== false) {
            htmlContent.value = renderMarkdown(data);
        }
        postWords.value = data.length;
    }
</script>

<template>
    <div id="profile" :style="{ animation: 'article 1s' }" v-if="config.path !== false" :data-theme="Cookies.get('darkTheme') !== 'true' ? 'default' : 'dark'">
        <headerview class="active"/>
        <div id="article-head">
            <img class="cover-bg" :src="config.image"/>
            <h1 class="cover-title">{{ config.name }}</h1>
        </div>
        <div id="markdown" class="markdown-post">
            <div class="markdown-body" :style="{ minHeight: '100%'  }">
                <section id="section" v-html="htmlContent"></section>
                <div id="post-tages">
                    <span class="tag" v-for="(tag,index) in config.tags" :key="index">
                        <span class="tag-text">{{ config.tags[index] }}</span>
                    </span>

                    <span class="tag">
                        <span class="tag-text">全文共 {{ postWords }} 字</span>
                    </span>
                </div>
                <ArticleFoot v-if="siteConfig.global.post_copyright" :config="config"/>
                <footerview />
            </div>
        </div>
    </div>
    <NotFounds v-if="config.path === false"/>
</template>