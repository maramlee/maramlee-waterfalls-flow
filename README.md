# maramlee-waterfalls-flow 使用

## 前言

waterfalls-flow 是一个瀑布流插件，简单易用。

前段时间做项目中需要使用到瀑布流，但找了一圈，都没有找到合适的瀑布流插件，很多都是直接通过标签分列，治标不治本。

所以自己动手写了这个插件，可以配置多列（默认 2 列，最少 2 列，除非故意太多列影响渲染，按理讲最多可以无限多）。适用于 h5 端、app 端、微信小程序端，这几个端是经过我反复测试再发布，并使用在项目中，一般没啥 bug，但其他端没测试，可以自行测试，有什么问题留言给我。

此瀑布流可为纯图片瀑布流，也可在图片下方自定义其他内容，内容高度是自适应的，可以随意添加、布局内容。

利用 vue 的特性避免重复渲染，每次加载数据除非本身 http 请求时间长影响外，每次渲染只有新增的数据渲染，已经渲染的数据不会重复再次渲染影响性能。所以，不会因为数据加载越来越多而渲染越来越慢。

我自己的项目是用 ts 写的，不知道有没有需要 ts 版本的，我觉得可能用 ts 占少数，如果需要的话，也可以联系我。

## 使用方式

### 在 `script` 中引用组件

```javascript
import waterfallsFlow from "@/components/maramlee-waterfalls-flow/maramlee-waterfalls-flow.vue";
export default {
  components: { waterfallsFlow },
};
```

### 在 `template` 中使用组件

#### 可以只是渲染图片，不需要其他

```vue
<template>
  <waterfallsFlow :single="true" :list="list" />
</template>
```

#### 有插槽（自定义内容）的情况要分情况使用

##### 若只适配 app、h5 端，利用作用域插槽

注意：`item` 包含 `list` 对应的数据项，可以随意搭配、自定义使用。

```vue
<template>
  <waterfallsFlow :list="list">
    <template v-slot:default="item">
      <!-- 此处添加插槽内容 -->
      <!-- <view class="cnt">
          <view class="title">{{item.title}}</view>
          <view class="text">{{item.text}}</view>
        </view> -->
    </template>
  </waterfallsFlow>
</template>
```

##### 若只适配微信小程序，利用小程序插槽

注意：微信小程序没有动态模板，也和 vue 的插槽使用方式不一样

```vue
<template>
  <waterfallsFlow :list="list">
    <view v-for="(item, index) of list" :key="index" slot="slot{{index}}">
      <!-- 自定义内容 -->
      <!-- <view class="cnt">
          <view class="title">{{item.title}}</view>
          <view class="text">{{item.text}}</view>
        </view> -->
    </view>
  </waterfallsFlow>
</template>
```

##### 若需要同时兼容 app、h5、微信小程序，则需条件渲染

```vue
<template>
  <view class="content">
    <waterfallsFlow :list="list">
      <!--  #ifdef  MP-WEIXIN -->
      <!-- 微信小程序自定义内容 -->
      <view v-for="(item, index) of list" :key="index" slot="slot{{index}}">
        <!-- <view class="cnt">
          <view class="title">{{item.title}}</view>
          <view class="text">{{item.text}}</view>
        </view>-->
      </view>
      <!--  #endif -->

      <!-- #ifndef  MP-WEIXIN -->
      <!-- app、h5 自定义内容 -->
      <template v-slot:default="item">
        <!-- <view class="cnt">
          <view class="title">{{item.title}}</view>
          <view class="text">{{item.text}}</view>
        </view> -->
      </template>
    </waterfallsFlow>
    <!-- #endif -->
  </view>
</template>
```

## 属性说明

