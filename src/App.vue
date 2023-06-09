<script setup>
import {
    CalendarOutlined,
    DashboardOutlined,
    ToolOutlined,
    UserOutlined,
    LockOutlined,
    CarryOutOutlined,
    FileOutlined
} from '@ant-design/icons-vue';
import {message} from "ant-design-vue";
import {ref, computed, reactive, watch} from 'vue';
import api from './api.js';

const formState = reactive({
    email: '',
    password: '',
});

const disabled = computed(() => {
    return !(formState.email && formState.password);
});

const collapsed = ref(true);
const selectedKeys = ref(['1']);
const token = ref(localStorage.token);
let latestToken = token.value; // 中间变量存储最新的token值
const is_admin = ref(localStorage.is_admin);

watch(token, (newToken) => {
    latestToken = newToken; // 更新最新的token值
});

// 在发送请求前应用最新的token值
api.interceptors.request.use((config) => {
    config.headers.Authorization = `Bearer ${latestToken}`;
    return config;
});

const name = ref(localStorage.name);

const signin = ref(false);
const login = () => {
    signin.value = true;
    api.post("/auth/login", formState).then((res) => {
        signin.value = false;
        let {data, msg} = res.data;
        token.value = data.token;
        name.value = data.name;
        is_admin.value = data.is_admin;
        localStorage.token = data.token;
        localStorage.name = data.name;
        localStorage.is_admin = data.is_admin;
        message.success(msg);
    }).catch((err) => {
        let {msg} = err.response.data;
        signin.value = false;
        message.error(msg);
    });
}

const logout = () => {
    api.delete("/auth/logout").then((res) => {
        let {msg} = res.data;
        localStorage.clear();
        token.value = null;
        name.value = null;
        message.success(msg);
    }).catch((err) => {
        let {msg} = err.response.data;
        localStorage.clear();
        token.value = null;
        name.value = null;
        localStorage.clear();
        message.warn("会话注销可能失败:" + msg);
    });
}

</script>
<template>
    <div v-if="!token" class="login">
        <a-form
                :model="formState"
                name="basic"
                :label-col="{ span: 8 }"
                :wrapper-col="{ span: 16 }"
                autocomplete="off"
                @submit="login"
                style="background-color: #FFFFFF;     z-index: 1;
             padding: 24px; box-sizing: border-box; display: flex; flex-direction: column; border-radius: 3px; box-shadow: #FFFFFF 0 0 1px 1px;
