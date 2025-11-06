<template>
    <div class="login">
        <div class="login-title">
            后台管理系统
        </div>
        <div class="form-box">
            <!-- TabBar组件 -->
            <TabBar
                :tabs="['常见问题', '联系客服']"
                :on-change="handleTabChange"
                :initial-index="0"
                :container-style="{
                    width: '300px',
                    margin: '0 auto',
                    backgroundColor: 'rgba(255, 255, 255,.9)',
                    border: '1px solid #cdcdcd',
                    borderBottom: 'none',
                    borderTopLeftRadius: '5px',
                    borderTopRightRadius: '5px'
                }"
            />
            <!-- 内容区域 -->
            <div class="tab-content">
                <!-- 登录表单 -->
                <div v-if="currentTabIndex === 0" class="form-container">
                    <el-form class="login-form" label-position="top" size="small" :inline-message="inlinemessage" :model="loginForm" :rules="loginRule" ref="loginForm" label-width="100px">
                <el-form-item label="Email address" prop="email">
                    <el-input type="text" v-model="loginForm.email" placeholder="请输入邮箱"></el-input>
                </el-form-item>
                <el-form-item label="Password" prop="password">
                    <el-input type="password" @keypress.enter.native="submitForm('loginForm')" show-password v-model="loginForm.password" placeholder="请输入密码"></el-input>
                </el-form-item>

                <el-form-item>
                    <el-button class="login-btn" type="primary" @click="submitForm('loginForm')">Sign in</el-button>
                </el-form-item>
                    </el-form>
                </div>
                <!-- 联系客服 -->
                <div v-else-if="currentTabIndex === 1" class="contact-container">
                    <div class="contact-content">
                        <h3>联系客服</h3>
                        <p>如果您在使用过程中遇到任何问题，请通过以下方式联系我们：</p>
                        <ul>
                            <li>客服热线：400-123-4567</li>
                            <li>客服邮箱：support@ktv.com</li>
                            <li>工作时间：周一至周日 9:00-21:00</li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
import wsmLoading from "@/plugins/wsmLoading"
import jwt_decode from 'jwt-decode';
import TabBar from '@/components/TabBar.vue';
export default {
  components: {
    TabBar
  },
    name:"login",
    data(){

        return{
            inline:true,
            inlinemessage:false,
            loginForm:{
                email:"",
                password:""
            },
            loginRule:{
                email:[
                    {required:true,message:"邮箱不能为空",trigger:"blur"},
                    {type:"email",message:"请输入正确的邮箱",trigger:"blur"},
                ],
                password:[
                    {required:true,message:"密码不能为空",trigger:"blur"},
                    {min:6,max:20,message:"密码长度在6-20之间",trigger:"blur"}
                ],

            },

        currentTabIndex: 0
        }
    },
    methods:{
        // 登录
        submitForm(formName){
            this.$refs[formName].validate(valid => {
                if(valid){
                    wsmLoading.start("正在登录,请稍候...")
                    setTimeout(() => {
                        this.$axios.post("http://localhost:8633/api/admin/account/login", this.loginForm)
                            .then(res => {
                                if(res){
                                    // 解析token
                                    const {token} = res.data;
                                    localStorage.setItem("adminToken", token);
                                    const decoded = jwt_decode(token);
                                    // 存储vuex中
                                    this.$store.dispatch("setAdminInfo", decoded);
                                    this.$store.dispatch("isAdminAuthorization", true);
                                    this.$Message.success(`${decoded.username}登录成功`)
                                    wsmLoading.end();
                                    this.$router.push("/");
                                }
                            })
                            .catch(error => {
                                console.error(error.response);
                            })
                    }, 900)
                }
            })
        },
        // 获取验证码cookie
        getCookie(cname){
            var name = cname + "=";
            var ca = document.cookie.split(';');
            for(var i=0; i<ca.length; i++){
                var c = ca[i].trim();
                if (c.indexOf(name)==0) return c.substring(name.length,c.length);
            }
            return "";
        },
        // 刷新验证码
        refreshCaptcha(){
            this.$refs.captcha.src = "http://localhost:8633/api/safecode?d=" + Math.random();
        },
        // 切换Tab
        handleTabChange(index) {
            this.currentTabIndex = index;
        }
    }
}
</script>
<style lang="less" scoped>
.login {
    width: 100%;
    height: 100%;
    cursor: default;
    padding: 40px 0px;
    color: red;
    background-image: url(../assets/image/login-bg.jpg);
    background-size: 100% 100%;
    display: flex;
    flex-direction: column;

    .login-title {
        width: 100%;
        height: 150px;
        line-height: 150px;
        text-align: center;
        font-size: 40px;
        color: aliceblue;
        font-family: "隶书";
    }

    // 表单
    .form-box {
        width: 100%;
        // min-width: 500px;

        .login-form {
            width: 100%;
        }

        .form-container {
            width: 300px;
            margin: 0px auto;
            background-color: rgba(255, 255, 255, .9);
            border: 1px solid #cdcdcd;
            border-top: none;
            border-top-left-radius: 0;
            border-top-right-radius: 0;
            padding: 10px 15px;
            border-bottom-left-radius: 5px;
            border-bottom-right-radius: 5px;
        }

        .contact-container {
            width: 300px;
            margin: 0px auto;
            background-color: rgba(255, 255, 255, .9);
            border: 1px solid #cdcdcd;
            border-top: none;
            border-top-left-radius: 0;
            border-top-right-radius: 0;
            padding: 10px 15px;
            border-bottom-left-radius: 5px;
            border-bottom-right-radius: 5px;
            min-height: 250px;

            .contact-content {
                h3 {
                    margin-top: 10px;
                    color: #333;
                    text-align: center;
                }

                p {
                    color: #666;
                    margin: 15px 0;
                }

                ul {
                    list-style: none;
                    padding: 0;

                    li {
                        color: #666;
                        margin: 10px 0;
                        padding-left: 20px;
                        position: relative;

                        &:before {
                            content: "•";
                            position: absolute;
                            left: 0;
                            color: #1890ff;
                        }
                    }
                }
            }
        }

        .form-container {
            .login-form {
                width: 100%;

                .login-btn {
                    width: 100%;
                    background: linear-gradient(to bottom, rgba(47, 228, 89, 0.9), #26a744);
                    font-weight: 600;
                }
                .login-btn:hover {
                    background-color: rgb(94, 255, 132);
                }


            }
        }
    }
}
</style>
