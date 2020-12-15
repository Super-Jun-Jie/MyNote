# Vue表单提交
```vue
<template>
    <div>
                <!--:visible.sync 控制对话框显示-->
                <el-dialog title="添加用户" :visible.sync="addUserDialogVisible" width="50%" @close="addUserDialogClosed"
                           :close-on-click-modal="false">
                    <!-- 内容主体 -->
                    <el-form
                            :model="addUserForm"
                            ref="addUserFormRef"
                            :rules="addUserFormRules"
                            label-width="100px"
                    >
                        <el-form-item label="用户名" prop="username">
                            <el-input v-model="addUserForm.username"></el-input>
                        </el-form-item>
                        <el-form-item label="密码" prop="password">
                            <el-input v-model="addUserForm.password"></el-input>
                        </el-form-item>
                        <el-form-item label="邮箱" prop="email">
                            <el-input v-model="addUserForm.email"></el-input>
                        </el-form-item>
                        <el-form-item label="手机" prop="mobile">
                            <el-input v-model="addUserForm.mobile"></el-input>
                        </el-form-item>
                    </el-form>
                    <span slot="footer" class="dialog-footer">
                        <el-button @click="addUserDialogVisible = false">取 消</el-button>
                        <el-button type="primary" @click="saveUser">确 定</el-button>
                    </span>
                </el-dialog>
    </div>
</template>
<script>
    export default {
    
            data() {
    
                // 自定义邮箱规则
                var checkEmail = (rule, value, callback) => {
                    const regEmail = /^\w+@\w+(\.\w+)+$/
                    if (regEmail.test(value)) {
                        // 合法邮箱
                        return callback()
                    }
                    callback(new Error('请输入合法邮箱'))
                }
                // 自定义手机号规则
                var checkMobile = (rule, value, callback) => {
                    const regMobile = /^1[34578]\d{9}$/
                    if (regMobile.test(value)) {
                        return callback()
                    }
                    // 返回一个错误提示
                    callback(new Error('请输入合法的手机号码'))
                }
                return {
                    addUserDialogVisible: false,
                    addUserForm: {
                        username: '',
                        password: '',
                        email: '',
                        mobile: ''
                    },
                    addUserFormRules: {
                        username: [
                            {required: true, message: '请输入用户名', trigger: 'blur'},
                            {
                                min: 2,
                                max: 10,
                                message: '用户名的长度在2～10个字',
                                trigger: 'blur'
                            }
                        ],
                        password: [
                            {required: true, message: '请输入用户密码', trigger: 'blur'},
                            {
                                min: 6,
                                max: 18,
                                message: '用户密码的长度在6～18个字',
                                trigger: 'blur'
                            }
                        ],
                        email: [
                            {required: true, message: '请输入邮箱', trigger: 'blur'},
                            {validator: checkEmail, trigger: 'blur'}
                        ],
                        mobile: [
                            {required: true, message: '请输入手机号码', trigger: 'blur'},
                            {validator: checkMobile, trigger: 'blur'}
                        ]
                    }
                }
            },
            methods: {

                //新增文章
                async saveUser() {
    
                        const {data: res} = await this.$http.post('user', this.User)
                        console.log(res)
                        this.$message.success('添加用户成功！')
                        // 隐藏添加用户对话框
                        this.addUserDialogVisible = false
                        this.getUserList()
                },
                //关闭新增对话框
                addUserDialogClosed() {
                    this.$refs.addUserFormRef.resetFields()
                },
            }
    
        }
</script>
```
