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
          {{ label }}
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
  },
  data() {
    return {};
  },
  methods: {},
};
</script>

<style lang="less">
.dong_header_content {
  padding-left: 4px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  word-break: break-all;
}
</style>