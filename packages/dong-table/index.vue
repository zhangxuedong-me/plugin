<template>
  <div class="dong_table" :style="border ? 'border: #ebebeb solid 1px;' : ''">
    <div style="display: flex" v-show="showHeader">
      <div
        class="dong_table_header"
        :style="{
          background: tableStyle.thead.background,
          fontSize: tableStyle.thead.fontSize,
          fontFamily: tableStyle.thead.fontType,
          color: tableStyle.thead.color,
          fontWeight: tableStyle.thead.fontWeight,
          borderBottom: animated ? '#ebeef5 solid 1px' : 'none',
          paddingLeft: lazy ? '40px' : '10px',
        }"
      >
        <slot></slot>
      </div>
    </div>
    <div style="display: flex">
      <div
        class="dong_table_content"
        :style="{ height: height + 'px' }"
        v-if="data.length"
      >
        <div
          class="dong_table_row_clumn"
          v-for="(item, index) in tabData.data"
          :key="index"
          :style="{ background: tableStyle.tbody.background, top: top + 'px' }"
          @click="rowClick(data[index], index)"
          :index="index"
          @mouseenter="rowMover(data[index])"
          @mouseleave="rowLeave(data[index])"
        >
          <div class="open" v-if="lazy">
            <i v-if="item.options.loadStatus" class="iconfont icon-loading"></i>
            <i
              v-else
              class="iconfont icon-youzhankai"
              @click.stop="lazyLoad(item, index)"
            ></i>
          </div>
          <div
            class="dong_table_cell_clumn"
            v-for="(obj, i) in $slots.default"
            :key="i"
            :style="{
              width: obj.componentOptions.propsData.width,
              lineHeight: obj.componentOptions.propsData.height,
              textAlign: align,
              color: tableStyle.tbody.color,
              fontSize: tableStyle.tbody.fontSize,
              fontFamily: tableStyle.tbody.fontType,
              fontWeight: tableStyle.tbody.fontWeight,
            }"
          >
            <table-item :childSolts="obj" :item="item" :obj="obj"></table-item>
          </div>
        </div>
      </div>
      <div class="empty_text" v-else>
        <slot name="empty">
          <div class="empty_text_content">
            <div>{{ emptyText }}</div>
          </div>
        </slot>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "DongTable",
  components: {
    tableItem: () => import("../../examples/components/table-item"),
  },
  props: {
    data: {
      type: Array,
      default: () => [],
    },
    align: {
      type: String,
      default: "left",
    },
    tableStyle: {
      type: Object,
      default: () => {},
    },
    border: {
      type: Boolean,
      default: false,
    },
    timeOut: {
      type: Number,
      default: 100,
    },
    animated: {
      type: Boolean,
      default: false,
    },
    height: {
      type: Number,
      default: 100,
    },
    showHeader: {
      type: Boolean,
      default: true,
    },
    emptyText: {
      type: String,
      default: "亲，数据跑路了",
    },
    mouseSuspend: {
      type: Boolean,
      default: true,
    },
    lazy: {
      type: Boolean,
      default: false,
    },
    load: {
      type: Function,
      default: null,
    },
  },
  data() {
    return {
      top: 0,
      timeId: null,
      tabData: {
        data: [],
      },
    };
  },
  created() {
    if (this.data && this.data.length) {
      this.initData();
    }
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      let height = parseInt(this.$slots.default[0].componentInstance.height);
      if (
        this.animated &&
        this.height <= (this.tabData.data.length - 1) * height
      ) {
        this.timeId = setInterval(this.cellAnimated, this.timeOut);
      }
    },
    initData() {
      this.tabData.data = this.data.map((item) => {
        return {
          ...item,
          options: {
            loadStatus: false,
            leave: 0,
          },
        };
      });
    },
    // 表格行的点击事件
    rowClick(item, index) {
      this.$emit("row-click", item, index);
    },
    // 表格移动动画
    cellAnimated(e) {
      let height = -parseInt(this.$slots.default[0].componentInstance.height);
      if (this.top > height) {
        this.top--;
      } else {
        let obj = this.tabData.data.shift();
        this.tabData.data.push(obj);
        this.top = 0;
      }
    },
    rowMover(item) {
      if (this.mouseSuspend) {
        clearInterval(this.timeId);
        this.timeId = null;
      }
      this.$emit("row-mover", item);
    },
    rowLeave(item) {
      if (this.mouseSuspend) {
        this.init();
      }
      this.$emit("row-leave", item);
    },
    lazyLoad(obj, index) {
      obj.options.loadStatus = true;
      new Promise((resolve, reject) => {
        if (this.load && this.load instanceof Function) {
          this.load(this.data[index], resolve);
        }
        setTimeout(() => {
          resolve([]);
        }, 3000);
      }).then((res) => {
        obj.options.loadStatus = false;
        if (res && res instanceof Array) {
          if (res.length) {
            console.log(res);
          }
        }
      });
    },
  },
  beforeDestroy() {
    clearInterval(this.timeId);
    this.timeId = null;
  },
};
</script>

<style lang="less" scoped>
.dong_table {
  border-radius: 3px;
  overflow: hidden;
  overflow-x: auto;
  overflow-y: auto;
  min-width: 300px;
  position: relative;
  .dong_table_header {
    display: flex;
    position: relative;
    z-index: 100;
  }
  .empty_text {
    width: 100%;
    height: 200px;
    line-height: 200px;
    color: #666666;
    text-align: center;
  }
  .dong_table_content {
    .dong_table_row_clumn {
      display: flex;
      border-top: #ebeef5 solid 1px;
      padding-left: 10px;
      position: relative;
      transition: background 0.5s linear;
      align-items: center;
      &:hover {
        background: #f5f7fa !important;
      }
      .open {
        i {
          width: 0;
          height: 0;
          margin-right: 10px;
          font-size: 20px;
          cursor: pointer;
        }
      }
    }
  }
}
</style>