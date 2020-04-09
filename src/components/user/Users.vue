<template>
    <div>
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>用户管理</el-breadcrumb-item>
        <el-breadcrumb-item>用户列表</el-breadcrumb-item>
      </el-breadcrumb>

      <el-card>
        <el-row :gutter="20">
          <el-col :span="8">
            <el-input placeholder="请输入内容" v-model="queryInfo.query" clearable @clear="getUserList">
              <el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
            </el-input>
          </el-col>
          <el-col :span="4">
            <el-button type="primary" @click="addDialogVisible = true">添加用户</el-button>
          </el-col>
        </el-row>

          <el-table :data="userlist" border stripe>
            <el-table-column type="index" label="#"></el-table-column>
            <el-table-column label="姓名" prop="username"></el-table-column>
            <el-table-column label="邮箱" prop="email"></el-table-column>
            <el-table-column label="电话" prop="mobile"></el-table-column>
            <el-table-column label="角色" prop="role_name"></el-table-column>
            <el-table-column label="状态">
              <template slot-scope="list">
                <el-switch v-model="list.row.mg_state" @change="changUserState(list.row)"></el-switch>
              </template>
            </el-table-column>
            <el-table-column label="操作" width="180px">
              <template slot-scope="list">
                <el-button type="primary" icon="el-icon-edit" @click="editUser(list.row.id)" size="mini"></el-button>
                <el-button type="danger" icon="el-icon-delete" size="mini" @click="confirmDelete(list.row.id)"></el-button>
                <el-tooltip :enterable="false" effect="dark" content="分配权限" placement="top">
                  <el-button type="warning" icon="el-icon-setting" size="mini" ></el-button>
                </el-tooltip>
              </template>
            </el-table-column>
          </el-table>
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          background
          layout="total, sizes, prev, pager, next"
          :page-size="queryInfo.pagesize"
          :page-sizes="[1, 2, 5, 10]"
          :current-page="queryInfo.pagenum"
          :pager-count="5"
          :total="total">
        </el-pagination>
      </el-card>
      <el-dialog
        title="添加用户"
        :visible.sync="addDialogVisible"
        @close="addDialogClosed"
        width="50%">
        <el-form
          :model="addForm"
          :rules="addFormRules"
          ref="addFormRef"
          label-width="70px">
          <el-form-item label="用户名" prop="username">
            <el-input v-model="addForm.username"></el-input>
          </el-form-item>

          <el-form-item label="密 码" prop="password">
            <el-input v-model="addForm.password"></el-input>
          </el-form-item>

          <el-form-item label="邮 箱" prop="email">
            <el-input v-model="addForm.email"></el-input>
          </el-form-item>

          <el-form-item label="手 机" prop="mobile">
            <el-input v-model="addForm.mobile"></el-input>
          </el-form-item>

        </el-form>
        <span slot="footer" class="dialog-footer">
          <el-button @click="addDialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="addUser">确 定</el-button>
        </span>
      </el-dialog>

      <el-dialog
        title="修改用户信息"
        :visible.sync="updateDialogVisible"
        @close="updateDialogClosed"
        width="50%">
        <el-form
          :model="updateForm"
          :rules="updateFormRules"
          ref="updateFormRef"
          label-width="70px">
          <el-form-item label="用户名" prop="username">
            <el-input v-model="updateForm.username" disabled></el-input>
          </el-form-item>

          <el-form-item label="邮 箱" prop="email">
            <el-input v-model="updateForm.email"></el-input>
          </el-form-item>

          <el-form-item label="手 机" prop="mobile">
            <el-input v-model="updateForm.mobile"></el-input>
          </el-form-item>

        </el-form>
        <span slot="footer" class="dialog-footer">
          <el-button @click="updateDialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="updateUser">确 定</el-button>
        </span>
      </el-dialog>
    </div>
</template>

