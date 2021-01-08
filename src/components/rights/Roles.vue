<template>
  <div>
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>角色列表</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card>
      <!-- 角色列表区域 -->
      <!-- row-key="id" 是2019年3月提供的新特性，
      if there's nested data, rowKey is required.
      如果这是一个嵌套的数据，rowkey 是必须添加的属性 -->
      <el-table :data="roleList" border>
        <!-- 添加展开列 -->
        <el-table-column type="expand">
          <template slot-scope="scope">
            <el-row :class="['bdbottom',i1===0?'bdtop':'','vcenter']" v-for="(item1,i1) in scope.row.children"
                    :key="item1.id">
              <!-- 渲染一级权限 -->
              <el-col :span="5">
                <el-tag closable @close="removeRightById(scope.row,item1.id)">
                  {{item1.authName}}
                </el-tag>
                <i class="el-icon-caret-right"></i>
              </el-col>
              <!-- 渲染二，三级权限 -->
              <el-col :span="19">
                <!-- 通过for循环嵌套渲染二级权限  -->
                <el-row :class="[i2===0?'':'bdtop','vcenter']" v-for="(item2,i2) in item1.children" :key="item2.id">
                  <el-col :span="6">
                    <el-tag type="success" closable @close="removeRightById(scope.row,item2.id)">{{item2.authName}}
                    </el-tag>
                    <i class="el-icon-caret-right"></i>
                  </el-col>
                  <el-col :span="18">
                    <el-tag type="warning" v-for="(item3) in item2.children" :key="item3.id" closable
                            @close="removeRightById(scope.row,item3.id)">
                      {{item3.authName}}
                    </el-tag>
                  </el-col>
                </el-row>
              </el-col>
            </el-row>
          </template>
        </el-table-column>
        <el-table-column type="index"></el-table-column>
        <el-table-column label="角色名称" prop="roleName"></el-table-column>
        <el-table-column label="角色描述" prop="roleDesc"></el-table-column>
        <el-table-column label="操作" width="300px">
          <template slot-scope="">
            <el-button size="mini" type="primary" icon="el-icon-edit">编辑</el-button>
            <el-button size="mini" type="danger" icon="el-icon-delete">删除</el-button>
            <el-button size="mini" type="warning" icon="el-icon-setting" @click="showSetRightsDialog">分配权限</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-dialog
        title="设置权限"
        :visible.sync="setRightDialogVisible"
        width="50%"
      >
        <el-tree
          :data="rightsList"
          show-checkbox
          node-key="id"
          default-expand-all
          :default-checked-keys="defKeys"
          :props="treeProps"
          >
        </el-tree>
        <span slot="footer" class="dialog-footer">
    <el-button @click="setRightDialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="setRightDialogVisible = false">确 定</el-button>
  </span>
      </el-dialog>
    </el-card>
  </div>
</template>

<script>
export default {
  name: 'Roles',
  data () {
    return {
      roleList: [],
      setRightDialogVisible: false,
      rightsList: [],
      treeProps: {
        label: 'authName',
        children: 'children'
      },
      defKeys: [105, 116]
    }
  },
  created () {
    this.getRoleList()
  },
  methods: {
    async removeRightById (role, rightId) {
      const confirmResult = await this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).catch(err => err)
      console.log(confirmResult)
      if (confirmResult !== 'confirm') {
        return this.$message.info('你取消了删除')
      }
      const { data: res } = await this.$http.delete(`roles/${role.id}/rights/${rightId}`)
      role.children = res.data
    },
    async getRoleList () {
      const { data: res } = await this.$http.get('roles')
      // 如果返回状态为异常状态则报错并返回
      // if (res.meta.status !== 200)
      //     return this.$message.error('获取角色列表失败')
      // //如果返回状态正常，将请求的数据保存在data中
      // this.roleList = res.data
      console.log(res.data)
      this.roleList = res.data
    },
    // 显示权限对话框
    async showSetRightsDialog () {
      this.setRightDialogVisible = true
      const { data: res } = await this.$http.get('rights/tree')
      console.log(res)
      if (res.meta.status !== 200) {
        return this.$message.error(res.meta.msg)
      }
      this.rightsList = res.data
    }
  }
}
</script>

<style scoped>
  .el-card {
    margin-top: 20px;
  }

  .bdtop {
    border-top: 1px #eee solid;
  }

  .bdbottom {
    border-bottom: 1px #eee solid;
  }

  .el-tag {
    margin: 7px;
  }

  .vcenter {
    display: flex;
    align-items: center;
  }

</style>
