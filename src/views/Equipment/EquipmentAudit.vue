<script setup>
import {createVNode, ref} from "vue";
import {Modal} from "ant-design-vue";
import {Empty} from "ant-design-vue";
import {ExclamationCircleOutlined} from "@ant-design/icons-vue";

const data = [];

for (let i = 1; i < 2; i++) {
    data.push(    {
        fixed_assets_num: 'TY20220800001',
        name: '惠普Z2工作站',
        model: 'Tower G5 ( Intel i9 10900K, Nvidia Quadro P2200, 32G, 1T SSD )',
        apply_uid: '22200001',
        apply_name: 'Demo Demo',
        apply_time: "2023-05-31 09:00:00"
    },
    {
        fixed_assets_num: 'TY20220800002',
        name: '惠普Z2工作站',
        model: 'Tower G5 ( Intel i9 10900K, Nvidia Quadro P2200, 32G, 1T SSD )',
        apply_uid: '22200001',
        apply_name: 'Demo Demo',
        apply_time: "2023-05-31 09:00:00"
    })
}

const current = ref(1);


function deleteEquipment(id) {
    alert(id);
}

function lostEquipment(id) {

}

function brokeEquipment(id) {

}

const visiblePhotos = ref(false)
const showPhotos = id => {
    visiblePhotos.value = true;
};

const handleCancel = () => {
    visiblePhotos.value = false;
};

const showConfirm = (op) => {
    if (op === "agree") {
        Modal.confirm({
            title: '确认操作',
            icon: createVNode(ExclamationCircleOutlined),
            content: '确定同意？',
            okText: '确认',
            cancelText: '取消',
            onOk() {

            }
        });
    } else if (op === "refuse") {
        Modal.confirm({
            title: '确认操作',
            icon: createVNode(ExclamationCircleOutlined),
            content: '确认驳回？',
            okText: '确认',
            cancelText: '取消',
            onOk() {

            }
        });
    }
}

</script>

<template>
    <a-layout-content :style="{margin: '16px', height: '100%'}">
        <h2>
            设备申请审核
        </h2>
        <a-descriptions-item v-if="data.length === 0">
            <div style="height: 100%; display: flex; flex-direction: column; align-items: center; justify-content: center;">
                <a-empty :image="Empty.PRESENTED_IMAGE_SIMPLE" style="width: 100%;  "/>
            </div>
        </a-descriptions-item>
        <a-space direction="vertical" :size="5" style="height: 100%">

            <a-descriptions v-for="item in data" title="借条"
                            style="background-color: #FFFFFF; padding: 16px; box-sizing: border-box;">
                <a-descriptions-item label="设备ID">{{ item.fixed_assets_num }}</a-descriptions-item>
                <a-descriptions-item label="设备名称">{{ item.name }}</a-descriptions-item>
                <a-descriptions-item label="设备型号">{{ item.model }}</a-descriptions-item>
                <a-descriptions-item label="申请人学籍号">{{ item.apply_uid }}</a-descriptions-item>
                <a-descriptions-item label="申请人姓名">{{ item.apply_name }}</a-descriptions-item>
                <a-descriptions-item label="承诺归还时间">{{ item.apply_time }}</a-descriptions-item>
                <a-descriptions-item label="操作">
                    <a-row style="gap: 5px;">
                        <a-col>
                            <a-button type="primary" style="padding-top: 5px; box-sizing: border-box;" @click="showPhotos(item.id)">查看照片</a-button>
                        </a-col>
                        <a-col>
                            <a-button type="primary" style="padding-top: 5px; box-sizing: border-box;" @click="showConfirm('agree')">同意</a-button>
                        </a-col>
                        <a-col>
                            <a-button danger style="padding-top: 5px; box-sizing: border-box;" @click="showConfirm('refuse')">拒绝</a-button>
                        </a-col>
                    </a-row>
                </a-descriptions-item>

            </a-descriptions>
            <a-pagination align="center" style="margin-top: 8px;" v-model:current="current" simple pageSize="5"
                          :total="data.length" v-if="data.length !== 0"/>
        </a-space>
        <a-modal  v-model:visible="visiblePhotos" >
            <a-image-preview-group >
                <a-image :width="200" src="https://aliyuncdn.antdv.com/vue.png" />
                <a-image :width="200" src="https://aliyuncdn.antdv.com/logo.png" />
            </a-image-preview-group>
            <template #footer>
                <a-button type="primary" @click="handleCancel">OK</a-button>
            </template>
        </a-modal>
    </a-layout-content>
</template>

<style scoped>

</style>