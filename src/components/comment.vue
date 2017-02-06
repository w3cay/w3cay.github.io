<template>
  <div class="ds-thread" id="duoshuo" data-title="安望云海"
   data-thread-key="1"


   data-url="http://w3cay.com"></div>
</template>

<script>
/* global window,document */
/* eslint no-param-reassign: ["error", { "props": false }]*/

window.onload = () => {
  const ele = document.querySelector('#ds-thread');
  if (ele && ele.innerText.indexOf('990015') > -1) {
    document.querySelector('#ds-thread').style.display = 'none';
  }
};

window.duoshuoQuery = { short_name: 'brightchen' };
(() => {
  const ds = document.createElement('script');
  ds.type = 'text/javascript';
  ds.async = true;
  ds.src = `${document.location.protocol === 'https:' ? 'https:' : 'http:'}//static.duoshuo.com/embed.js`;
  ds.charset = 'UTF-8';
  (document.getElementsByTagName('head')[0]
   || document.getElementsByTagName('body')[0]).appendChild(ds);
})();

export default {
  name: 'comment',
  data() {
    return {
    };
  },
  computed: {
  },
  components: {
  },
};
</script>

<style lang='scss'>
#ds-thread {
  width: 90%;
  max-width: 500px;
  margin: auto;

  div#ds-reset .ds-meta {
    padding: 20px 0 30px;
    text-align: center;
    a.ds-like-thread-button {
      margin-right: 0;
    }
  }

  // .ds-avatar {
  //   border-radius: 50%;
  // }
  #ds-reset span.ds-like-panel {
    display: block;
    width: 100%;
    margin: 9px 0;
  }
  .ds-powered-by {
    visibility: hidden;
  }

}
</style>
