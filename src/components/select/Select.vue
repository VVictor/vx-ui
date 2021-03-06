<template>
  <div :class="['vx-select',`vx-select--size-${size}`, {'is-disabled':disabled}]" @click.stop.prevent="handleFocusIn">
    <flexbox :gutter="0" class="vx-select--inner" align="center">
      <slot name="prepend"></slot>
      <flexbox-item class="vx-select--placeholder">
        <span :data-placeholder="placeholder">{{myLabel}}</span>
      </flexbox-item>
      <template v-if="!$slots.append">
        <transition v-if="this.clearable && value+''" name="input-clearable-fade">
          <button
            tabindex="-2"
            type="button"
            v-show="!!value && clearable && !disabled"
            class="vx-input--clearable-button"
            @click.stop="handleClear">
            <i class="vx-input--clearable-icon"></i>
          </button>
        </transition>
        <arrow v-else-if="arrow" v-bind="arrowProps" direction="down"/>
      </template>
      <slot v-else name="append"></slot>
    </flexbox>
    <datalist>
      <slot></slot>
    </datalist>
  </div>
</template>

<script>
import Vue from 'vue'
import Picker from './Picker'
import { input } from 'utils/mixins'
import { Flexbox, FlexboxItem } from '../flexbox'
import Arrow from '../arrow'

export default {
  name: 'XSelect',
  componentName: 'XSelect',
  components: {
    Arrow,
    Flexbox,
    FlexboxItem
  },
  mixins: [input],
  props: {
    ...input.props,
    value: {
      type: [String, Number, Array]
    },
    getPopupMounted: {
      type: Function
    },
    max: {
      type: Number,
      default: 1
    },
    placeholder: {
      type: String,
      default: '请选择'
    },
    popupProps: {
      type: Object
    },
    arrow: {
      type: Boolean,
      default: true
    },
    separator: {
      type: String,
      default: ','
    },
    clearable: {
      type: Boolean,
      default: false
    }
  },
  watch: {
    value (val) {
      this.updateLabel(val)
    }
  },
  data () {
    return {
      myLabel: this.max === 1 ? '' : []
    }
  },
  mounted () {
    this.$nextTick(() => {
      this.$$myOptions = this.getOptions()
      this.value && this.updateLabel(this.value)
    })
  },
  updated () {
    this.$nextTick(() => {
      this.$$myOptions = this.getOptions()
      this.updateLabel(this.value)
    })
  },
  beforeDestroy () {
    if (this.$root && this.$root.__popup && this.__popup === this.$root.__popup) {
      this.$root.__popup && this.$root.__popup.$destroy()
      this.__popup = this.$root.__popup = null
    }
  },
  methods: {
    getOptions () {
      let result = []
      let nodes = this.$el.querySelector('datalist').children
      let len = nodes.length
      for (let i = 0; i < len; i++) {
        let node = nodes[i]
        let item = JSON.parse(node.value)
        let html = node.innerHTML.trim()
        result.push({
          label: nodes[i].innerText,
          ...item,
          html
        })
      }
      return result
    },
    handleFocusIn (e) {
      if (!this.disabled) {
        let self = this
        let node = document.createElement('div')
        if (this.getPopupMounted) {
          this.getPopupMounted(e).appendChild(node)
        } else {
          document.body.appendChild(node)
        }
        if (this.$root && this.$root.__popup) {
          this.$root.__popup && this.$root.__popup.$destroy()
        }
        if (this.$$myOptions.length) {
          /* eslint-disable no-new */
          this.$root.__popup = this.__popup = new Vue({
            el: node,
            render (createElement) {
              return createElement(Picker, {
                props: {
                  open: this.open,
                  value: self.value,
                  options: self.$$myOptions,
                  title: self.title,
                  max: self.max,
                  ...self.popupProps
                },
                class: ['vx-select--picker'],
                on: {
                  'close': this.handleClose,
                  'close-after': this.handleCloseAfter,
                  'change': this.handleChange
                }
              })
            },
            data: {
              open: false
            },
            mounted () {
              requestAnimationFrame(() => {
                this.open = true
                self.isFocus = true
              })
            },
            beforeDestroy () {
              this.$el.parentNode && this.$el.parentNode.removeChild(this.$el)
            },
            methods: {
              handleClose () {
                self.isFocus = false
                this.open = false
              },
              handleCloseAfter () {
                this.$nextTick(() => {
                  this.$destroy()
                })
              },
              handleChange (value) {
                if (self.value !== value) {
                  self.$emit('input', value).$emit('change', value)
                  self.eDispatch('ElFormItem', 'el.form.blur', [value])
                  self.eDispatch('ElFormItem', 'el.form.change', [value])
                  self.updateLabel(value)
                }
                this.handleClose()
              }
            }
          })
        }
      }
    },
    updateLabel (value) {
      let label = this.getLabel(value)
      if (label !== this.$$label) {
        this.$$label = label
        this.myLabel = label
        this.$emit('update:label', label)
      }
    },
    getLabel (value) {
      let result = ''
      if (this.$$myOptions && this.$$myOptions.length) {
        if (this.max === 1) {
          this.$$myOptions && this.$$myOptions.forEach(item => {
            if (item.value === value) {
              result = item.label
            }
          })
        } else {
          let label = []
          this.$$myOptions && this.$$myOptions.forEach(item => {
            value && value.indexOf(item.value) > -1 && label.push(item.label)
          })
          result = label.join(this.separator)
        }
      }
      return result
    },
    handleClear () {
      let value = this.max === 1 ? '' : []
      this.$emit('input', value).$emit('change', value)
    }
  }
}
</script>
