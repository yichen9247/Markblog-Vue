<script setup>
    import "./assets/CarouView.css"
    import siteConfig from "../../../docs/main.js"
</script>

<template>
    <div class="carousel-chart" v-if="siteConfig.global.index_carousel">
        <el-carousel height="155px" :interval="siteConfig.global.carousel_interval">
            <el-carousel-item v-for="(item,index) in siteConfig.carousel" :key="index">
                <a class="carousel-item" :href="item.href" :title="item.title">
                    <img class="carousel-item-image" :src="item.image" :alt="item.title">
                    <div class="carousel-item-cover">
                        <p class="item-title">{{ item.title }}</p>
                    </div>
                </a>
            </el-carousel-item>
        </el-carousel>
    </div>
</template>