| 属性名      | 类型    | 默认值    | 是否必传 | 说明                                     | 平台支持         |
| ----------- | ------- | --------- | -------- | ---------------------------------------- | ---------------- |
| list        | Array   | -         | 是       | 渲染的列表                               | 全               |
| offset      | Number  | 10        | 否       | 单位是 px                                | 全               |
| idKey       | String  | id        | 否       | 列表渲染的 key 的键名，值必须唯一        | 全               |
| imageSrcKey | String  | image_url | 否       | 图片 src 的键名                          | 全               |
| cols        | Number  | 2         | 否       | 列数，值必须不小于 2                     | 全               |
| single      | Boolean | false     | 否       | 是否是单独的渲染图片，只控制图片圆角而已 | 全               |
| listStyle   | Object  | -         | 否       | 单个展示项的样式                         | 微信小程序不支持 |
| imageStyle  | Object  | -         | 否       | 图片的样式                               | 全               |

## 事件说明

| 事件名       | 说明             | 返回值       |
| ------------ | ---------------- | ------------ |
| @wapper-lick | 单项点击事件     | 单项对应数据 |
| @image-click | 图片点击事件     | 单项对应数据 |
| @image-load  | 图片渲染完成触发 | -            |

## 组件方法

| 方法名  | 说明                 | 参数 | 使用场景       |
| ------- | -------------------- | ---- | -------------- |
| refresh | 刷新数据，数据初始化 | -    | 页面下拉刷新等 |

### refresh 使用示例

使用部分代码样例

```vue
<template>
  <waterfallsFlow ref="waterfallsFlow" :list="list">
    <template v-slot:default="item"> <!-- ... --> </template>
  </waterfallsFlow>
</template>
<script>
import waterfallsFlow from "@/components/maramlee-waterfalls-flow/maramlee-waterfalls-flow.vue";
export default {
  components: { waterfallsFlow },
  onPullDownRefresh() {
    this.$refs.waterfallsFlow.refresh();
    // 重新获取渲染列表
  },
};
</script>
```

## 提示

如果你看到了这里，还有以下情况：

1. 插件使用方法有不懂的地方；
2. 插件本身研究不明白的地方；
3. 觉得插件有待提高的建议；
4. 或者其他你遇到的问题；
5. 纯粹想前端技术交流也行。

可以加我微信，微信号：`ml-maramlee`，备注以`maramlee-${姓名}-${1}`的形式，其中 1、2、3、4 对应前面的情况（前端都看得懂哈），例如：`maramlee-maram-1`。

**注：人家是有脾气的，不这样备注不给加哟~**

**再注：觉得好用记得收藏、评论，这样可以让更多人看到，让更多人受益。当然，也是欢迎土豪赞赏，哈哈哈哈哈哈……**

**申明：主要是看到评论里有或多或少的问题，加微信有助于解决问题，只接受技术交流，其他请勿扰。**

## 使用样例

pages/index/index.vue

