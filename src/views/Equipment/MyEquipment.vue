<script setup>
import {ref} from "vue";
import {Empty} from "ant-design-vue";

const data = [];

for (let i = 1; i < 2; i++) {
    data.push({
        id: i,
        type: '电子设备',
        name: '单反相机',
        model: 'Canon EOS 80D',
        apply_date: '2023-05-19 14:43:00'
    })
    // data.push({
    //     id: i,
    //     type: '电子设备',
    //     name: '单反相机',
    //     model: 'Canon EOS 77D',
    //     apply_date: '2023-05-19 14:43:00'
    // })
    // data.push({
    //     id: i,
    //     type: '电子设备',
    //     name: '单反相机',
    //     model: 'Canon EOS Mark 5D IV',
    //     apply_date: '2023-05-19 14:43:00'
    // })
    // data.push({
    //     id: i,
    //     type: '电子设备',
    //     name: '单反相机',
    //     model: 'Canon EOS 400D',
    //     apply_date: '2023-05-19 14:43:00'
    // })
}

const current = ref(1);


function deleteEquipment(id) {
    alert(id);
}

function lostEquipment(id) {

}

function brokeEquipment(id) {

}
</script>

<template>
    <a-layout-content :style="{margin: '16px', height: '100%'}">
        <h2>
            我的设备
        </h2>
        <a-descriptions-item v-if="data.length === 0">
            <div style="height: 100%; display: flex; flex-direction: column; align-items: center; justify-content: center;">
                <a-empty :image="Empty.PRESENTED_IMAGE_SIMPLE" style="width: 100%;  "/>
            </div>
        </a-descriptions-item>
        <a-space direction="vertical" :size="5" style="height: 100%">

            <a-descriptions v-for="item in data" title="借条"
                            style="background-color: #FFFFFF; padding: 16px; box-sizing: border-box;">
                <a-descriptions-item label="设备ID">{{ item.id }}</a-descriptions-item>
                <a-descriptions-item label="设备名称">{{ item.name }}</a-descriptions-item>
                <a-descriptions-item label="设备型号">{{ item.model }}</a-descriptions-item>
                <a-descriptions-item label="申请时间">{{ item.apply_date }}</a-descriptions-item>
                <a-descriptions-item label="操作">
                        <a-row style="gap: 5px;">
                                <a-col>
                                    <a-button type="primary" style="padding-top: 5px; box-sizing: border-box;">归还申报</a-button>
                                </a-col>
                                <a-col>
                                    <a-button type="primary" style="padding-top: 5px; box-sizing: border-box;">延期申报</a-button>
                                </a-col>
                                <a-col>
                                    <a-button danger style="padding-top: 5px; box-sizing: border-box;">归还申报</a-button>
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