<script setup>
    import { useRouter } from 'vue-router'
    import { ElMessage } from 'element-plus'
    import "@/views/pages/assets/HomeArtic.css"
    import { ref, onMounted, watch } from 'vue'
    import siteConfig from "../../../docs/main.js"
    import NoData from "@/views/widgets/NoData.vue"
    import AsideView from "@/views/pages/AsideView.vue"
    import CarouView from "@/views/pages/CarouView.vue"

    let posts = [];
    const router = useRouter();
    let links = document.getElementsByTagName('a');
    siteConfig.actives.map((item) => {
        if (item.isFixed) {
            posts.unshift(item);
            let index = posts.indexOf(item);
            let removedElements = posts.splice(index, 1);
            posts.push(removedElements[0]);
        } else {
            posts.push(item);
        }
    });
    
    const loadMorePost = () => {
        if (postArray.value.length < posts.length) {
            postIndex.value = postIndex.value + 8;
            setTimeout(() => checkSiteHref(),100);
        } else ElMessage({ message: '没有更多数据了！', type: 'warning' });
    }

    const postIndex = ref(8);
    const postArray = ref(posts.reverse().slice(0,postIndex.value));

    watch(postIndex,(newValue,oldValue) => {
        postArray.value = posts.slice(0,postIndex.value);
    });

    const checkSiteHref = () => {
        links = document.getElementsByTagName('a');
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

    onMounted(() => {
        links = document.getElementsByTagName('a');
    });
</script>

<template>
    <CarouView/>
    <div id="body-content">
        <div class="content-main">
            <ul class="post-list-default post-list" v-if="siteConfig.global.post_listmode === 0">
                <el-space wrap :style="{ width: '100%' }" :size="8">
                    <li class="post-list-item" v-for="(item,index) in postArray" :key="index">
                        <a class="post-cover" :href="`/docs/${item.path}`">
                            <img class="post-image" :src="item.config.image" :alt="item.config.name" draggable="false">
                            <div class="post-cover">
                                <div class="post-info">
                                    <p class="post-title">
                                        <span class="post-fixed" v-if="item.isFixed">置顶</span>
                                        {{ item.config.name }}
                                    </p>
                                    <p class="post-desce">{{ item.config.desc }}</p>
                                </div>
                            </div>
                        </a>
                    </li>
                </el-space>

                <NoData v-if="postArray.length === 0" :description="'博主还没有发布过任何文章哦！'"/>
            </ul>
    
            <ul class="post-list-signal post-list" v-if="siteConfig.global.post_listmode === 1">
                <el-space wrap :style="{ width: '100%' }" :size="8">
                    <li class="post-list-item" v-for="(item,index) in postArray" :key="index">
                        <a class="post-cover" :href="`/docs/${item.path}`">
                            <img class="post-image" :src="item.config.image" :alt="item.config.name" draggable="false">
                            <div class="post-cover">
                                <div class="post-info">
                                    <p class="post-title">
                                        <span class="post-fixed" v-if="item.isFixed">置顶</span>
                                        {{ item.config.name }}
                                    </p>
                                    <p class="post-desce">{{ item.config.desc }}</p>
                                </div>
                            </div>
                        </a>
                    </li>
                </el-space>
            </ul>

            <el-button class="load-more-button" size="large" :color="siteConfig.style.site_buttoColor" plain @click="loadMorePost" v-if="postArray.length < posts.length">加载更多</el-button>
        </div>
        <AsideView/>
    </div>
</template>