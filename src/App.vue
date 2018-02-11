<template>
  <div id="app">
    <div id="robot"
      v-on:mouseover="eyesMove($event)"
      v-on:mouseout="eyesReset()"
      :class="{'move': status.foot.lift }">
      <div class="bubble"
        v-show="status.words">
        {{status.words}}
      </div>
      <header class="head">
        <i class="mast"></i>
        <i class="eyes left"
          @click="actions('say',{text: '戳到我的眼睛了！', mood: 'angry'})">
        <i class="dot" :style="eyeStyle"></i>
        </i>
        <i class="eyes right"
          @click="actions('say',{text: '戳到我的眼睛了！', mood: 'angry'})">
        <i class="dot" :style="eyeStyle"></i>
        </i>
        <i class="mouse"
          @click="actions('say',{text: '信不信我咬你~', mood: 'angry'})">{{status.mouse}}</i>
      </header>
      <section class="body">
        <i class="lingjie"></i>
        <i class="collar"></i>
        <i class="fastener"></i>
        <i class="fastener"></i>
      </section>
      <section class="arms">
        <i class="arm left"
          :style="armLeftStyle"
          @click="armsMove('left', -30, 1000)"></i>
        <i class="arm right"
          :style="armRightStyle"
          @click="armsMove('right', -30, 1000)"></i>
      </section>
      <footer class="footer"
        @click="walk()">
        <i class="foot left"
          :class="{'left-lift': status.foot.lift }"></i>
        <i class="foot right"
          :class="{'right-lift': status.foot.lift }"></i>
      </footer>
    </div>
    <footer class="copyright">
      <p>© 安望云海 ❤️</p>
      <p class="host">Hosted by <a href="https://pages.coding.me"
          style="font-weight: bold">Coding Pages</a></p>
    </footer>
  </div>
</template>
<script>
/* global window,document */
/* eslint no-param-reassign: ["error", { "props": false }]*/
import Index from './components/index.vue';

export default {
  name: 'app',
  data() {
    return {
      status: {
        mouse: '-',
        words: '',
        arm: {
          left: 30,
          right: 30,
        },
        eye: {
          top: 3,
          left: 3,
        },
        foot: {
          lift: false,
        },
      },
      parms: {
        eye: {
          middle: 3,
          high: 5,
          low: 1,
        },
        words: 'Hi，你好！。我是小安。欢迎访问',
      },
    };
  },
  computed: {
    armLeftStyle() {
      return `transform: translateX(-61%) scaleX(-1) rotate(${this.status.arm.left}deg)`;
    },
    armRightStyle() {
      return `transform: translateX(61%) rotate(${this.status.arm.right}deg)`;
    },
    eyeStyle() {
      return `left: ${this.status.eye.left}px; top: ${this.status.eye.top}px`;
    },
    wordList() {
      return this.parms.words.split(/。/);
    },
  },
  components: {
    Index,
  },
  created() {
    this.say();
  },
  methods: {
    // 动作组合
    actions(cmd, options) {
      switch (cmd) {
        case 'say':
          this.status.words = options.text;
          if (options.mood) {
            this.actions(options.mood);
          } else {
            this.actions();
          }
          break;
        case 'smile':
          this.status.mouse = '︶';
          break;
        case 'sad':
          this.status.mouse = '︵';
          break;
        case 'amazed':
          this.status.mouse = '■';
          break;
        case 'dull':
          this.status.mouse = '•';
          break;
        case 'angry':
          this.status.mouse = '～';
          break;
        default:
          this.status.mouse = '-';
          break;
      }
    },
    /**
     * 说话
     */
    say() {
      setTimeout(() => {
        this.status.words = this.wordList.shift();
        this.status.mouse = '︶';
        setTimeout(() => {
          this.status.words = '';
          if (this.wordList.length !== 0) {
            this.say();
          }
        }, 3000);
      }, 500);
    },
    /**
     * @param side 左或右
     * @param deg 旋转角度
     * @param msec 动作耗时（毫秒）
     */
    armsMove(side, deg, msec) {
      const ntick = 100;
      let clock = 0;
      const timer = setInterval(() => {
        this.status.arm[side] += deg / ntick;
        clock += 1;
        if (clock === 100) {
          clearInterval(timer);
        }
      }, msec / ntick);
    },
    eyesMove(e) {
      if (e) {
        const X = e.clientX - (window.innerWidth / 2) > 0 ?
          this.parms.eye.high : this.parms.eye.low;

        const Y = (
            document.querySelector('#robot').offsetTop +
            document.querySelector('.head').offsetTop +
            document.querySelector('.eyes').offsetTop
          ) - e.clientY > 0 ?
          this.parms.eye.low : this.parms.eye.high;
        this.status.eye.left = X;
        this.status.eye.top = Y;
      }
    },
    walk() {
      this.status.foot.lift = !this.status.foot.lift;
      if (this.status.foot.lift) {
        this.actions('amazed');
      } else {
        this.actions('');
      }
    },
    eyesReset() {
      this.status.eye.left = this.parms.eye.middle;
      this.status.eye.top = this.parms.eye.middle;
    },
  },
};

