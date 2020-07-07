<template>
  <div class="textarea-box">
    <textarea
        class="textarea-box-textarea"
      :style="'height: ' + height + 'px; z-index:' + zIndex"
      v-model="valueData"
      @input="changeValues"
      @compositionstart="changeValues2"
      @compositionend="changeValues3"
    ></textarea>
    <!--<div
      contenteditable="true"
      class="textarea-box-textarea"
      :style="'height: ' + height + 'px; overflow: hidden; z-index:' + zIndex"
      @input="changeValues"
      @compositionstart="changeValues2"
      @compositionend="changeValues3"
      @paste="watchPaste"
    >{{v}}</div>-->
    <div class="textarea-box-show" v-show="isShow" :style="'height: ' + height + 'px;'">
      <div style="height: 99999px;">
        <div
          style="overflow-y: auto; box-sizing: border-box;"
          ref="textareaDiv"
          v-html="newVal"
        ></div>
      </div>
    </div>
    <!-- <div class="getValueData" ref="getValueData">{{valueData}}</div> -->
  </div>
</template>

<script>
export default {
  name: "textareaComp",
  data() {
    return {
      warningData: [],
      valueData: "",
      newVal: "",
      height: 100,
      isShow: true,
      v: '',
    };
  },
  model: {
    prop: "value",
    event: "changeValue"
  },
  props: {
      zIndex: {
          default: 10,
          type: Number
      },
    value: {
      default: "",
      type: String
    },
    max: {
      default: false,
      type: [Number,Boolean]
    },
    warning: {
      default: () => [],
      type: Array
    }
  },
  watch: {
    value(data) {
      this.valueData = data || ''
      this.changeValues();
      // this.$nextTick(() => {
      //   this.valueData = this.$refs.getValueData.innerHTML;
      // });
    },
    valueData(data) {
      this.$emit('changeValue',data)
    },
    warning(data) {
      this.warningData = data || [];
      this.changeValues();
    }
  },
  created() {
      if (this.warning && this.warning.length) {
          this.warningData = this.warning
      }
      if (this.value && this.value.length) {
          this.valueData = this.value
          this.v = this.value
      }
  },
  methods: {
    watchPaste(e) {
      console.log(e,arguments)
    },
    changeValues2(e) {
      this.isShow = false
    },
    changeValues3(e) {
      this.isShow = true
    },
    changeValues(e) {
      this.isShow = true
      if (e) {
        console.log(e,this.valueData)
        // this.valueData = e.target.innerHTML
        // this.valueData = e.data
        this.$emit("change");
      }
      if (this.max) {
          let max = this.max
          if (max === true) max = 999
        if (this.valueData.length > max) {
            this.valueData = this.valueData.slice(0,max)
        }
      }
      let val = this.valueData
        // .replace(/\</g, "A")
        // .replace(/\>/g, "A")
        .replace(/\n/g, "<br/>")
        .replace(/\s/g,'&nbsp;')
        this.warningData.forEach(item => {
            val = val.replace(
            new RegExp('(?!<span style="color: red; position: relative; z-index: ' + (this.zIndex + 1) + '; pointer-events: none;">)' + item, 'g'),
            '<span style="color: red; position: relative; z-index: ' + (this.zIndex + 1) + '; pointer-events: none;">' + item + "</span>"
            );
        })
        this.newVal = val
        this.$nextTick(() => {
          this.$refs.textareaDiv.offsetHeight > 80
          ? (this.height = this.$refs.textareaDiv.offsetHeight + 25)
          : (this.height = 100);
        })
    }
  }
};
</script>

<style lang="scss">
    .textarea-box {
        overflow: hidden;
        position: relative;
        &-textarea, &-show {
            position: relative;
            width: 100%;
            box-sizing: border-box;
            padding: 10px;
            outline: none;
            margin: 0px;
            border: 1px;
            font-size: 14px;
            font-weight: 400;
            color: rgba(0, 0, 0, 0.65);
            line-height: 22px;
            margin-bottom: 16px;
            min-height: 100px;
            white-space: pre-wrap;
            word-break:break-all;
            text-align: left;
            font-family: Helvetica Neue, Helvetica, PingFang SC, Hiragino Sans GB, Microsoft YaHei, SimSun, sans-serif;
        }
        &-show {
            position: absolute;
            left: 0;
            top: 0;
            // padding: 3px;
            // pointer-events: none;
            // word-wrap: break-word;
            // span {
            //     position: relative;
            // }
        }
        &-textarea, &-show {
          font-variant: tabular-nums;
          list-style: none;
          font-feature-settings: "tnum";
          display: inline-block;
          padding: 4px 11px;
          color: rgba(0,0,0,.65);
          font-size: 14px;
          line-height: 1.5;
          background-color: #fff;
          background-image: none;
          border: 1px solid #d9d9d9;
          border-radius: 4px;
          transition: all .3s;
          resize:none!important;
          &:hover {
            border-color: #40a9ff;
            border-right-width: 1px!important;
          }
          &:focus {
            outline: 0;
            box-shadow: 0 0 0 2px rgba(24,144,255,.2);
          }
        }
    }
</style>