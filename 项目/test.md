<template>
    <div>
        <el-button type="success" class="el-icon-user" @click="handleRegister">注册</el-button>
        <h1>This is HomePage</h1>
        <el-button @click="handleLogin" class="el-icon-user-solid" type="primary">登录</el-button>


        <!-- 注册弹框 -->
        <el-dialog title="注册" :visible.sync="dialogFormVisible1">

            <el-form :model="saveUser">

                <el-form-item label="用户名" :label-width="formLabelWidth">
                    <el-input v-model="saveUser.userName" autocomplete="off" placeholder="用户名不能为空"></el-input>
                </el-form-item>

                <el-form-item label="手机号" :label-width="formLabelWidth">
                    <el-input v-model="saveUser.phoneNumber" autocomplete="off" placeholder="手机号不能为空"></el-input>
                </el-form-item>

                <el-form-item label="邮箱" :label-width="formLabelWidth">
                    <el-input v-model="saveUser.emailNumber" autocomplete="off" placeholder="邮箱不能为空"></el-input>
                </el-form-item>

                <el-form-item label="密码" :label-width="formLabelWidth">
                    <el-input v-model="saveUser.password" autocomplete="off" type="password"
                        placeholder="密码不能为空"></el-input>
                </el-form-item>

                <el-form-item label="确认密码" :label-width="formLabelWidth">
                    <el-input v-model="saveUser.confirmPassword" autocomplete="off" type="password"
                        placeholder="密码不能为空"></el-input>
                </el-form-item>

            </el-form>


            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>

                <el-button type="primary" @click="save(saveUser)">确 定</el-button>
            </div>
        </el-dialog>

        <!-- 登录弹框 -->
        <el-dialog title="登录" :visible.sync="dialogFormVisible2">
            <el-form :model="loginUser">
                <el-form-item label="账号" :label-width="formLabelWidth">
                    <el-input style="width: 90%" v-model="loginUser.phoneNumber" autocomplete="off"
                        placeholder="请输入你的手机号/邮箱"></el-input>
                </el-form-item>
                <el-form-item label="密码" :label-width="formLabelWidth">
                    <el-input style="width: 90%" v-model="loginUser.password" autocomplete="off" type="password"
                        placeholder="请输入你密码"></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="save(saveUser)">确 定</el-button>
            </div>
        </el-dialog>


    </div>
</template>

<script>
export default {
    name: 'HomePage',
    data() {
        return {
            formLabelWidth: '120px',
            dialogFormVisible1: false,
            dialogFormVisible2: false,
            saveUser: {
                userName: '',
                phoneNumber: '',
                emailNumber: '',
                password: '',
                confirmPassword: '', // 确认密码
            },

            loginUser: {
                phoneNumber: '',
                emailNumber: '',
                password: '',
            }
        }
    },
    methods: {
        handleRegister() {
            this.dialogFormVisible1 = true
        },
        handleLogin() {
            this.dialogFormVisible2 = true
        }
    }
}
</script>

<style>
.el-icon-user {
    float: right;
}

.el-icon-user-solid {
    float: right;
}
</style>