</script>
<style lang='scss'>
$body-border: #f0f0f0;
$body-color: #98d43a;

html,
body {
  width: 100%;
  height: 100%;
  overflow: hidden;
  margin: 0; // background: url(~/static/img/science.jpg);
}

h1,
h2 {
  font-weight: normal;
  font-size: 36px;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #3f9c71;
  font-size: 18px;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #5d6063;
  user-select: none;
  margin-top: 100px;
}

#robot {
  width: 200px;
  height: 250px; // border: 1px solid #f0f0f0;
  margin: auto;
  padding: 10px;
  position: relative;
  cursor: pointer;
  -webkit-tap-highlight-color: transparent;
  &.move {
    animation: move 3s linear infinite;
  }

  i {
    font-style: normal;
  }

  .bubble {
    display: inline-block;
    position: absolute;
    border: 2px dotted #6786e0;
    padding: 3px 8px;
    border-radius: 10px;
    font-size: 14px;
    width: 100px; // max-height: 50px;
    left: 170px;
    text-align: center;
    z-index: 20;
    background-color: #fff;

    &:after {
      content: '';
      display: inline-block;
      border: 2px dotted #6786e0;
      width: 8px;
      border-top: 0;
      border-right: 0;
      height: 7px;
      position: absolute;
      top: 9px;
      background-color: #fff;
      left: -6px;
      transform: rotate(40deg);
    }
  }

  .head {
    border: 2px solid #d0b8b8;
    margin: 40px auto 0;
    height: 50px;
    width: 80px;
    border-radius: 10px;
    position: relative;
    background-color: #f1f5f7;

    .mast {
      display: inline-block;
      width: 0;
      height: 20px;
      border: 2px dotted #3d9db5;
      position: relative;
      top: -25px;
      &:after {
        content: '';
        display: block;
        width: 6px;
        height: 6px;
        border: 1px solid #bba7ad;
        position: relative;
        top: -6px;
        left: -4px;
        background-color: #8bf939;
        border-radius: 50%;
        animation: light-blink 3s infinite;
      }
    }

    @keyframes light-blink {
      0% {
        background-color: #8bf939;
      }
      50% {
        background-color: #ab7b8a;
      }
      100% {
        background-color: #8bf939;
      }
    }

    .eyes {
      display: inline-block;
      position: absolute;
      top: 13px;
      transform: translateY(-50%);
      width: 10px;
      height: 10px;
      overflow: hidden;
      border: 2px solid #dac3c3;
      border-radius: 50%;
      animation: blink 3s infinite;

      &.left {
        left: 20px;
      }

      &.right {
        right: 20px;
      }

      .dot {
        display: inline-block;
        width: 0px;
        border: 2px solid #000;
        position: absolute;
        border-radius: 50%;
        top: 3px;
        left: 3px;
      }
    }

    &:hover .eyes {
      animation: none;
    }

    @keyframes blink {
      0% {
        height: 10px;
      }
      5% {
        height: 0px;
      }
      10% {
        height: 10px;
      }
      100% {
        height: 10px;
      }
    }


    .mouse {
      display: block;
      position: absolute;
      width: 15px;
      left: 50%;
      font-weight: bold;
      line-height: 20px;
      transform: translateX(-50%);
    }
  }

  .body {
    border: 2px solid #796a6a;
    background-color: #7c898e;
    ;
    width: 100px;
    height: 100px;
    border-radius: 10px;
    margin: 2px auto 0;
    position: relative;
    z-index: 3;
    overflow: hidden;

    .lingjie {
      width: 30px;
      height: 28px;
      background: url(./assets/lingjie.png) no-repeat 0 0 / 100% 100%;
      display: block;
      position: absolute;
      left: 50%;
      transform: translateX(-50%);
      z-index: 9;
      top: -6px;
    }

    .collar {
      display: block;
      width: 50px;
      height: 50px;
      transform: rotate(45deg);
      margin: -40px auto 30px;
      border: 1px solid #000;
      background-color: #fff;
    }

    .fastener {
      display: block;
      width: 5px;
      height: 5px;
      border: 1px solid #f0f0f0;
      margin: 20px auto;
      background-color: #a28686;
      border-radius: 50%;
    }
  }

  .arms {
    .arm {
      z-index: 14;
      background-color: #cadeab;
      top: 120px;
      width: 60px;
      height: 10px;
      display: inline-block;
      border: 2px solid #796a6a;
      position: absolute;
      border-left: 0;

      &:after {
        content: '';
        display: inline-block;
        width: 10px;
        height: 13px;
        border: 2px solid #796a6a;
        position: absolute;
        right: -10px;
        border-radius: 10px;
        top: -3px;
        background-color: #fff;
      }
    }

    .left {
      transform-origin: 0 0;
      transform: translateX(-70%) scaleX(-1) rotate(45deg);
    }

    .right {
      transform-origin: 0 0;
      transform: translateX(70%) rotate(45deg);
    }
  }

  .footer {
    width: 100px;
    margin: auto;
    text-align: center;
    font-size: 0;

    .foot {
      border: 2px solid $body-border;
      background-color: #77a2b7;
      border-top: 0;
      display: inline-block;
      width: 20px;
      height: 10px;

      &:after {
        content: '';
        display: inline-block;
        width: 26px;
        height: 10px;
        border: 1px solid #000;
        position: relative;
        left: -5px;
        top: 10px;
        background-color: #000;
        border: 2px solid $body-border;
        border-radius: 5px;
      }
    }

    .right {
      margin-left: 10px;
    }

    .left-lift:after {
      animation: leftlift .5s ease infinite;
    }

    .right-lift:after {
      animation: rightlift .5s ease infinite;
    }

    @keyframes leftlift {
      0% {
        top: 10px;
      }
      50% {
        top: 6px;
      }
      100% {
        top: 10px;
      }
    }

    @keyframes rightlift {
      0% {
        top: 6px;
      }
      50% {
        top: 10px;
      }
      100% {
        top: 6px;
      }
    }

    @keyframes move {
      0% {
        transform: translateX(0);
      }
      25% {
        transform: translateX(-10%);
      }
      50% {
        transform: translateX(0%);
      }
      75% {
        transform: translateX(10%);
      }
      100% {
        transform: translateX(0);
      }
    }
  }
}

.copyright {
  position: absolute;
  bottom: 10px;
  text-align: center;
  display: block;
  width: 100%;
}

p.host,
.host a {
  font-size: 13px;
}

.hello {
  text-align: center;
  .title {
    color: #333;
  }
}

</style>
