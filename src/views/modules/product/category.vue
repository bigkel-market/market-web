<template>
  <el-tree
    :data="data"
    :props="defaultProps"
    :expand-on-click-node="false"
    show-checkbox
    node-key="catId"
    :default-expanded-keys="expandKey"
  >
    <span class="custom-tree-node" slot-scope="{ node, data }">
      <span>{{ node.label }}</span>
      <span>
        <el-button
          v-if="node.level <= 2"
          type="text"
          size="mini"
          @click="() => append(data)"
        >
          Append
        </el-button>
        <el-button
          v-if="node.childNodes.length == 0"
          type="text"
          size="mini"
          @click="() => remove(node, data)"
        >
          Delete
        </el-button>
      </span>
    </span>
  </el-tree>
</template>

<script>
export default {
  data() {
    return {
      data: [],
      defaultProps: {
        children: "children",
        label: "name",
      },
      expandKey: []
    };
  },

  created() {
    this.getMenus();
  },

  methods: {
    getMenus() {
      this.$http({
        url: this.$http.adornUrl("/product/category/list/tree"),
        method: "get",
      }).then(({ data }) => {
        this.data = data.data;
      });
    },

    append(data) {

    },

    remove(node, data) {
      var ids = [data.catId]
      this.$confirm(`是否删除【${data.name}】菜单`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
          url: this.$http.adornUrl('/product/category/delete'),
          method: 'post',
          data: this.$http.adornData(ids, false)
        }).then(({ data }) => {
          this.$message({
            message: '恭喜你，这是一条成功消息',
            type: 'success'
          });
          // 获取删除之后的树
          this.getMenus()
          // 展开当前删除节点的父节点
          this.expandKey = [node.parent.data.catId]
        })
      }).catch(() => {

      });

    },
  },
};
</script>

<style>
</style>