<template>
  <div class="users-container">
    <!--  面包屑导航区域  -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{path:'/home'}">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片区域  -->
    <el-card>
      <!--搜索和添加区域-->
      <el-row :gutter="20">
        <el-col :span="7">
          <el-input placeholder="请输入内容" class="input-with-select">
            <el-button slot="append" icon="el-icon-search"></el-button>
          </el-input>
        </el-col>
        <el-col :span="4">
          <el-button type="primary" @click="addUserDialog=true">添加用户</el-button>
        </el-col>
      </el-row>
      <!--      用户列表区域-->
      <el-table :data="userlist" border stripe>
        <el-table-column label="#" type="index"></el-table-column>
        <el-table-column label="姓名" prop="username"></el-table-column>
        <el-table-column label="邮箱" prop="email"></el-table-column>
        <el-table-column label="电话" prop="mobile"></el-table-column>
        <el-table-column label="角色" prop="role_name"></el-table-column>
        <el-table-column label="状态" prop="mg_state">
          <template slot-scope="scope">
            <el-switch @change="changeStatus(scope.row.id,scope.row.mg_state)" v-model="scope.row.mg_state" active-color="#409eff" inactive-color="#dcdfe6"></el-switch>
          </template>
        </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button
              size="mini"
              type="primary"
              @click="handleEdit(scope.row.id)">
              <i class="el-icon-edit"></i>
            </el-button>
            <el-button
              size="mini"
              type="danger"
              @click="handleDelete(scope.row.id)"><i class="el-icon-delete"></i></el-button>
            <el-tooltip content="分配角色" placement="top" :enterable="false">
              <el-button
                size="mini"
                type="warning"
                @click="handleSetting(scope.$index, scope.row)"><i class="el-icon-setting"></i></el-button>
            </el-tooltip>
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pagenum"
        :page-sizes="[1, 2, 5, 10]"
        :page-size="queryInfo.pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total">
      </el-pagination>
    </el-card>
    <!-- 添加用户 弹框 -->
    <el-dialog title="添加用户" :visible.sync="addUserDialog" destroy-on-close>
      <el-form :model="user" :rules="rules" label-position="right" label-width="80px">
        <el-form-item label="用户名" prop="name">
          <el-input :model="user.name"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input :model="user.password" type="password"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input :model="user.email" type="email"></el-input>
        </el-form-item>
        <el-form-item label="手机号" prop="mobile">
          <el-input :model="user.mobile"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer">
        <el-button @click="addUserDialog = false">取 消</el-button>
        <el-button type="primary" @click="dialogFormVisible = false">确 定</el-button>
      </div>
    </el-dialog>
<!--  编辑用户弹窗  -->
    <el-dialog title="编辑用户" :visible.sync="editUserDialog" destroy-on-close>
      <el-form :model="userInfo" label-position="right" label-width="80px">
        <el-form-item label="用户名" prop="username">
          <el-input  disabled v-model="userInfo.username"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email"  :rules="{required:true,message:'请输入邮箱'}">
          <el-input  type="email" v-model="userInfo.email"></el-input>
        </el-form-item>
        <el-form-item label="手机号" prop="mobile" :rules="{required:true,message:'请输入手机号'}">
          <el-input  v-model="userInfo.mobile" ></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer">
        <el-button @click="editUserDialog = false">取 消</el-button>
        <el-button type="primary" @click="saveUser(userInfo.id)">确 定</el-button>
      </div>
    </el-dialog>
<!--  设置用户角色  -->
    <el-dialog title="分配角色" :visible.sync="settingDialog" destroy-on-close></el-dialog>
  </div>
</template>

<script>
export default {
  name: 'UsersPage',
  data () {
    return {
      userlist: [],
      user: {
        name: '',
        password: '',
        email: '',
        mobile: ''
      },
      rules: {
        name: [{
          required: true, message: '请输入用户名'
        }],
        password: [{
          required: true, message: '请输入密码'
        }],
        email: [{
          required: true, message: '请输入邮箱'
        }],
        mobile: [{
          required: true, message: '请输入手机号'
        }]
      },
      total: 0,
      // 获取用户列表的参数对象
      queryInfo: {
        query: '',
        pagenum: 1,
        pagesize: 2
      },
      userInfo: { id: '', email: '', mobile: '', username: '' }, // 用户信息
      addUserDialog: false, // 添加用户弹框
      editUserDialog: false, // 编辑用户弹窗
      settingDialog: false
    }
  },
  computed: {},
  watch: {},
  created () {
    this.getUserList()
  },
  mounted () {},
  methods: {
    async getUserList () {
      const { data: res } = await this.$http.get('users', { params: this.queryInfo })
      if (res.meta.status !== 200) {
        return this.$message.error(res.meta.msg)
      }
      this.userlist = res.data.users
      this.total = res.data.total
      console.log(res)
    },
    // 改变用户状态
    async changeStatus(id, status) {
      const { data: res } = await this.$http.put(`users/${id}/state/${status}`)
      if (res.meta.status !== 200) {
        return this.$message.error(res.meta.msg)
      } else {
        return this.$message.success(res.meta.msg)
      }
    },
    // 监听每页长度变化
    handleSizeChange(newSize) {
      this.queryInfo.pagesize = newSize
      this.getUserList()
    },
    // 监听页码变化
    handleCurrentChange(page) {
      this.queryInfo.pagenum = page
      this.getUserList()
    },
    // 编辑用户
    handleEdit(id) {
      this.$http.get(`users/${id}`).then(res => {
        if (res.data.meta.status === 200) {
          console.log(res)
          this.userInfo = res.data.data
          this.editUserDialog = true
        }
      })
    },
    // 编辑用户提交
    saveUser(id) {
      this.$http.put(`users/${id}`, { ...this.userInfo }).then(res => {
        if (res.data.meta.status === 200) {
          this.$message.success('编辑成功！')
          this.editUserDialog = false
          this.getUserList()
        } else {
          this.$message.error('编辑失败')
        }
      })
    },
    handleDelete(id) {
      console.log(id)
      this.$confirm('此操作将永久删除该用户，是否继续？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http.delete(`users/${id}`).then(res => {
          if (res.data.meta.status === 200) {
            this.$message.success('删除成功')
            this.getUserList()
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    },
    handleSetting () {
      this.settingDialog = true // 设置用户角色
    }
  }
}
</script>

<style scoped lang="less">
.el-table {
  margin-top: 15px;
}
.el-pagination {
  margin-top: 15px;
}
</style>
