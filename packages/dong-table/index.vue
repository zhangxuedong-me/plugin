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
          v-for="(item, index) in tabData"
          :key="index"
          :style="{ background: tableStyle.tbody.background, top: top + 'px' }"
          @click="rowClick(item, index)"
          :index="index"
          @mouseenter="cellMover(item)"
          @mouseleave="cellLeave(item)"
        >
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
            @click="
              cellClick(
                item[obj.componentOptions.propsData.prop],
                obj.componentOptions.propsData.prop
              )
            "
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
  },
  data() {
    return {
      top: 0,
      timeId: null,
      tabData: this.data,
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      let height = parseInt(this.$slots.default[0].componentInstance.height);
      if (this.animated && this.height <= this.tabData.length * height) {
        this.timeId = setInterval(this.cellAnimated, this.timeOut);
      }
    },
    // 表格行的点击事件
    rowClick(item, index) {
      this.$emit("row-click", item, index);
    },
    cellClick(item, type) {
      this.$emit("cell-click", item, type);
    },
    // 表格移动动画
    cellAnimated(e) {
      let height = -parseInt(this.$slots.default[0].componentInstance.height);
      if (this.top > height) {
        this.top--;
      } else {
        let obj = this.tabData.shift();
        this.tabData.push(obj);
        this.top = 0;
      }
    },
    cellMover(item) {
      if (this.mouseSuspend) {
        clearInterval(this.timeId);
        this.timeId = null;
      }
      this.$emit("cell-mover", item);
    },
    cellLeave(item) {
      if (this.mouseSuspend) {
        this.init();
      }
      this.$emit("cell-leave", item);
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
  min-width: 300px;
  position: relative;
  .dong_table_header {
    display: flex;
    padding-left: 10px;
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
      &:hover {
        background: #f5f7fa !important;
      }
    }
  }
}
</style>