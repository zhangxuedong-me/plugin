<script>
export default {
  name: "tableItem",
  props: {
    item: {
      type: Object,
      default: () => {},
    },
    obj: {
      type: Object,
      default: () => {},
    },
    childSolts: {
      type: Object,
      default: null,
    },
  },
  data() {
    return {};
  },
  render(h) {
    if (this.childSolts) {
      let slotsItem = this.childSolts.child;
      // 判断自定义组件table内容是否使用的是作用域插槽渲染
      if (slotsItem.$scopedSlots.default) {
        return (
          <div>
            {slotsItem.$scopedSlots.default({
              row: this.item,
            })}
          </div>
        );
      }
      // 判断自定义组件table内容是否使用的是默认插槽渲染
      if (slotsItem.$slots.default) {
        return <div>{slotsItem.$slots.default}</div>;
      } else {
        // 如果有格式化函数的话，优先渲染固定函数的内容
        if (slotsItem.formatter) {
          return (
            <div class={slotsItem.hidden ? "ellipsis" : ""}>
              {slotsItem.formatter(this.item)}
            </div>
          );
        }
        // 默认没有使用自定义内容的渲染
        return (
          <div class={slotsItem.hidden ? "ellipsis" : ""}>
            {this.item[this.obj.componentOptions.propsData.prop]}
          </div>
        );
      }
    }
  },
};
</script>

<style lang="less" scoped>
.ellipsis {
  padding-left: 4px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  word-break: break-all;
}
</style>