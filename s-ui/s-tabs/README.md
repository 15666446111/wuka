# 提示
后续将不在插件市场更新，组件包和示例请访问github中[s-ui地址](https://github.com/sldt/s-ui)下载

# s-tabs

## s-tabs组件参数说明

``` js
{
  // class
  customClass: {
    type: String,
    default: ''
  },
  // v-model双向绑定
  value: {
    type: Number,
    default: 0
  },
  color: {
    type: String,
    default: '#333'
  },
  activeColor: {
    type: String,
    default: '#406BDC'
  },
  // 标签栏高度 rpx
  height: {
    type: Number,
    default: 80
  },
  // 自适应宽度还是百分比等分
  navPerView: {
    type: [Number, String],
    default: 'auto'
  },
  // 内容部分是否动画切换
  effect: {
    type: Boolean,
    default: false
  },
  // 内容部分动画切换时间
  duration: {
    type: Number,
    default: 0.3
  },
  // 是否显示底部条
  line: {
    type: Boolean,
    default: true
  },
  // 底部条颜色
  lineColor: {
    type: String,
    default: '#406BDC'
  },
  // 底部条高度 rpx
  lineHeight: {
    type: Number,
    default: 4
  },
  // 底部条宽度相对于标签宽度比例
  lineScale: {
    type: Number,
    default: 0.6
  }
}
```
## s-tab子组件参数说明

``` js
{
  // 导航标题,内部v-html放置，支持html写法
  title: {
    type: String,
    default: ''
  },
  // 是否禁用导航
  disabled: {
    type: Boolean,
    default: false
  }
}
```

## 使用方式

#### template
``` html
<s-tabs :effect="true" v-model="activeTab" @change="change" :nav-per-view="5">
  <s-tab title="<span>1</span>">1</s-tab>
  <s-tab title="Tab2" :disabled="true">2</s-tab>
  <s-tab title="Tab3">3</s-tab>
  <s-tab title="Tab4">4</s-tab>
  <s-tab title="Tab5">5</s-tab>
  <s-tab title="Tab6">6</s-tab>
</s-tabs>
```

#### script
``` js
import sTabs from '@/s-ui/s-tabs';
import sTab from '@/s-ui/s-tab';

export default {
  components: {
    sTabs,
    sTab
  },
  data () {
    return {
      activeTab: 0
    };
  },
  methods: {
    change (index) {
      console.log(index);
    }
  }
};
```