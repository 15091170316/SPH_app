<template>
    <header class="header">
        <!-- 头部的第一行 -->
        <div class="top">
            <div class="container">
                <div class="loginList">
                    <p>尚品汇欢迎您！</p>
                    <p v-if="!userInfo.phoneNum">
                        <span>请</span>
                        <router-link to="/login">登录</router-link>
                        <router-link to="/register" class="register">免费注册</router-link>
                    </p>
                    <p v-else>
                        <span>亲爱的 {{ userInfo.nickName || userInfo.phoneNum }} | </span>
                        <a @click="exitHandler">退出登录</a>
                    </p>
                </div>
                <div class="typeList">
                    <router-link to="/center">我的订单</router-link>
                    <router-link to="/shopcart">我的购物车</router-link>
                    <a href="###">我的尚品汇</a>
                    <a href="###">尚品汇会员</a>
                    <a href="###">企业采购</a>
                    <a href="###">关注尚品汇</a>
                    <a href="###">合作招商</a>
                    <a href="###">商家后台</a>
                </div>
            </div>
        </div>
        <!--头部第二行 搜索区域-->
        <div class="bottom">
            <h1 class="logoArea">
                <router-link to="/home" class="logo" title="尚品汇">
                    <img src="./images/logo.png" alt="">
                </router-link>
            </h1>
            <div class="searchArea">
                <form action="###" class="searchForm">
                    <input type="text" id="autocomplete" class="input-error input-xxlarge" v-model="keyword" />
                    <button class="sui-btn btn-xlarge btn-danger" type="button" @click="goSearch">搜索</button>
                </form>
            </div>
        </div>
    </header>
</template>
<script>
import {removeToken} from '@/utils/token'
export default {
    name: 'HeaderVue',
    data() {
        return {
            keyword: '',
        }
    },
    computed: {
        // 获取登录用户的信息 
        userInfo() {
            return this.$store.state.user.userInfo
        }
    },
    methods: {
        // 搜索按钮点击事件
        goSearch() {
            // 定义路由跳转路径和参数
            let location = { path: '/search', query: { keyword: this.keyword || undefined } }
            // 判断是否已有三级菜单的路由参数
            //      若有，则需要同时携带上三级菜单的参数一起跳转；若无，则直接跳转
            if (this.$route.query.categoryName) {     // 有三级菜单的路由参数
                for (const key in this.$route.query) {
                    if (key !== 'keyword') {  //防止把上次搜索的keyword参数添加进来
                        location.query[key] = this.$route.query[key]
                    }
                }
            }
            // 路由跳转
            this.$router.push(location)
        },
        // 退出登录
        async exitHandler(){
            try {
                await this.$store.dispatch('user/exitLogin')  //通知服务器退出登录
                removeToken()   //清除用户token信息
                this.$router.push('/login') //跳转到登录页
            } catch (error) {
                console.log(error.message);
            }
        }
    },
    async mounted() {
        // 全局事件总线，将input搜索框置空
        this.$bus.$on('emptySearch', () => {
            this.keyword = ''
        })
    }
}
</script>
<style scoped lang="less">
.header {
    &>.top {
        background-color: #eaeaea;
        height: 30px;
        line-height: 30px;

        .container {
            width: 1200px;
            margin: 0 auto;
            overflow: hidden;

            .loginList {
                float: left;

                p {
                    float: left;
                    margin-right: 10px;

                    .register {
                        border-left: 1px solid #b3aeae;
                        padding: 0 5px;
                        margin-left: 5px;
                    }
                }
            }

            .typeList {
                float: right;

                a {
                    padding: 0 10px;

                    &+a {
                        border-left: 1px solid #b3aeae;
                    }
                }

            }

        }
    }

    &>.bottom {
        width: 1200px;
        margin: 0 auto;
        overflow: hidden;

        .logoArea {
            float: left;

            .logo {
                img {
                    width: 175px;
                    margin: 25px 45px;
                }
            }
        }

        .searchArea {
            float: right;
            margin-top: 35px;

            .searchForm {
                overflow: hidden;

                input {
                    box-sizing: border-box;
                    width: 490px;
                    height: 32px;
                    padding: 0px 4px;
                    border: 2px solid #ea4a36;
                    float: left;

                    &:focus {
                        outline: none;
                    }
                }

                button {
                    height: 32px;
                    width: 68px;
                    background-color: #ea4a36;
                    border: none;
                    color: #fff;
                    float: left;
                    cursor: pointer;

                    &:focus {
                        outline: none;
                    }
                }
            }
        }
    }
}
</style>