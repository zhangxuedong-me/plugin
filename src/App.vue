<template>
  <div id="app" style="padding: 50px">
    <dong-table
      @row-click="rowClick"
      @row-mover="rowMover"
      @row-leave="rowLeave"
      align="left"
      border
      :data="data"
      :tableStyle="tableStyle"
      :timeOut="50"
      :animated="false"
      :height="200"
      :show-header="true"
      empty-text="暂无数据"
      :mouseSuspend="true"
      tree="children"
      row-key="id"
    >
      <dong-clumn-table
        width="260px"
        height="50px"
        align="left"
        label="日期"
        prop="date"
      >
        <template slot="header">
          <div><span>姓名</span>：<input type="text" /></div>
        </template>
        <template v-slot="obj">
          <div class="child">{{ obj.row.date + "aaaa" }}</div>
        </template>
      </dong-clumn-table>
      <dong-clumn-table
        label="姓名"
        width="230px"
        height="50px"
        align="left"
        prop="name"
        :hidden="true"
      >
      </dong-clumn-table>
      <dong-clumn-table
        label="年龄"
        width="260px"
        height="50px"
        align="left"
        prop="age"
        :hidden="true"
        :sortable="true"
        :sort="sortClick"
      >
      </dong-clumn-table>
      <dong-clumn-table
        label="住址"
        width="230px"
        height="50px"
        align="left"
        prop="address"
        :hidden="true"
      ></dong-clumn-table>
      <dong-clumn-table
        label="身份证号"
        width="300px"
        height="50px"
        align="left"
        prop="idNumber"
        :hidden="true"
      ></dong-clumn-table>
      <dong-clumn-table
        label="性别"
        width="220px"
        height="50px"
        align="left"
        prop="gender"
        :formatter="formatter"
        :hidden="true"
      ></dong-clumn-table>
      <dong-clumn-table label="操作" width="220px" height="50px" align="left">
        <button>编辑</button>
        <button>删除</button>
      </dong-clumn-table>
    </dong-table>
  </div>
</template>

<script>
import dongTable from "../packages/dong-table";
import dongClumnTable from "../packages/dong-clumn-table";
export default {
  name: "App",
  components: {
    dongTable,
    dongClumnTable,
  },
  data() {
    return {
      tableStyle: {
        thead: {
          fontSize: "14px",
          color: "#909399",
          fontType: "黑体",
          background: "#ffffff",
          fontWeight: 700,
        },
        tbody: {
          fontSize: "12px",
          color: "#606266",
          fontType: "宋体",
          background: "#ffffff",
          fontWeight: 400,
        },
      },
      data: [
        {
          id: 1,
          date: "2021-1-12",
          age: 22,
          name: "哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈",
          address: "河北省张家口市",
          idNumber: "130731199912456789",
          gender: "女",
          children: [
            {
              id: 111,
              date: "2015-12-24",
              name: "大漂亮",
              age: 18,
              address: "张家口市",
              idNumber: "130731200123456782",
              gender: "女",
              children: [
                {
                  id: 444,
                  date: "2015-12-24",
                  name: "AK-47",
                  age: 18,
                  address: "张家口市",
                  idNumber: "130731200123456782",
                  gender: "女",
                },
              ],
            },
          ],
        },
        {
          id: 2,
          date: "2020-12-23",
          name: "小垃圾",
          age: 12,
          address: "河北省邯郸市",
          idNumber: "130731199923456782",
          gender: "男",
          children: [
            {
              id: 222,
              date: "2015-12-24",
              name: "大姐姐",
              age: 18,
              address: "张家口市",
              idNumber: "130731200123456782",
              gender: "女",
            },
          ],
        },
        {
          id: 3,
          date: "2020-12-24",
          name: "小妹妹",
          age: 30,
          address: "河北省邯郸市",
          idNumber: "130731199923456782",
          gender: "男",
        },
        {
          id: 4,
          date: "2020-12-24",
          name: "啦啦啦啦",
          age: 21,
          address: "河北省邯郸市",
          idNumber: "130731199923456782",
          gender: "男",
        },
      ],
    };
  },
  methods: {
    rowClick(item, index) {
      // console.log(item, index);
    },
    rowMover(item) {
      // console.log(item);
    },
    rowLeave(item) {
      // console.log(item);
    },
    formatter(row) {
      return row.gender === "女" ? 0 : 1;
    },
    sortClick(data, type, prop) {
      if (type === "up") {
        return data.sort((a, b) => {
          return a[prop] - b[prop];
        });
      } else {
        return data.sort((a, b) => {
          return b[prop] - a[prop];
        });
      }
    },
    load(tree, resolve) {
      resolve([
        {
          id: Date.now() + "",
          date: "2019-12-24",
          name: "多来阿蒙",
          age: 26,
          address: "上海市",
          idNumber: "130731199923456782",
          gender: "女",
        },
        {
          id: Date.now() + 1999,
          date: "2017-12-24",
          name: "我爱小姐姐",
          age: 22,
          address: "河北省张家口市",
          idNumber: "130731199923456782",
          gender: "男",
        },
      ]);
    },
  },
};
</script>

<style>
</style>
