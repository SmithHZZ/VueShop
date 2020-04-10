<template>
    <div>
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>权限管理</el-breadcrumb-item>
        <el-breadcrumb-item>角色列表</el-breadcrumb-item>
      </el-breadcrumb>

      <el-card>
        <el-row>
          <el-col>
            <el-button type="primary">添加角色</el-button>
          </el-col>
        </el-row>

          <el-table
            :data="roleList"
            border
            stripe>
            <el-table-column type="expand">
              <template slot-scope="item">
                <el-row :class="['bdbottom', index ===0 ? 'bdtop' : '', 'vcenter']" v-for="(val,index) in item.row.children" :key="val.id">
                  <el-col :span="5">
                    <el-tag type="primary" closable @close="removeRightById(item.row, val.id)">{{val.authName}}</el-tag>
                    <i class="el-icon-caret-right"></i>
                  </el-col>
                  <el-col :span="19">
                    <el-row :class="[index2 === 0 ? '' : 'bdtop', 'vcenter']" v-for="(val2,index2) in val.children" :key="val2.id">
                      <el-col :span="6">
                        <el-tag type="success" closable @close="removeRightById(item.row, val2.id)">{{val2.authName}}</el-tag>
                        <i class="el-icon-caret-right"></i>
                      </el-col>
                      <el-col :span="18">
                        <el-tag type="warning" v-for="val3 in val2.children" :key="val3.id" closable @close="removeRightById(item.row, val3.id)">{{val3.authName}}</el-tag>
                      </el-col>
                    </el-row>
                  </el-col>
                </el-row>
              </template>
            </el-table-column>
            <el-table-column type="index" label="#">
            </el-table-column>
            <el-table-column
              label="角色名称"
              prop="roleName">
            </el-table-column>
            <el-table-column
              label="角色描述"
              prop="roleDesc">
            </el-table-column>
            <el-table-column
              label="操作">
              <template slot-scope="i">
                <el-button type="primary" icon="el-icon-edit" size="small">编辑</el-button>
                <el-button type="danger" icon="el-icon-delete" size="small">删除</el-button>
                <el-button type="warning" icon="el-icon-setting" size="small" @click="showSetRightDialog(i.row)">分配权限</el-button>
              </template>
            </el-table-column>
          </el-table>
      </el-card>
      <el-dialog
        title="分配权限"
        :visible.sync="setRightDialogVisible"
        @close="setRightDialogClosed"
        width="50%">
        <el-tree
          show-checkbox
          default-expand-all
          :default-checked-keys="checkKeys"
          node-key="id"
          ref="treeRef"
          :data="rightsList"
          :props="treeProps">
        </el-tree>
        <span slot="footer" class="dialog-footer">
          <el-button @click="setRightDialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="updateRights">确 定</el-button>
        </span>
      </el-dialog>

    </div>
</template>

<script>
    export default {
      data () {
        return {
          roleList: [],
          setRightDialogVisible: false,
          rightsList: [],
          treeProps: {
            label: 'authName',
            children: 'children'
          },
          checkKeys: [],
          roleId: ''
        }
      },
      methods: {
        getroleList () {
          this.$http.get('roles').then((data) => {
            if (data.data.meta.status !== 200) return this.$message.error('获取数据失败')
            this.roleList = data.data.data
          })
        },
        removeRightById (role, rightId) {
          this.$confirm('此操作将永久删除该权限, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            this.$http.delete(`roles/${role.id}/rights/${rightId}`)
              .then(data => {
                if (data.data.meta.status !== 200) return this.$message.error('删除失败')
                this.$message.success('删除成功')
                role.children = data.data.data
            })
          }).catch(() => {
            this.$message.error('取消删除')
          })
        },
        showSetRightDialog (role) {
          this.roleId = role.id
          this.$http.get('rights/tree').then(data => {
            if (data.data.meta.status !== 200) return this.$message.error('获取数据失败')
            this.rightsList = data.data.data
          })
          this.getLeafKeys(role, this.checkKeys)
          this.setRightDialogVisible = true
        },
        getLeafKeys (node, arr) {
          if (!node.children) {
            return arr.push(node.id)
          }
          node.children.forEach(item => {
            this.getLeafKeys(item, arr)
          })
        },
        setRightDialogClosed () {
          this.checkKeys = []
        },
        updateRights () {
          const keys = [this.$refs.treeRef.getCheckedKeys(), this.$refs.treeRef.getHalfCheckedKeys()]
          const idStr = keys.join(',')
          this.$http.post(`roles/${this.roleId}/rights`,
            { rids: idStr }).then(data => {
              if (data.data.meta.status !== 200) return this.$message.error('修改失败')
            this.$message.success('修改成功')
          })
          this.getroleList()
          this.setRightDialogVisible = false
        }
      },
      created () {
        this.getroleList()
      }
    }
</script>

<style lang="less" scoped>
.el-tag{
  margin: 7px;
}
  .bdtop{
    border-top: 1px solid #eeeeee;
  }
  .bdbottom{
    border-bottom: 1px solid #eeeeee;
  }
  .vcenter{
    display: flex;
    align-items: center;
  }
</style>
