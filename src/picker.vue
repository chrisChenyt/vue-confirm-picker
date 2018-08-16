<template>
  <transition name="picker">
    <touch class="picker-backdrop" v-show="value" v-on:tap="onClose">
      <div class="picker-wrapper">
        <div class="pickerHeader">
          <span class="canle-btn" @click="cancel">取消</span>
          <span>时间筛选</span>
          <span class="confirm-btn" @click="confirm">确认</span>
        </div>
        <div class="picker-content">
          <div class="picker-body">
            <picker-item @change="onChangeValue" v-for="(item, index) in dataItems" 
              :key="index"
              :itemIndex="index" 
              :index="item.index" 
              :all-values="item.values" 
              :values="item.$value" 
              :length="item.values.length - 1" 
              :maxScrollValue="item.maxScrollValue"
              :keyName="item.key"
              :style="item.style"></picker-item>
          </div>
          <div class="picker-helper"></div>
        </div>
      </div>
    </touch>
  </transition>
</template>

<script>

  import 'raf.js';
  import Vue from 'vue';
  import VueTouch from 'vue-touch';
  import pickerItem from './picker-item.vue';

  Vue.use(VueTouch, { name: 'touch' });
  
  Vue.directive('for-nested', {
    bind (el, binding) {
      let value = binding.value[0];
      let key = binding.value[1];
      let html = '';
      for(let i = 0; i < 20; i++) {
        let n = 19 - i;
        let item = null;
        if (typeof value[n] == 'object') {
          item = value[n] && typeof value[n][key] !== 'undefined' ? value[n][key] : '';
        } else {
          item = typeof value[n] !== 'undefined' ? value[n] : '';
        }
        html = `<div><span data-index="${n}">${item}</span>${html}</div>`;
      }
      el.innerHTML = html;
    },
    update (el, binding) {
      let value = binding.value[0];
      let key = binding.value[1];
      let spenEl = el.querySelectorAll('span');
      let length = spenEl.length;
      for(let i = 0; i < length; i++) {
        let item = null;
        if (typeof value[i] == 'object') {
          item = value[i] && typeof value[i][key] !== 'undefined' ? value[i][key] : '';
        } else {
          item = typeof value[i] !== 'undefined' ? value[i] : '';
        }
        spenEl[i].innerHTML = item;
      }
    }
  });

  export default {
    name: 'picker',
    components: {
      'picker-item': pickerItem
    },
    data () {
      return {
        result: [],
        flex: {
          '-webkit-box-flex': 'initial',
          '-ms-flex': 'initial',
          'flex': 'initial'
        }
      }
    },
    props: {
      value: {
        type: Boolean
      },
      dataItems: {
        type: Array
      },
      callbackArg: {}
    },
    methods : {
      onClose (... arg) {
        let e = arg[0];
        if (e.target && e.target.classList.contains('picker-backdrop')) {
          this.$emit('input', false);
        }
      },
      onChangeValue (itemIndex, result, isSend) {
        if (isSend) {
          this.result[itemIndex] = result;
          this.$emit('change', ...this.result, this.callbackArg);
        } else {
          this.result[itemIndex] = result;
        }
      },
      init (n) {
        Vue.set(n, 'key', n.name );
        if (n.width) {
          let width = { 'width' : n.width }
          Vue.set(n, 'style', [width, this.flex] );
        }
        if (! n.maxScrollValue) {
          Vue.set(n, 'maxScrollValue', n.values.length);
        }
        n.$value = n.values.slice(0, 15);
      },
      cancel () {
        this.$emit('input', false);
      },
      confirm () {
        this.$emit('confirm',...this.result, this.callbackArg)
        this.$emit('input', false);
      },
    },
    watch: {
      dataItems (newDataItems) {
        newDataItems.forEach(this.init);
      },
      value (result) {
        if (! result) return;
        this.$emit('change', ...this.result, this.callbackArg);
      }
    },
    created () {
      this.dataItems.forEach(this.init);
    }
  }
