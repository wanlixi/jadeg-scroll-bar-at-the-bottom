# jadeg-scroll-bar-at-the-bottom
### 判断滚动条是否是在滚到最底部，支持PC滚动加载和MOBILE端上拉加载
#### 自己用原生封装的，所以兼容性比较好
## welcome Star or Issues
```
<template>
<!-- if PC platform -->
  <div @wheel="scrollLoad">
<!-- if mobile platform -->
  <div @touchstart="loadTouchStart($event)"
       @touchmove="loadTouchMove($event)"
       @touchend="loadTouchEnd">
    <ul>
      <li v-for="(item, index) in list" :key="index">{{item}}</li>
    </ul>
    // 加载过程中显示蒙版及提示动画或文字，可以有效防止用户连续滑动
    <div class="mark" v-show="loading"></div>
  </div>
</template>
<script type="text/babel">
import axios from 'axios'
export default {
  data () {
    sy: null,
    ey: null,
    distanceY: null,
    loadState: false,
    loadText: 'loading...',
    // 手指垂直滑动的距离，越小越灵敏
    verticalDragLimit: 30,
    // 滚动条到最底部的距离
    scrollbarToBottomLimit: 10
  },
  computed: {
    getScrollTop () {
      let bodyScrollTop = !!document.body ? document.body.scrollTop : 0;
      let documentScrollTop = !!document.documentElement ? document.documentElement.scrollTop : document.documentElement;
      let scrollTop = (bodyScrollTop - documentScrollTop > 0) ? bodyScrollTop : documentScrollTop;
      return scrollTop; 
    },
    getScrollHeight () {
      let bodyScrollHeight = !!document.body ? document.body.scrollHeight : 0;
      let documentScrollHeight = !!document.documentElement ? document.documentElement.scrollHeight : 0;
    　let scrollHeight = (bodyScrollHeight - documentScrollHeight > 0) ? bodyScrollHeight : documentScrollHeight;
    　return scrollHeight;
    },
    getWindowsHeight () {
      let windowHeight = document.compatMode == "CSS1Compat" ? document.documentElement.clientHeight : document.body.clientHeight;
  　　return windowHeight;
    }
  },
  methods: {
    scrollLoad () {
      if (this.getScrollHeight - this.getScrollTop - this.getWindowsHeight < 10) {
        // load more data
      }
    },
    loadTouchStart (e) {
      e = e || window.event
      this.sy = e.touches[0].pageY
    },
    loadTouchMove (e) {
      let self = this
      e = e || window.event
      self.ey = e.touches[0].pageY
      self.distanceY = self.ey - self.sy
      // dragUp to load
      if (self.distanceY < -this.verticalDragLimit && self.getScrollHeight - self.getScrollTop - self.getWindowsHeight < scrollbarToBottomLimit) {
      // drag to down
      // if (self.distanceY > this.verticalDragLimit) {
        self.loadState = true
        axios({
          url: '',
          method: 'post',
          data: {
            
          }
        }).then(function(res){
          self.loadText = 'load success!'
          self.list.push(res.data)
          setTimeout(function(){
            self.loadState = false
          },2000)
        }).catch(function(err){
          self.loadText = 'load fail!'
          setTimeout(function(){
            self.loadState = false
          },2000)
        })
      }
    },
    loadTouchEnd () {
    
    }
  }
  
}
</script>

```
