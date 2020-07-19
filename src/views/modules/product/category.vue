<template>
  <div>
    <el-tree
      :data="menus"
      :props="defaultProps"
      @node-click="handleNodeClick"
      :expand-on-click-node="false"
      show-checkbox
      node-key="catId"
      :default-expanded-keys='expandedKeys'
    >
      <span class="custom-tree-node" slot-scope="{ node, data }">
        <span>{{ node.label }}</span>
        <span>
          <el-button v-if="node.level<=2" type="text" size="mini" @click="() => append(data)">Append</el-button>
          <el-button
            v-if="node.childNodes.length==0"
            type="text"
            size="mini"
            @click="() => remove(node, data)"
          >Delete</el-button>
        </span>
      </span>
    </el-tree>
  </div>
</template>

<script>
export default {
  //import 引入的组件需要注入到对象中才能使用
  components: {},
  props: {},
  data() {
    return {
      menus: [],
      expandedKeys:[],
      defaultProps: {
        children: "children",
        label: "name"
      }
    };
  },
  methods: {
    handleNodeClick(data) {
      console.log(data);
    },
    getMenus() {
      this.$http({
        url: this.$http.adornUrl("/product/category/list/tree"),
        method: "get"
      }).then(({ data }) => {
        //解构 ({})
        console.log("成功获取到数据:", data.data);
        this.menus = data.data;
      });
    },
    append(data) {
      //console.log("append",data)
    },

    remove(node, data) {
      //弹框提醒
      this.$confirm(`是否删除【${data.name}】菜单?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          //  console.log("remove",node,data)
          var ids = [data.catId];
          this.$http({
            url: this.$http.adornUrl("/product/category/delete"),
            method: "post",
            data: this.$http.adornData(ids, false)
          }).then(({ data }) => {
            this.$message({
              message: "菜单删除成功",
              type: "success"
            });
            this.expandedKeys = [node.parent.data.catId]
            this.getMenus();
          });
        })
        .then(() => {
          this.$message({
            type: "success",
            message: "删除成功!"
          });
        })
        .catch(() => {});
    }
  },
  //计算属性
  computed: {},
  //监控data中数据变化
  watch: {},
  //方法
  created() {
    this.getMenus();
  },
  //生命周期 - 挂载完成（可以访问DOM元素）
  mounted() {},
  beforeCreate() {},
  //生命周期 - 创建之前
  beforeMount() {},
  //生命周期 - 挂载之前
  beforeUpdate() {},
  //声明周期 - 更新之前
  updated() {},
  //生命周期 - 更新之后
  beforeDestroy() {},
  //生命周期 - 销毁之前
  destroyed() {},
  //生命周期 - 销毁之后
  activated() {}
  //缓存keep-alive
};
</script>

<style scoped>
</style>