</script>
<style>
@keyframes picker-close {
  0% { transform: translateY(0%) }
  100% { transform: translateY(100%) }
}

@keyframes picker-open {
  0% { transform: translateY(100%) }
  100% { transform: translateY(0) }
}

@keyframes picker-close-backdrop {
  0% { opacity: 1 }
  50% { opacity: 1 }
  100% {opacity: 0 }
}

@keyframes picker-open-backdrop {
  0% { opacity: 0 }
  50% { opacity: 1 }
  100% { opacity: 1 }
}

.picker-enter-active {
  animation: picker-open-backdrop 0.3s ease-out;
}
.picker-enter-active .picker-wrapper {
  animation: picker-open 0.3s ease;
}

.picker-leave-active {
  animation: picker-close-backdrop 0.3s ease-out;
}
.picker-leave-active .picker-wrapper {
  animation: picker-close 0.3s ease;
}

.picker-enter, .picker-leave-active {
  opacity: 0;
}


.picker-backdrop {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  z-index: 999;
}

.picker-wrapper {
  position: absolute;
  left: 0;
  bottom: 0;
  width: 100%;
  background: transparent;
  transform: translate3d(0px, 0px, 0px);
  z-index: 9999;
  overflow: hidden;
}

.picker-content {
  position: relative;
  width: 100%;
  height: 380px;
  background: white;
}

.picker-body {
  z-index: 1;
  position: absolute;
  width: 100%;
  height: 100%;
  display: flex;
  display: -moz-box;
  display: -webkit-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  flex-direction: row;
  -o-flex-direction: row;
  -ms-flex-direction: row;
  -moz-flex-direction: row;
  -webkit-flex-direction: row;
  justify-content: center;
  -webkit-justify-content: center;
  -moz-justify-content: center;
  -ms-justify-content: center;
  -o-justify-content: center;
}

.picker-item {
  font-size: 30px;
  color: #5F5550;
  height: 100%;
  position: relative;
  display: flex;
  display: -moz-box;
  display: -webkit-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  flex: 1;
  -webkit-flex: 1;
}

.picker-helper {
  position: absolute;
  width: 100%;
  height: 100%;
  display: flex;
  display: -moz-box;
  display: -webkit-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  justify-content: center;
  -webkit-justify-content: center;
  -moz-justify-content: center;
  -ms-justify-content: center;
  -o-justify-content: center;
  flex-direction: column;
  -o-flex-direction: column;
  -ms-flex-direction: column;
  -moz-flex-direction: column;
  -webkit-flex-direction: column;
}
.picker-helper:before {
  content: '';
  width: 100%;
  height: 76px;
  border-top: 2px solid #adb0a7;
  border-bottom: 2px solid #adb0a7;
  display: flex;
  display: -moz-box;
  display: -webkit-box;
  display: -ms-flexbox;
  display: -webkit-flex;
}

.picker-item-content {
  width: 100%;
  height: 72px;
  position: absolute;
  top: 156px;
  left: 0px;
  transform-style: preserve-3d;
  transform-origin: center center -227.295054px;
}
.picker-item-content div {
  width: 100%;
  position: absolute;
  top: 72px;
  left: 0px;
  transform-origin: top;
  transform-style: preserve-3d;
  transform: rotateX(-18deg);
}
.picker-item-content>div {
  top: 0;
  transform: rotateX(0deg);
}
.picker-item-content span {
  width: 100%;
  height: 72px;
  text-align: center;
  display: block;
  line-height: 72px;
  backface-visibility: hidden;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.pickerHeader{
  background: #ffffff;
  display: flex;
  display: -moz-box;
  display: -webkit-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  justify-content: space-between;
  -webkit-justify-content: space-between;
  -moz-justify-content: space-between;
  -ms-justify-content: space-between;
  -o-justify-content: space-between;
}
.pickerHeader span{
  font-size: 28px;
  color: #5F5550;
  padding: 30px;
}
</style>
