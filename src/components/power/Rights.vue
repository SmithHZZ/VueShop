<template>
    <div>
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>权限管理</el-breadcrumb-item>
        <el-breadcrumb-item>权限列表</el-breadcrumb-item>
      </el-breadcrumb>

      <el-card>
        <el-table :data="rightList">
          <el-table-column type="index" label="#"></el-table-column>
          <el-table-column prop="authName" label="权限名称"></el-table-column>
          <el-table-column prop="path" label="路径"></el-table-column>
          <el-table-column prop="level" label="权限等级">
            <template slot-scope="item">
              <el-tag v-if="item.row.level === '0'" type="primary">一级</el-tag>
              <el-tag v-if="item.row.level === '1'" type="success">二级</el-tag>
              <el-tag v-if="item.row.level === '2'" type="warning">三级</el-tag>
            </template>
          </el-table-column>
        </el-table>
      </el-card>
    </div>
</template>

<script>
    export default {
      data () {
        return {
          rightList: []
        }
      },
      methods: {
        getRightList () {
          this.$http.get('rights/list').then((data) => {
            if (data.data.meta.status !== 200) return this.$message.error('获取数据失败')
            this.rightList = data.data.data
          })
        }
      },
      created () {
        this.getRightList()
      }
    }
</script>

<style lang="less" scoped>

</style>
