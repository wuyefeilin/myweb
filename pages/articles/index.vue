<template>
<div class="D-big">
  <go-back routerName="/" title="文章列表"></go-back>
  <div class="article-box">
    <reject v-if="blogList==null"></reject>
    <div v-else class="article-item" v-for="(item,index) in blogList" :key="index">
      <div @click="goArticle(item[0])" class="title"> 《 {{item[0]}} 》</div>
      <div class="flex-row-between">
        <!-- 最新编辑时间 -->
        <div class="icon-time">{{item[1]}}</div>
        <!-- 阅读次数 -->
        <div class="icon-read"></div>
      </div>
    </div>
  </div>
</div>
</template>

<script>
import SERVER from '~/assets/server/api.js';
import goBack from '~/components/mobile/back.vue';
import reject from "~/components/mobile/reject";
export default {
  transition: 'article',
  components: {
    goBack,
    reject
  },
  data() {
    return {
      blogList: null
    }
  },
  methods: {
    // 获得文章列表
    getList() {
      SERVER.getAticleList().then((data) => {
        this.blogList = data.data;
        data.data.forEach(element => {

          // 处理T型时间
          var jsonTime = new Date(element[1]).toJSON();
          var time = new Date(+new Date(jsonTime) + 8 * 3600 * 1000).toISOString().replace(/T/g, ' ').replace(/\.[\d]{3}Z/, '')
          element[1] = time;
        });
      }).catch((err) => {
        this.$Message.error("(╥╯﹏╰╥)ง请求失败，改bug去~");
      })
    },
    goArticle(title) {
      // 跳转去查看文章详情
      this.$router.push(`/articles/article?title=${title}`);
    }
  },
  mounted() {
    this.getList();
  }
}
</script>

<style lang="less" scoped>
@import "~assets/css/mobile/base.less";
.D-big {
  display: fixed;
  height: 100%;
  width: 100%;
  min-width: 350px;
  max-width: 1024px;
  margin: 0 auto;
  overflow: hidden;
}

.article-box {
  padding: 0 @distansBig;
}

.article-item {
  padding: @distansBig @distansSmall;
  margin: @distansBig; // x方向重复背景
  background-image: url("~static/mobile/icon/cao.png");
  background-position: bottom;
  background-repeat: repeat-x;
}

// 伪类图标
.icon-time {
  line-height: @distansBig;
  &:before {
    content: " ";
    display: inline-block;
    vertical-align: middle;
    margin-right: @distansSmall;
    .icon(@width: 15px);
    background: url(@img-time) no-repeat;
    background-size: 100%;
  }
}

.icon-read {
  line-height: @distansBig;
  &:before {
    content: " ";
    display: inline-block;
    vertical-align: middle;
    margin-right: @distansSmall;
    .icon(@width: 15px);
    background: url(@img-read) no-repeat;
    background-size: 100%;
  }
}
</style>
