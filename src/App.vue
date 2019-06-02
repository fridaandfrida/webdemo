<template>
    <div id="app">
        <!-- search -->
        <el-form :inline="true" :model="searchData" class="demo-form-inline">
            <el-form-item label="用户名">
                <el-input v-model="searchData.account" placeholder="用户名"></el-input>
            </el-form-item>
            <el-form-item label="真实姓名">
                <el-input v-model="searchData.realName" placeholder="真实姓名"></el-input>
            </el-form-item>
            <el-form-item>
                <el-button type="primary" @click="handleSearch">查询</el-button>
                <el-button type="primary" @click="handleAdd">添加</el-button>
            </el-form-item>
        </el-form>
        <!-- table -->
        <el-table :data="tableData" border style="width: 100%">
            <el-table-column prop="username" label="用户名" width="180">
            </el-table-column>
            <el-table-column prop="realName" label="真实姓名" width="180">
            </el-table-column>
            <el-table-column prop="mobile" label="手机号">
            </el-table-column>
            <el-table-column prop="title" label="职称">
            </el-table-column>
            <el-table-column prop="enabled" label="是否启用">
                <template slot-scope="scope">
                    {{scope.row.enabled?'是':'否'}}
                </template>
            </el-table-column>
            <el-table-column prop="password" label="密码">
            </el-table-column>
            <el-table-column prop="email" label="邮箱地址">
            </el-table-column>
            <el-table-column label="操作">
                <template slot-scope="scope">
                    <el-button size="mini" type="primary" @click="handleCheckDetail(scope.row.id)">
                        查看</el-button>
                    <el-button size="mini" type="warn" @click="handleDelete(scope.row.id)">删除</el-button>
                </template>
            </el-table-column>
        </el-table>
        <!-- 分页 -->
         <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="searchData.page"
          :page-sizes="[10, 20, 30, 40]"
          :page-size="searchData.size"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
          style="margin-top:20px;text-align:right;">
        </el-pagination>
        <!-- 添加和更新 -->
        <el-dialog :title="!!formData.id?'修改':'添加'" :visible.sync="visibleDialog">
          <el-form :model="formData" ref="userForm">
            <el-form-item label="用户名" prop="username" :label-width="formLabelWidth"
                          :rules="[{ required: true, message: '请输入用户名', trigger: 'blur' }]">
              <el-input v-model="formData.username"  autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="真实姓名" prop="realName" :label-width="formLabelWidth"
                          :rules="[{ required: true, message: '请输入真实姓名', trigger: 'blur' }]">
              <el-input v-model="formData.realName" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="手机号" prop="mobile" :label-width="formLabelWidth"
                          :rules="[{ required: true, message: '请输入手机号', trigger: 'blur' }]">
              <el-input v-model="formData.mobile" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="是否启用" prop="enabled" :label-width="formLabelWidth"
                          :rules="[{ required: true, message: '请输入用户名', trigger: 'blur' }]">
              <el-radio v-model="formData.enabled" :label="true">是</el-radio>
              <el-radio v-model="formData.enabled" :label="false">否</el-radio>
            </el-form-item>           
            <el-form-item label="职称" prop="title" :label-width="formLabelWidth">
              <el-input v-model="formData.title" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="密码" prop="password" :label-width="formLabelWidth">
              <el-input v-model="formData.password" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="邮箱地址" prop="email" :label-width="formLabelWidth"
                          :rules="[{ required: true, message: '请输入用户名', trigger: 'blur' }]">
              <el-input v-model="formData.email" autocomplete="off"></el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="visibleDialog = false">取 消</el-button>
            <el-button type="primary" @click="handleSubmit()">确 定</el-button>
          </div>
        </el-dialog>
    </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import api_server from './api/api_server.js'

export default {
    name: 'app',
    data() {
        return {
            searchData: {
                account: '',
                enabled: '',
                realName: '',
                page: 1,
                size: 10
            },
            tableData: [],
            total: 0,
            visibleDialog: false,
            formData: {
                id: 0,
                email: '',
                enabled: false,
                mobile: '',
                password: '',
                realName: '',
                title: '',
                username: ''
            },
            formLabelWidth: '120px'
        }
    },
    created() {
        this.getTableData();
    },
    methods: {
        //获取数据
        getTableData() {
            api_server.getTestData(this.searchData).then(res => {
                console.log(res)
                this.tableData = res.body.data;
                this.total = res.body.total;
            })
        },
        //添加数据
        handleAdd() {
            this.formData = {
                id: 0,
                email: '',
                enabled: false,
                mobile: '',
                password: '',
                realName: '',
                title: '',
                username: ''
            };
            this.visibleDialog = true;
        },
        //查看数据详情
        handleCheckDetail(id) {
            api_server.getTestDataDetail({ id: id }).then(res => {
                this.formData = res.body;
                this.visibleDialog = true;
            });
        },
        //查询数据
        handleSearch() {
            this.searchData.page = 1;
            this.getTableData();
        },
        //删除数据
        handleDelete(id) {
            api_server.delTestData({ id: id }).then(res => {
                if (res.status === 200) {
                    this.$message.success(res.message);
                    this.getTableData();
                } else {
                    this.$message.error(res.message);
                }
            })
        },
        //提交数据
        handleSubmit() {
            this.$refs['userForm'].validate((valid) => {
                if (valid) {
                    if (this.formData.id) {
                        api_server.upTestData(this.formData).then(res => {
                            if (res.status === 200) {
                                this.getTableData();
                                this.$message.success(res.message);
                                this.closeDialog();
                            } else {
                                this.$message.error(res.message);
                            }
                        });
                    } else {
                        api_server.addTestData(this.formData).then(res => {
                            if (res.status === 200) {
                                this.getTableData();
                                this.$message.success(res.message);
                                this.closeDialog();
                            } else {
                                this.$message.error(res.message);
                            }
                        });
                    }
                }
            });
        },
        // 关闭弹跳
        closeDialog() {
            this.visibleDialog = false;
        },
        //分页
        handleSizeChange(val) {
            this.searchData.size = val;
            this.getTableData();
        },
        //分页
        handleCurrentChange(val) {
            this.searchData.page = val;
            this.getTableData();
        }

    },
    components: {
        HelloWorld
    }
}
</script>

<style>

</style>