<script>
    export default {
      data () {
        const checkEmail = (rule, value, cb) => {
          const regEmail = /^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(\.[a-zA-Z0-9_-])/
          if (regEmail.test(value)) {
            return cb()
          }
          cb(new Error('请输入合法的邮箱'))
        }
        const checkMobile = (rule, value, cb) => {
          const regMobile = /^(0|86|17951)?(13[0-9]|15[012356789]|17[678]|18[0-9]|14[57])[0-9]{8}$/
          if (regMobile.test(value)) {
            return cb()
          }
          cb(new Error('请输入合法的手机号'))
        }
        return {
          queryInfo: {
            query: '',
            pagenum: 1,
            pagesize: 5
          },
          userlist: [],
          total: 0,
          addDialogVisible: false,
          updateDialogVisible: false,
          addForm: {
            username: '',
            password: '',
            email: '',
            mobile: ''
          },
          updateForm: {},
          addFormRules: {
            username: [
              { required: true, message: '请输入用户名', trigger: 'blur' },
              { min: 3, max: 10, message: '用户名长度在3到10之间', trigger: 'blur' }
            ],
            password: [
              { required: true, message: '请输入密码', trigger: 'blur' },
              { min: 6, max: 15, message: '密码长度在6到15之间', trigger: 'blur' }
            ],
            email: [
              { required: true, message: '请输入邮箱', trigger: 'blur' },
              { validator: checkEmail, trigger: 'blur' }
            ],
            mobile: [
              { required: true, message: '请输入手机号', trigger: 'blur' },
              { validator: checkMobile, trigger: 'blur' }
            ]
          },
          updateFormRules: {
            email: [
              { required: true, message: '请输入邮箱', trigger: 'blur' },
              { validator: checkEmail, trigger: 'blur' }
            ],
            mobile: [
              { required: true, message: '请输入手机号', trigger: 'blur' },
              { validator: checkMobile, trigger: 'blur' }
            ]
          }
        }
      },
      methods: {
        getUserList () {
          this.$http.get('users',
            { params: this.queryInfo }).then((data) => {
              this.total = data.data.data.total
              this.userlist = data.data.data.users
          }, (e) => {
              this.$message.error('获取用户数据失败')
          })
        },
        handleSizeChange (newSize) {
          this.queryInfo.pagesize = newSize
          this.getUserList()
        },
        handleCurrentChange (newPage) {
          this.queryInfo.pagenum = newPage
          this.getUserList()
        },
        changUserState (user) {
          this.$http.put(`users/${user.id}/state/${user.mg_state}`).then((data) => {
            if (data.data.meta.status !== 200) {
              user.mg_state = !user.mg_state
              return this.$message.error('修改失败')
            }
            this.$message.success('修改成功')
          })
        },
        addDialogClosed () {
          this.$refs.addFormRef.resetFields()
        },
        updateDialogClosed () {
          this.$refs.updateFormRef.resetFields()
        },
        addUser () {
         this.$refs.addFormRef.validate(valid => {
            if (!valid) return this.$message.error('请按要求填写用户信息')
            this.$http.post('users', this.addForm).then((data) => {
              if (data.data.meta.status === 201) {
                this.$message.success('用户添加成功')
                this.addDialogVisible = false
                this.$refs.addFromRef.resetFields()
                this.getUserList()
              }
            })
          })
        },
        updateUser () {
          this.$refs.updateFormRef.validate(valid => {
            if (!valid) return this.$message.error('请按要求填写用户信息')
            this.$http.put(`users/${this.updateForm.id}`, this.updateForm).then((data) => {
              if (data.data.meta.status === 200) {
                this.$message.success('用户更新成功')
                this.updateDialogVisible = false
                this.$refs.updateFormRef.resetFields()
                this.getUserList()
              }
            })
          })
        },
        editUser (id) {
          this.$http.get(`users/${id}`).then((data) => {
            if (data.data.meta.status !== 200) return this.$message.error('获取用户数据失败')
            this.updateForm = data.data.data
            this.updateDialogVisible = true
          })
        },
        confirmDelete (id) {
          this.$confirm('此操作将永久删除该用户, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            this.$http.delete(`users/${id}`).then((data) => {
              if (data.data.meta.status !== 200) return this.$message.error('删除用户失败')
              this.$message.success('删除成功!')
              this.getUserList()
            })
          }).catch(() => {
            this.$message.error('取消删除')
          })
        }
      },
      created () {
        this.getUserList()
      }
    }
</script>

<style lang="less" scoped>

</style>