```vue
<template>
  <view class="content">
    <waterfallsFlow :list="list">
      <!--  #ifdef  MP-WEIXIN -->
      <view v-for="(item, index) of list" :key="index" slot="slot{{index}}">
        <view class="cnt">
          <view class="title">{{ item.title }}</view>
          <view class="text">{{ item.text }}</view>
        </view>
      </view>
      <!--  #endif -->

      <!-- #ifndef  MP-WEIXIN -->
      <template v-slot:default="item">
        <view class="cnt">
          <view class="title">{{ item.title }}</view>
          <view class="text">{{ item.text }}</view>
        </view>
      </template>
    </waterfallsFlow>
    <!-- #endif -->
  </view>
</template>

<script>
import waterfallsFlow from "@/components/maramlee-waterfalls-flow/maramlee-waterfalls-flow.vue";
export default {
  components: { waterfallsFlow },
  data() {
    return {
      list: [
        {
          id: 1,
          image_url:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1599475741266&di=e36d6c01c93320e2ba1504d8357248f4&imgtype=0&src=http%3A%2F%2Fa0.att.hudong.com%2F30%2F29%2F01300000201438121627296084016.jpg",
          title: "可爱的小猫咪呀",
          text:
            "小小的猫咪，甚是呆萌，呆萌呆萌呆萌呆萌呆萌呆萌呆萌呆萌呆萌呆萌呆萌呆萌呆萌呆萌呆萌呆萌呆萌呆萌呆萌",
        },
        {
          id: 2,
          image_url:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1599475934834&di=7a37b8d628252c4aced6ed0decba9442&imgtype=0&src=http%3A%2F%2Fa3.att.hudong.com%2F43%2F74%2F01300000164151121808741085971.jpg",
          title: "迪士尼动画",
          text: "迪士尼动画之……",
        },
        {
          id: 3,
          image_url:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1599476083909&di=a5debff35edec5de105bc105d6fdbce3&imgtype=0&src=http%3A%2F%2Fa2.att.hudong.com%2F77%2F77%2F01300000336597125202779973172.jpg",
          title: "火箭",
          text: "火箭升空瞬间，宏伟壮观啊",
        },
        {
          id: 5,
          image_url:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1599476129760&di=7a3db0b14f6a74240bbfa7922ba22f45&imgtype=0&src=http%3A%2F%2Fa4.att.hudong.com%2F82%2F55%2F01300000349330124003555691086.jpg",
          title: "华佗",
          text: "华佗人物画像 中国画 线条画 毛笔画 彩色画",
        },
        {
          id: 6,
          image_url:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1599476215687&di=97c2bbf6f3a1a3e2a6a2dc77dfe4bea7&imgtype=0&src=http%3A%2F%2Fa4.att.hudong.com%2F72%2F82%2F19300000009075130804824786610.jpg",
          title: "恐龙",
          text: "恐龙来啦",
        },
        {
          id: 7,
          image_url:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1599476258176&di=29622b0f0cfd659aecebabaae352d02c&imgtype=0&src=http%3A%2F%2F1882.img.pp.sohu.com.cn%2Fimages%2Fblog%2F2011%2F3%2F25%2F13%2F13%2Fu48513077_12fa4ba953ag213.jpg",
          title: "手",
          text: "什么？",
        },
        {
          id: 8,
          image_url:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1599476300222&di=49712f992d8f7bbb1a5851eced71cbe2&imgtype=0&src=http%3A%2F%2Fa2.att.hudong.com%2F71%2F56%2F16300000988660128426569668958.jpg",
          title: "百年好合",
          text: "百年好合 结婚 庚帖 二次元",
        },
        {
          id: 9,
          image_url:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1599476416001&di=ea1a1f8f9b1274d39c05af3e48041e6a&imgtype=0&src=http%3A%2F%2Finews.gtimg.com%2Fnewsapp_bt%2F0%2F12420002963%2F641",
          title: "5G",
          text: "5G 来啦",
        },
        {
          id: 10,
          image_url:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1599476497015&di=2dfa9a6ec465d7330bc0b4433d63cd9e&imgtype=0&src=http%3A%2F%2Fimgo.zjjjtg.com%2Fimg2020%2F9%2F4%2F10%2F2020090410315179234.jpg",
          title: "LEVEL 2",
          text: "LEVEL 2",
        },
        {
          id: 12,
          image_url:
            "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1599476567983&di=040976a1cd1a6e5510a237c57bdcff06&imgtype=0&src=http%3A%2F%2Finews.gtimg.com%2Fnewsapp_bt%2F0%2F12421051168%2F641",
          title: "王者荣耀",
          text: "王者荣耀 龙 快来打龙 请求集合",
        },
      ],
    };
  },
};
</script>
<style>
page {
  background-color: #eee;
}
</style>
<style lang="scss" scoped>
.content {
  padding: 10px;
  .cnt {
    padding: 10px;
    .title {
      font-size: 16px;
    }
    .text {
      font-size: 14px;
      margin-top: 5px;
    }
  }
}
</style>
```
