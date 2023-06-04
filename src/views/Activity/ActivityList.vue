<script setup>
import {ref, createVNode} from "vue";
import { ExclamationCircleOutlined } from '@ant-design/icons-vue';
import {Empty, Modal} from "ant-design-vue";


const current = ref(1);

const data = [];
data.push(1);

const visible = ref(false);
const showModal = () => {
    visible.value = true;
};
const hideModal = () => {
    visible.value = false;
};

function cancelEnroll(id) {

}

const showAlert = ref(false)

const enroll = id => {
    Modal.confirm({
        title: '确认',
        icon: createVNode(ExclamationCircleOutlined),
        content: '确认报名？',
        okText: '确认',
        cancelText: '取消',
        onOk() {
            countDown();
        }
    });

}

const countDown = () => {
    let secondsToGo = 5;
    const modal = Modal.success({
        title: '报名成功',
        content: `提示框将在 ${secondsToGo}s 后关闭`,
    });
    const interval = setInterval(() => {
        secondsToGo -= 1;
        modal.update({
            content: `提示框将在 ${secondsToGo}s 后关闭`,
        });
    }, 1000);
    setTimeout(() => {
        clearInterval(interval);
        modal.destroy();
    }, secondsToGo * 1000);
};

</script>

<template>
    <a-layout-content :style="{margin: '16px', height: '100%'}">
        <h2>
            活动列表
        </h2>
        <a-tabs v-model:activeKey="activeKey" type="card">
            <a-tab-pane key="1" tab="可报名">
                <a-descriptions-item v-if="data.length === 0">
                    <div style="height: 100%; display: flex; flex-direction: column; align-items: center; justify-content: center;">
                        <a-empty :image="Empty.PRESENTED_IMAGE_SIMPLE" style="width: 100%;  "/>
                    </div>
                </a-descriptions-item>
                <a-space direction="vertical" :size="5" style="height: 100%">

                    <a-descriptions v-for="item in data" title="活动公告"
                                    style="background-color: #FFFFFF; padding: 16px; box-sizing: border-box;">
                        <a-descriptions-item label="地点">校内8#影视中心</a-descriptions-item>
                        <a-descriptions-item label="需求">demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo</a-descriptions-item>
                        <a-descriptions-item label="开始时间">2023/05/14 14:45:00</a-descriptions-item>
                        <a-descriptions-item label="结束时间">2023/05/14 14:45:00</a-descriptions-item>
                        <a-descriptions-item label="面向人员类型">面向全体和指派</a-descriptions-item>
                        <a-descriptions-item label="操作">
                            <a-row>
                                <a-col v-if="true">
                                    <a-button type="primary" @click="enroll(item.id)">报名</a-button>
                                </a-col>
                                <a-col v-if="false">
                                    <a-button type="primary" danger @click="cancelEnroll(item.id)">撤销</a-button>
                                </a-col>
                            </a-row>
                        </a-descriptions-item>

                    </a-descriptions>
                    <a-pagination align="center" style="margin-top: 8px;" v-model:current="current" simple pageSize="5"
                                  :total="data.length" v-if="data.length !== 0"/>
                </a-space>
            </a-tab-pane>
            <a-tab-pane key="2" tab="待审核">
                <a-descriptions-item v-if="data.length === 0">
                <div style="height: 100%; display: flex; flex-direction: column; align-items: center; justify-content: center;">
                    <a-empty :image="Empty.PRESENTED_IMAGE_SIMPLE" style="width: 100%;  "/>
                </div>
            </a-descriptions-item>
                <a-space direction="vertical" :size="5" style="height: 100%">

                    <a-descriptions v-for="item in data" title="活动公告"
                                    style="background-color: #FFFFFF; padding: 16px; box-sizing: border-box;">
                        <a-descriptions-item label="地点">校内8#影视中心</a-descriptions-item>
                        <a-descriptions-item label="需求">demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo</a-descriptions-item>
                        <a-descriptions-item label="开始时间">2023/05/14 14:45:00</a-descriptions-item>
                        <a-descriptions-item label="结束时间">2023/05/14 14:45:00</a-descriptions-item>
                        <a-descriptions-item label="申请时间">2023/05/14 14:45:00</a-descriptions-item>
                        <a-descriptions-item label="面向人员类型">面向全体和指派</a-descriptions-item>
                    </a-descriptions>
                    <a-pagination align="center" style="margin-top: 8px;" v-model:current="current" simple pageSize="5"
                                  :total="data.length" v-if="data.length !== 0"/>
                </a-space>
            </a-tab-pane>
            <a-tab-pane key="3" tab="已同意">
                <a-descriptions-item v-if="data.length === 0">
                    <div style="height: 100%; display: flex; flex-direction: column; align-items: center; justify-content: center;">
                        <a-empty :image="Empty.PRESENTED_IMAGE_SIMPLE" style="width: 100%;  "/>
                    </div>
                </a-descriptions-item>
                <a-space direction="vertical" :size="5" style="height: 100%">

                    <a-descriptions v-for="item in data" title="活动公告"
                                    style="background-color: #FFFFFF; padding: 16px; box-sizing: border-box;">
                        <a-descriptions-item label="地点">校内8#影视中心</a-descriptions-item>
                        <a-descriptions-item label="需求">demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo</a-descriptions-item>
                        <a-descriptions-item label="开始时间">2023/05/14 14:45:00</a-descriptions-item>
                        <a-descriptions-item label="结束时间">2023/05/14 14:45:00</a-descriptions-item>
                        <a-descriptions-item label="申请时间">2023/05/14 14:45:00</a-descriptions-item>
                        <a-descriptions-item label="面向人员类型">面向全体和指派</a-descriptions-item>
                        <a-descriptions-item label="审批人学籍号">22100001</a-descriptions-item>
                        <a-descriptions-item label="审批人姓名">Demo</a-descriptions-item>
                        <a-descriptions-item label="同意时间">2023/05/14 14:45:00</a-descriptions-item>
                    </a-descriptions>
                    <a-pagination align="center" style="margin-top: 8px;" v-model:current="current" simple pageSize="5"
                                  :total="data.length" v-if="data.length !== 0"/>
                </a-space>
            </a-tab-pane>
            <a-tab-pane key="4" tab="曾驳回">
                <a-descriptions-item v-if="data.length === 0">
                    <div style="height: 100%; display: flex; flex-direction: column; align-items: center; justify-content: center;">
                        <a-empty :image="Empty.PRESENTED_IMAGE_SIMPLE" style="width: 100%;  "/>
                    </div>
                </a-descriptions-item>
                <a-space direction="vertical" :size="5" style="height: 100%">

                    <a-descriptions v-for="item in data" title="活动公告"
                                    style="background-color: #FFFFFF; padding: 16px; box-sizing: border-box;">
                        <a-descriptions-item label="地点">校内8#影视中心</a-descriptions-item>
                        <a-descriptions-item label="需求">demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo</a-descriptions-item>
                        <a-descriptions-item label="开始时间">2023/05/14 14:45:00</a-descriptions-item>
                        <a-descriptions-item label="结束时间">2023/05/14 14:45:00</a-descriptions-item>
                        <a-descriptions-item label="申请时间">2023/05/14 14:45:00</a-descriptions-item>
                        <a-descriptions-item label="面向人员类型">面向全体和指派</a-descriptions-item>
                        <a-descriptions-item label="审批人学籍号">22100001</a-descriptions-item>
                        <a-descriptions-item label="审批人姓名">Demo</a-descriptions-item>
                        <a-descriptions-item label="驳回时间">2023/05/14 14:45:00</a-descriptions-item>
                        <a-descriptions-item label="操作">
                            <a-row>
                                <a-col v-if="true">
                                    <a-button type="primary" @click="enroll(item.id)">再次报名</a-button>
                                </a-col>
                            </a-row>
                        </a-descriptions-item>

                    </a-descriptions>
                    <a-pagination align="center" style="margin-top: 8px;" v-model:current="current" simple pageSize="5"
                                  :total="data.length" v-if="data.length !== 0"/>
                </a-space>
            </a-tab-pane>
            <a-tab-pane key="5" tab="已失效活动">
                <a-descriptions-item v-if="data.length === 0">
                    <div style="height: 100%; display: flex; flex-direction: column; align-items: center; justify-content: center;">
                        <a-empty :image="Empty.PRESENTED_IMAGE_SIMPLE" style="width: 100%;  "/>
                    </div>
                </a-descriptions-item>
                <a-space direction="vertical" :size="5" style="height: 100%">

                    <a-descriptions v-for="item in data" title="活动公告"
                                    style="background-color: #FFFFFF; padding: 16px; box-sizing: border-box;">
                        <a-descriptions-item label="地点">校内8#影视中心</a-descriptions-item>
                        <a-descriptions-item label="需求">demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo</a-descriptions-item>
                        <a-descriptions-item label="开始时间">2023/05/14 14:45:00</a-descriptions-item>
                        <a-descriptions-item label="结束时间">2023/05/14 14:45:00</a-descriptions-item>
                        <a-descriptions-item label="面向人员类型">面向全体和指派</a-descriptions-item>
                    </a-descriptions>
                    <a-pagination align="center" style="margin-top: 8px;" v-model:current="current" simple pageSize="5"
                                  :total="data.length" v-if="data.length !== 0"/>
                </a-space>
            </a-tab-pane>
        </a-tabs>

        <a-modal
            v-model:visible="visible"
            title="报名确认"
            ok-text="确认"
            cancel-text="取消"
            @ok="hideModal"
        >
        </a-modal>
    </a-layout-content>
</template>

<style scoped>

</style>