"

        >
            <h2 align="center">欢迎登录</h2>
            <p align="center">SITC 团委学生会 宣传部</p>
            <a-form-item
                    label="账户"
                    name="email"
                    :rules="[{ required: true, message: '请输入账户!' }]"
                    style="margin-top: 16px;"
            >
                <a-input v-model:value="formState.email"/>
            </a-form-item>

            <a-form-item
                    label="密码"
                    name="password"
                    :rules="[{ required: true, message: '请输入密码!' }]"
            >
                <a-input-password v-model:value="formState.password"/>
            </a-form-item>

            <div style="display: flex; align-items: center; justify-content: center; margin-top: 16px;">
                <a-form-item>
                    <a-button type="primary" html-type="submit" :loading="signin">登录</a-button>
                </a-form-item>
            </div>

        </a-form>
    </div>
    <div v-if="token" style="height: 100%;">
        <a-layout style="min-height: 100vh">
            <a-layout-sider v-model:collapsed="collapsed" collapsible>
                <div class="logo"
                     :style="{height: '64px',display: 'flex', alignItems: 'center', justifyContent: 'center'}">
                    <img src="./assets/imgs/logo.png" height="60" width="60"
                         style="border-radius: 50%; background-color: #FFFFFF"/>
                </div>
                <a-menu v-model:selectedKeys="selectedKeys" theme="dark" mode="inline">
                    <a-menu-item key="1" @click.prevent="$router.push('/')">
                        <dashboard-outlined/>
                        <span>工作台</span>
                    </a-menu-item>
                    <a-sub-menu key="sub4">
                        <template #title>
                            <carry-out-outlined/>
                            <span>签到</span>
                        </template>
                        <a-menu-item key="17">
                            <RouterLink to="/checkin/list">签到</RouterLink>
                        </a-menu-item>
                        <a-menu-item key="18" v-if="is_admin === '1'">
                            <RouterLink to="/checkin/manager">签到管理</RouterLink>
                        </a-menu-item>
                    </a-sub-menu>
                    <a-sub-menu key="sub1">
                        <template #title>
                          <span>
                            <user-outlined/>
                            <span>用户</span>
                          </span>
                        </template>
                        <a-menu-item key="3">
                            <RouterLink to="/user/detail">用户资料</RouterLink>
                        </a-menu-item>
                        <div v-if="is_admin === '1'">
                            <a-menu-item key="4">
                                <router-link to="/user/manager">用户管理</router-link>
                            </a-menu-item>
                            <a-menu-item key="5">
                                <router-link to="/user/add">用户添加</router-link>
                            </a-menu-item>
                            <a-menu-item key="6">
                                <router-link to="/user/batch">批量添加</router-link>
                            </a-menu-item>
                        </div>

                    </a-sub-menu>
                    <a-sub-menu key="sub2">
                        <template #title>
                          <span>
                            <tool-outlined/>
                            <span>设备</span>
                          </span>
                        </template>
                        <a-menu-item key="7">
                            <router-link to="/equipment/my">
                                我的设备
                            </router-link>
                        </a-menu-item>
                        <a-menu-item key="8">
                            <router-link to="/equipment/apply">
                                设备借用申请
                            </router-link>
                        </a-menu-item>
                        <div v-if="is_admin === '1'">
                            <a-menu-item key="9">
                                <router-link to="/equipment/manager">
                                    设备管理
                                </router-link>
                            </a-menu-item>
                            <a-menu-item key="10">
                                <router-link to="/equipment/audit">
                                    设备申请审核
                                </router-link>
                            </a-menu-item>
                            <a-menu-item key="19">
                                <router-link to="/equipment/history">
                                    设备出借历史
                                </router-link>
                            </a-menu-item>
                            <a-menu-item key="11">
                                <router-link to="/equipment/add">
                                    设备添加
                                </router-link>
                            </a-menu-item>
                            <a-menu-item key="12">
                                <router-link to="/equipment/batch">
                                    批量添加
                                </router-link>
                            </a-menu-item>
                        </div>
                    </a-sub-menu>
                    <a-sub-menu key="sub3">
                        <template #title>
                          <span>
                            <calendar-outlined/>
                            <span>活动</span>
                          </span>
                        </template>
                        <a-menu-item key="13">
                            <router-link to="/activity/list">
                                活动列表
                            </router-link>
                        </a-menu-item>
                        <div v-if="is_admin === '1'">
                            <a-menu-item key="14">
                                <router-link to="/activity/manager">
                                    活动管理
                                </router-link>
                            </a-menu-item>
                            <a-menu-item key="15">
                                <router-link to="/activity/add">
                                    活动添加
                                </router-link>
                            </a-menu-item>
                            <a-menu-item key="16">
                                <router-link to="/activity/enrollment">
                                    报名审核
                                </router-link>
                            </a-menu-item>
                        </div>
                    </a-sub-menu>
                </a-menu>
            </a-layout-sider>
            <a-layout>
                <a-layout-header style="background: #fff; padding: 0">
                    <div class="header-bar">
                        <div class="welcome-display" style="margin-left: 28px;">
                            <span>SITC 团委学生会宣传部-管理系统</span>
                        </div>
                        <div style="margin-left: 24px; margin-right: 28px;">
                            <span style="margin-right: 8px;">你好！{{ name }}</span>
                            <span @click="logout"><a>登出</a></span>
                        </div>
                    </div>
                </a-layout-header>
                <a-layout-content style="margin: 0 16px">
                    <RouterView></RouterView>
                </a-layout-content>
                <a-layout-footer style="text-align: center">
                    上海信息技术学校团委学生会宣传部 &copy; 2023
                </a-layout-footer>
            </a-layout>
        </a-layout>
    </div>

</template>

<style>
html, body {
    margin: 0;
    padding: 0;
}

#components-layout-demo-side .logo {
    height: 32px;
    margin: 16px;
    background: rgba(255, 255, 255, 0.3);
}

.site-layout .site-layout-background {
    background: #fff;
}

[data-theme='dark'] .site-layout .site-layout-background {
    background: #141414;
}

.header-bar {
    display: flex;
    justify-content: space-between;
}

@media screen and (max-width: 525px) {
    .welcome-display {
        display: none;
    }
}

.login {
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
}

.login:before {
    content: "";
    width: 100%;
    height: 100%;
    position: fixed;
    top: 0;
    left: 0;
    background-image: url("@/assets/imgs/Login_container_bg.jpg");
    background-size: cover;
    background-repeat: no-repeat;
    background-position: center;
    filter: blur(8px);

}

</style>