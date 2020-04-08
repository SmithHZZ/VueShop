<template>
    <div class="login_container">
        <div class="login_box">
            <div class="avatar_box">
                <img src="../assets/logo.png">
            </div>
            <el-form ref="loginFormRef" label-width="0px" class="login_form" :model="form" :rules="loginFormRules">
                <el-form-item prop="username">
                    <el-input v-model="form.username" placeholder="请输入用户名" prefix-icon="iconfont icon-people_fill
"></el-input>
                </el-form-item>
                <el-form-item prop="password">
                    <el-input v-model="form.password" placeholder="请输入密码" prefix-icon="iconfont icon-lock_fill" type="password"></el-input>
                </el-form-item>
                <el-form-item class="btns">
                    <el-button type="primary" @click="login">登陆</el-button>
                    <el-button type="info" @click="reset">重置</el-button>
                </el-form-item>
            </el-form>
        </div>
    </div>
</template>

<script>
export default {
    data () {
        return {
            form: {
                username: 'zs',
                password: '123'
            },
            loginFormRules: {
                username: [
                    { required: true, message: '请输入用户名', trigger: 'blur' },
                    { min: 3, max: 20, message: '长度在3-20之间', trigger: 'blur' }
                ],
                password: [
                    { required: true, message: '请输入密码', trigger: 'blur' },
                    { min: 3, max: 20, message: '长度在3-20之间', trigger: 'blur' }
                ]
            }
        }
    },
    methods: {
        login () {
            this.$refs.loginFormRef.validate((valid) => {
                if (!valid) {
                    return
                }
                this.$http.post('login', this.form).then((data) => {
                    const user = data.data.data
                    if (!user) {
                        return this.$message.error('用户名或密码错误！')
                    }
                    this.$message.success('登陆成功')
                })
            })
        },
        reset () {
            this.$refs.loginFormRef.resetFields()
        }
    }
}

</script>

<style lang="less" scoped>
.login_container{
    background-color: #2b4b6b;
    height:100%;
}
.login_box{
    width: 450px;
    height: 300px;
    background-color: #ffffff;
    border-radius: 3px;
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%,-50%);
    .avatar_box{
        width: 130px;
        height: 130px;
        position: absolute;
        left: 50%;
        border:1px solid #eeeeee;
        background-color: #ffffff;
        transform: translate(-50%,-50%);
        border-radius: 50%;
        padding: 10px;
        box-shadow: 0 0 10px #dddddd;
        img{
            width: 100%;
            height: 100%;
            border-radius: 50%;
            background-color: #eeeeee;
        }
    }
}

.btns {
    display: flex;
    justify-content: flex-end;
}

.login_form {
    width: 100%;
    position: absolute;
    bottom: 0;
    padding:0 20px;
    box-sizing: border-box;
}

</style>
