<style lang="less">
    @import './login.less';
</style>

<template>
    <div class="login" @keydown.enter="handleSubmit">
        <div class="login-con">
            <Card :bordered="false">
                <p slot="title">
                    <Icon type="log-in"></Icon>
                    欢迎登录
                </p>
                <div class="form-con">
                    <Form ref="loginForm" :model="form" :rules="rules">
                        <FormItem prop="userName">
                            <Input v-model="form.userName" placeholder="请输入用户名">
                                <span slot="prepend">
                                    <Icon :size="16" type="person"></Icon>
                                </span>
                            </Input>
                        </FormItem>
                        <FormItem prop="password">
                            <Input type="password" v-model="form.password" placeholder="请输入密码">
                                <span slot="prepend">
                                    <Icon :size="14" type="locked"></Icon>
                                </span>
                            </Input>
                        </FormItem>
                        <FormItem>
                            <Button @click="handleSubmit" type="primary" long>登录</Button>
                        </FormItem>
                    </Form>
                    <p class="login-tip">输入任意用户名和密码即可</p>
                </div>
            </Card>
        </div>
    </div>
</template>

<script>
    import AccInfoUtil from '../utils/accInfoUtils';
    import { mapActions } from 'vuex';

    export default {
        data () {
            return {
                form: {
                    userName: 'iview_admin',
                    password: '',
                },
                rules: {
                    userName: [
                        { required: true, message: '账号不能为空', trigger: 'blur' },
                    ],
                    password: [
                        { required: true, message: '密码不能为空', trigger: 'blur' },
                    ],
                },
            };
        },
        methods: {
            ...mapActions(['login']),
            handleSubmit () {
                this.$refs.loginForm.validate((valid) => {
                    if (valid) {
                        this.login({
                            userName: this.form.userName,
                            pwd: AccInfoUtil.generateLocalPwd(this.form.userName, this.form.password),
                        }).then(res => {
                            if (res.status === 'ok') {
                                this.$Message.success('登录成功，正在跳转...');
                                let accInfo = res.data;
                                AccInfoUtil.setAccountInfo(accInfo);
                                // 设置路由信息
                                AccInfoUtil.setRouterPermission(accInfo.permission);
                                this.$store.commit('setAvator', 'https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=3448484253,3685836170&fm=27&gp=0.jpg');
                                this.$router.push({
                                    name: 'home_index',
                                });
                            } else {
                                this.$Message.error('登录出了一点问题... ' + res.msg);
                            }
                        });
                    }
                });
            },
        },
    };
</script>

<style>

</style>
