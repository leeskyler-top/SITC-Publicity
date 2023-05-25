<script setup>
import {ref} from "vue";
import {Empty} from "ant-design-vue";

const data = [];

for (let i = 1; i < 2; i++) {
    data.push({
        id: i,
    })
    data.push({
        id: i,
        type: '电子设备',
        name: '单反相机',
        model: 'Canon EOS 77D',
        apply_date: '2023-05-19 14:43:00'
    })
    data.push({
        id: i,
        type: '电子设备',
        name: '单反相机',
        model: 'Canon EOS Mark 5D IV',
        apply_date: '2023-05-19 14:43:00'
    })
    data.push({
        id: i,
        type: '电子设备',
        name: '单反相机',
        model: 'Canon EOS 400D',
        apply_date: '2023-05-19 14:43:00'
    })
}

const current = ref(1);


function enroll(id) {
    alert(id);
}
</script>

<template>
    <a-layout-content :style="{margin: '16px', height: '100%'}">
        <h2>
            活动列表
        </h2>
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
                <a-descriptions-item label="时间">2023/05/14 14:45:00</a-descriptions-item>
                <a-descriptions-item label="面向人员类型">面向全体和指派</a-descriptions-item>
                <a-descriptions-item label="可报名人数">3</a-descriptions-item>
                <a-descriptions-item label="操作">
                    <a-row>
                        <a-col>
                            <a-button type="primary" @click="enroll(item.id)">报名</a-button>
                        </a-col>
                    </a-row>
                </a-descriptions-item>

            </a-descriptions>
            <a-pagination align="center" style="margin-top: 8px;" v-model:current="current" simple pageSize="5"
                          :total="data.length" v-if="data.length !== 0"/>
        </a-space>
    </a-layout-content>
</template>

<style scoped>

</style>