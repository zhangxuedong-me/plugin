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
          :key="rowKey ? item[rowKey] : index"
          :style="{
            background: tableStyle.tbody.background,
            top: top + 'px',
            paddingLeft: item.options.loadStatus === 3 ? '40px' : '10px',
          }"
          @click="rowClick(data[index], index)"
          @mouseenter="rowMover(data[index])"
          @mouseleave="rowLeave(data[index])"
          :index="index"
          v-show="item.options.childStatus"
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
          >
            <div
              :style="{
                marginLeft:
                  i === 0 ? item.options.leve * (lazy ? 23 : 40) + 'px' : '',
              }"
            ></div>
            <div
              class="open"
              v-if="(lazy && i === 0) || (item[tree] && i === 0)"
            >
              <i
                v-if="item.options.loadStatus === 1"
                class="iconfont icon-loading"
              ></i>
              <i
                v-if="item.options.loadStatus === 0"
                class="iconfont icon-youzhankai"
                @click.stop="lazyLoad(item, index)"
              ></i>
              <i
                v-if="item.options.loadStatus === 2"
                class="iconfont icon-xiazhankai"
                @click.stop="uoClick(item, index)"
              ></i>
            </div>
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
    tableItem: () => import("./component/table-item"),
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
    tree: {
      type: String,
      default: "children",
    },
    rowKey: {
      type: String,
      default: "",
    },
  },
  data() {
    return {
      top: 0,
      timeId: null,
      customId: null,
      userId: null,
      tabData: {
        data: [],
      },
    };
  },
  created() {
    if (this.data && this.data.length) {
      this.tabData.data = this.initData(this.data);
      if (!(this.lazy && this.load instanceof Function)) {
        let newTabData = new Array(...this.tabData.data);
        newTabData.forEach((item) => {
          this.renderData(item);
        });
      }
    }
  },
  mounted() {
    this.init();
  },
  methods: {
    // 表格行的点击事件
    rowClick(item, index) {
      this.$emit("row-click", item, index);
    },
    // 表格行的滑入事件
    rowMover(item) {
      if (this.mouseSuspend) {
        clearInterval(this.timeId);
        this.timeId = null;
      }
      this.$emit("row-mover", item);
    },
    // 表格行的离开事件
    rowLeave(item) {
      if (this.mouseSuspend) {
        this.init();
      }
      this.$emit("row-leave", item);
    },
    init() {
      let height = parseInt(this.$slots.default[0].componentInstance.height);
      if (
        this.animated &&
        this.height <= (this.tabData.data.length - 1) * height
      ) {
        this.timeId = setInterval(this.cellAnimated, this.timeOut);
      }
    },
    initData(data, leve = 0, currentLeve = 0, parentData = null) {
      return data.map((item, index) => {
        item = {
          ...item,
          options: {
            loadStatus: 0,
            leve: leve,
            lazy: false,
            childStatus: true,
            currentLeve: currentLeve,
          },
          parent: {
            data: parentData,
          },
        };
        if (!(this.lazy && this.load instanceof Function)) {
          if (item.options.leve > 0) {
            item.options.childStatus = false;
          }
          if (item[this.tree] && item[this.tree].length) {
            item.options.lazy = true;
            item[this.tree] = this.initData(
              item[this.tree],
              item.options.leve + 1,
              item.options.currentLeve + 1,
              item
            );
          }
        }
        return item;
      });
    },
    renderData(tree) {
      if (tree[this.tree] && tree[this.tree].length) {
        tree[this.tree].forEach((child, index) => {
          this.tabData.data.forEach((item, i) => {
            if (child.parent.data[this.rowKey] === item[this.rowKey]) {
              this.tabData.data.splice(i + 1, 0, child);
              if (child[this.tree] && child[this.tree].length) {
                this.renderData(child);
              }
            }
          });
        });
      }
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
    // 递归遍历需要收起的子节点
    showOrHide(tree, status) {
      tree.children.forEach((child) => {
        this.tabData.data.forEach((item) => {
          if (child[this.rowKey] === item[this.rowKey]) {
            item.options.childStatus = status;
            child.options.childStatus = status;
            child.options.loadStatus = item.options.loadStatus;
            if (child.children && child.children.length) {
              if (item.options.loadStatus === 2) {
                this.showOrHide(child, status);
              }
            }
          }
        });
      });
    },
    // 懒加载收起数据
    uoClick(tree, index) {
      tree.options.loadStatus = 0;
      this.showOrHide(tree, false);
    },
    // 开启数据懒加载模式
    lazyLoad(tree, index) {
      // lazy为false才去加载数据，为true就是展开或者收缩
      if (!tree.options.lazy) {
        tree.options.loadStatus = 1;
        tree.options.lazy = true;
        new Promise((resolve, reject) => {
          if (this.load && this.load instanceof Function) {
            this.load(tree, resolve);
          }
          this.customId = setTimeout(() => {
            resolve([]);
          }, 3000);
        }).then((res) => {
          this.userId = setTimeout(() => {
            if (res && res instanceof Array) {
              if (res.length) {
                tree.options.loadStatus = 2;
                this.treeData(this.tabData.data, tree, res);
                this.tabData.data.splice(
                  index + 1,
                  0,
                  ...this.initData(
                    res,
                    tree.options.leve + 1,
                    tree.options.leve,
                    tree
                  )
                );
              } else {
                tree.options.loadStatus = 3;
              }
            } else {
              tree.options.loadStatus = 3;
            }
          }, 500);
        });
      } else {
        console.log(this.tabData.data);
        tree.options.loadStatus = 2;
        this.showOrHide(tree, true);
      }
    },
    treeData(data, tree, res) {
      data.forEach((item) => {
        if (item[this.rowKey] === tree[this.rowKey]) {
          this.$set(
            item,
            this.tree,
            this.initData(
              res,
              tree.options.leve + 1,
              tree.options.leve + 1,
              tree
            )
          );
        } else {
          if (item.children && item.children.length) {
            this.treeData(item.children, tree, res);
          }
        }
      });
    },
  },
  // 清除定时器
  beforeDestroy() {
    clearInterval(this.timeId);
    clearTimeout(this.customId);
    clearTimeout(this.userId);
    this.timeId = null;
    this.userId = null;
    this.customId = null;
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
      .dong_table_cell_clumn {
        display: flex;
        align-items: center;
      }
      display: flex;
      border-top: #ebeef5 solid 1px;
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