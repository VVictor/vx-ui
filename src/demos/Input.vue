<template>
  <page>
    <x-nav slot="header" title="Input"></x-nav>
    <x-body slot="body">
      <group title="default">
        <x-input placeholder="请输入帐号" v-model="value"/>
      </group>
      <group title="size small">
        <x-input placeholder="请输入帐号" size="small" v-model="value"/>
      </group>
      <group title="slot prepend&append">
        <x-input placeholder="请输入帐号" v-model="value">
          <span slot="prepend" style="padding:10px;">♡</span>
          <span slot="append" style="padding:10px;">★</span>
        </x-input>
      </group>
      注意：password,datetime,datarange,select,cascader等都支持这两个slot
      <group title="disabled">
        <x-input placeholder="请输入帐号" v-model="value" disabled/>
      </group>
    </x-body>
  </page>
</template>

<script>
export default {
  data () {
    return {
      value: ''
    }
  }
}
</script>
