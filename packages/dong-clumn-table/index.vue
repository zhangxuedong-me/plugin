<template>
  <div :style="{ lineHeight: height }" class="dong_clumn_table">
    <div
      :style="{
        textAlign: align,
        width,
        height,
      }"
      class="dong_clumn_table_content"
    >
      <slot name="header">
        <div class="dong_header_content">
          <div>{{ label }}</div>
          <div class="sort_container" v-if="sortable">
            <i class="sort_down" @click="sortClick('up')"></i>
            <i class="sort_up" @click="sortClick('down')"></i>
          </div>
        </div>
      </slot>
    </div>
  </div>
</template>

<script>
export default {
  name: "DongClumnTable",
  props: {
    label: {
      type: String,
      default: "",
    },
    prop: {
      type: String,
      default: "",
    },
    width: {
      type: String,
      default: "",
    },
    height: {
      type: String,
      default: "50px",
    },
    align: {
      type: String,
      default: "left",
    },
    formatter: {
      type: Function,
      default: null,
    },
    hidden: {
      type: Boolean,
      default: true,
    },
    sortable: {
      type: Boolean,
      default: false,
    },
    sort: {
      type: Function,
      default: null,
    },
  },
  data() {
    return {};
  },
  methods: {
    treeSort(data, type) {
      if (type === "up") {
        return data.sort((a, b) => {
          return a[this.prop] - b[this.prop];
        });
      } else {
        return data.sort((a, b) => {
          return b[this.prop] - a[this.prop];
        });
      }
    },
    traverseTree(data, type) {
      data.forEach((item) => {
        if (item.children && item.children.length) {
          item.children = this.treeSort(item.children, type);
          this.traverseTree(item.children);
        }
      });
      return data;
    },
    mixedData(data) {
      let newTabData = new Array(...data);
      newTabData.forEach((item) => {
        this.$parent.renderData(item, true);
      });
    },
    sortClick(type) {
      // 如果外部使用了自定义排序函数，就使用自定义排序函数，否则使用默认排序
      let data = this.$parent.tabData.data.filter(
        (item) => item.options.leve === item.options.currentLeve
      );
      if (this.sort && this.sort instanceof Function) {
        this.$parent.tabData.data = this.sort(data, type, this.prop);
      } else {
        data = this.treeSort(data, type);
        this.$parent.tabData.data = this.traverseTree(data, type);
      }
      this.mixedData(this.$parent.tabData.data);
    },
  },
};
</script>

<style lang="less">
.dong_header_content {
  padding-left: 4px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  word-break: break-all;
  display: flex;
  height: 100%;
  .sort_container {
    position: relative;
    left: 6px;
    i {
      position: absolute;
    }
    .sort_up {
      width: 0;
      height: 0;
      border: 6px solid transparent;
      border-top-color: #ccc;
      margin-top: 10px;
      cursor: pointer;
      top: 32%;
      &:hover {
        border-top-color: #409eff;
      }
    }
    .sort_down {
      width: 0;
      height: 0;
      border: 6px solid transparent;
      border-bottom-color: #ccc;
      cursor: pointer;
      top: 24%;
      &:hover {
        border-bottom-color: #409eff;
      }
    }
  }
}
</style>