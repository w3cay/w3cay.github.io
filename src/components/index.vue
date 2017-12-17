<template>
  <div class="hello">
    <h1>{{ title }}</h1>
    <ul class="items">
      <li><a href="https://github.com/w3cay/blog/issues"
          onclick="_czc.push(['_trackEvent', '链接', '点击', '博客','1','blog']);"
          target="_blank">博客</a></li>/
      <li><a href="https://github.com/w3cay"
          onclick="_czc.push(['_trackEvent', '链接', '点击', '代码','1','github']);"
          target="_blank">代码</a></li>/
      <li><a href="http://weibo.com/633012550"
          onclick="_czc.push(['_trackEvent', '链接', '点击', '微博','1','weibo']);"
          target="_blank">微博</a></li>/
      <li><a href="https://www.zhihu.com/people/bright-chen"
          onclick="_czc.push(['_trackEvent', '链接', '点击', '知乎','1','zhihu']);"
          target="_blank">知乎</a></li>
      <!-- <li><a href="http://w3cay.com/resume/" target="_blank">简历</a></li> -->
    </ul>
  </div>
</template>
<script>
/* global window,document */
/* eslint no-param-reassign: ["error", { "props": false }]*/

export default {
  name: 'index',
  data() {
    return {
      title: '安望云海',
    };
  },
  computed: {},
  components: {},
};

</script>
<style lang='scss'>
.items {
  a {
    text-decoration: none;
    display: inline-block;
  }
  li {
    // &::after {
    //   content: '';
    //   display: inline-block;
    //   border-top: 1px solid #000;
    //   width: 100%;
    //   height: 1px;
    //   line-height: 0;
    // }
  }
}

</style>
