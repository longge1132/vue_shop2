<template>
  <div>
    <el-breadcrumb
      style="font-size: 16px;"
      separator-class="el-icon-arrow-right"
    >
      <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card style="margin-top: 20px;" class="box-card">
      <el-row :gutter="20">
        <el-col :span="8">
          <el-input
            placeholder="查询信息"
            v-model="queryList.query"
            clearable
            @clear="getUserList"
          >
            <el-button
              slot="append"
              icon="el-icon-search"
              @click="getUserList"
            ></el-button>
          </el-input>
        </el-col>
        <el-col :span="4">
          <el-button type="primary" @click="addDialogVisible = true">
            添加用户
          </el-button>
        </el-col>
      </el-row>
      <el-table
        :data="userList"
        border
        stripe
        style="width: 100%; margin-top: 20px;"
      >
        <el-table-column type="index"></el-table-column>
        <el-table-column
          prop="username"
          label="姓名"
          width="180"
        ></el-table-column>
        <el-table-column
          prop="email"
          label="邮箱"
          width="180"
        ></el-table-column>
        <el-table-column prop="mobile" label="电话"></el-table-column>
        <el-table-column prop="role_name" label="角色"></el-table-column>
        <el-table-column label="状态">
          <template slot-scope="scope">
            <el-switch
              v-model="scope.row.mg_state"
              active-color="#13ce66"
              inactive-color="#f42f42"
              @change="changeStatus(scope.row)"
            ></el-switch>
          </template>
        </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button
              type="primary"
              icon="el-icon-edit"
              size="mini"
            ></el-button>
            <el-button
              type="danger"
              icon="el-icon-delete"
              size="mini"
              @click="deleteUser(scope.row.id)"
            ></el-button>
            <el-tooltip content="分配权限" placement="top" :enterable="false">
              <el-button
                type="warning"
                icon="el-icon-share"
                size="mini"
              ></el-button>
            </el-tooltip>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页效果栏 -->
      <el-pagination
        style="margin-top: 15px;"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryList.pagenum"
        :page-sizes="[1, 2, 4, 8]"
        :page-size="queryList.pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="totalNum"
      ></el-pagination>

      <!-- 添加用户对话框 -->
      <el-dialog title="添加用户" :visible.sync="addDialogVisible" width="50%">
        <el-form
          :model="userInfo"
          label-width="80px"
          ref="userFormRef"
          :rules="userFormRelus"
        >
          <el-form-item label="用户名:" prop="username">
            <el-input v-model="userInfo.username"></el-input>
          </el-form-item>
          <el-form-item label="密码:" prop="password">
            <el-input v-model="userInfo.password"></el-input>
          </el-form-item>
          <el-form-item label="邮箱:" prop="email">
            <el-input v-model="userInfo.email"></el-input>
          </el-form-item>
          <el-form-item label="电话:" prop="mobile">
            <el-input v-model="userInfo.mobile"></el-input>
          </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
          <el-button @click="closeDialog">取 消</el-button>
          <el-button type="primary" @click="addUserForm">
            确 定
          </el-button>
        </span>
      </el-dialog>
    </el-card>
  </div>
</template>

<script>
export default {
  name: 'myUser',
  data() {
    var validateMobile = (rule, value, callback) => {
      if (value.length !== 11) {
        callback(new Error('请输入正确的手机号'))
      } else {
        callback()
      }
    }
    return {
      queryList: {
        query: '',
        pagenum: 1,
        pagesize: 6,
      },
      totalNum: 0,
      userList: [],
      addDialogVisible: false,
      // 添加用户信息：
      userInfo: {
        username: '',
        password: '',
        email: '',
        mobile: '',
      },
      userFormRelus: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          {
            min: 3,
            max: 15,
            message: '长度在 3 到 15 个字符',
            trigger: 'blur',
          },
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          {
            min: 6,
            max: 15,
            message: '长度在 6 到 15 个字符',
            trigger: 'blur',
          },
        ],
        mobile: [{ validator: validateMobile, trigger: 'blur' }],
      },
    }
  },
  created() {
    this.getUserList()
  },
  methods: {
    async getUserList() {
      const { data: res } = await this.$http.get('users', {
        params: this.queryList,
      })
      if (res.meta.status !== 200)
        return this.$message.error('列表信息获取失败！')
      this.userList = res.data.users
      this.totalNum = res.data.total
    },
    addUserForm() {
      this.$refs.userFormRef.validate(async (valid) => {
        if (valid) {
          const { data: res } = await this.$http.post('users', this.userInfo)
          if (res.meta.status !== 201) return this.$message.error(res.meta.msg)
          this.$message.success('用户添加成功')
          this.addDialogVisible = false
          this.$refs.userFormRef.resetFields()
        } else {
          this.$message.error('请检查输入信息！')
        }
      })
      this.addDialogVisible = false
    },
    async deleteUser(id) {
      const sure = confirm('确认删除？')
      if (sure) {
        const { data: res } = await this.$http.delete('users/' + id)
        if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
        this.$message.success('删除成功！')
        this.getUserList()
      }
    },
    closeDialog() {
      this.addDialogVisible = false
      this.$refs.userFormRef.resetFields()
    },
    handleSizeChange(val) {
      this.queryList.pagesize = val
      this.getUserList()
    },
    handleCurrentChange(val) {
      this.queryList.pagenum = val
      this.getUserList()
    },
    async changeStatus(userinfo) {
      const { data: res } = await this.$http.put(
        `users/${userinfo.id}/state/${userinfo.mg_state}`,
      )
      if (res.meta.status !== 200) {
        userinfo.mg_state = !userinfo.mg_state
        return this.$message.error(res.meta.msg)
      }
      this.$message.success(res.meta.msg)
    },
  },
}
</script>

<style lang="less" scoped>
.el-card {
  margin-top: 0;
}
</style>
