# Actionsheet

```html
<template>
  <div>
    <group>
      <cell :arrow="false">
        <div slot="title">default</div>
        <x-switch slot="value" v-model="open1"/>
      </cell>
      <cell :arrow="false">
        <div slot="title">cancel item && title</div>
        <x-switch slot="value" v-model="open2"/>
      </cell>
      <cell :arrow="false">
        <div slot="title">menu</div>
        <x-switch slot="value" v-model="open3"/>
      </cell>
      <cell :arrow="false">
        <div slot="title">js调用</div>
        <span slot="value" @click="handleJSCall">点击我</span>
      </cell>
    </group>
    <actionsheet :open.sync="open1" @action="handleAction">
      <actionsheet-item v-for="item in options" :value="item.value" :key="item.value">{{item.label}}</actionsheet-item>
    </actionsheet>
    <actionsheet :open.sync="open2" :cancel="true" @action="handleAction" title="标题文字">
      <actionsheet-item v-for="item in options" :value="item.value" :key="item.value">{{item.label}}</actionsheet-item>
    </actionsheet>
    <actionsheet type="menu" :open.sync="open3" @action="handleAction" >
      <actionsheet-item v-for="item in options" :value="item.value" :key="item.value">{{item.label}}</actionsheet-item>
    </actionsheet>
  </div>
</template>

<script>

export default {
  data () {
    return {
      options: [
        {
          value: '1',
          label: '编辑'
        },
        {
          value: '2',
          label: '收藏'
        },
        {
          value: '3',
          label: '分享'
        },
        {
          value: '4',
          label: '删除'
        }
      ],
      open1: false,
      open2: false,
      open3: false
    }
  },
  methods: {
    handleAction (value) {
      let label = this.options.filter((item) => {
        return item.value === value
      })[0].label
      this.$toast({content: `您点击了“${label}”`})
    },
    handleJSCall () {
      let self = this
      this.$actionsheet({
        title: '标题文字',
        options: [...this.options]
      }).then((value) => {
        self.handleAction(value)
      }).catch(() => {
        console.log('close')
      })
    }
  }
}
</script>
```

### Actionsheet
#### Props
| 参数      | 说明    | 类型      | 可选值       | 默认值   |
|---------- |-------- |---------- |------------- |--------- |
| type     | 类型   | String  |   default,menu       |    default    |
| open     | 是否打开   | Boolean  |   -       |    false    |
| history     | 打开是否产生浏览器历史记录   | Boolean  |   -       |    true    |
| cancel     | 是否有取消操作   | Boolean  |   -       |    false    |
| title     | 标题文字   | String  |   -       |    -    |
| cancelText     | 取消文字   | String  |   -       |    取消    |
| fastClose     | 是否非内容区域关闭   | Boolean  |   -       |    true    |

#### Events
| 事件名称 | 说明 | 回调参数 |
|---------|--------|---------|
| click | 点击菜单触发 | value |

#### Slots
| 名称 | 说明 | 
|---------|--------|
| default | - |

### ActionsheetItem
#### Props
| 参数      | 说明    | 类型      | 可选值       | 默认值   |
|---------- |-------- |---------- |------------- |--------- |
| value     | 值   | String  |   -       |    -    |
| disabled     | 是否禁用   | Boolean  |   -       |    false    |

#### Events
| 事件名称 | 说明 | 回调参数 |
|---------|--------|---------|
| - | - | - |

#### Slots
| 名称 | 说明 | 
|---------|--------|
| default | - |