<script setup>
import {reactive, ref} from "vue";
import {Empty} from "ant-design-vue";
import {UploadOutlined} from "@ant-design/icons-vue";

const data = [];

for (let i = 1; i < 2; i++) {
    data.push({
        fixed_assets_num: "TY20220800001",
        name: '惠普Z2工作站',
        model: 'Tower G5 ( Intel i9 10900K, Nvidia Quadro P2200, 32G, 1T SSD )',
        created_at: '2023-05-19 14:43:00',
        apply_time: '2023-05-19 14:43:00',
        status: "出借"
    });
    data.push({
        fixed_assets_num: "TY20220800002",
        name: '惠普Z2工作站',
        model: 'Tower G5 ( Intel i9 10900K, Nvidia Quadro P2200, 32G, 1T SSD )',
        created_at: '2023-05-19 14:43:00',
        apply_time: '2023-05-19 14:43:00',
        status: "已拒绝"
    });
}

const current = ref(1);


function deleteEquipment(id) {
    alert(id);
}

function lostEquipment(id) {

}

function brokeEquipment(id) {

}

const formState = reactive({
    activity: {
        activity_title: '',
        name: '',
        start_datetime: '',
        end_datetime: '',
    },
});
const formItemLayout = {
    labelCol: {
        span: 6,
    },
    wrapperCol: {
        span: 14,
    },
};
const validateMessages = {
    required: '${label} 必填!',
    types: {
        email: '${label} 非法邮箱格式',
    },
};
const onFinish = values => {
    console.log('Success:', values);
};
const onFinishFailed = errorInfo => {
    console.log('Failed:', errorInfo);
};

const visibleBack = ref(false);

const showBack = (id) => {
    visibleBack.value = true;
}

const visibleDelay = ref(false);

const showDelay = (id) => {
    visibleDelay.value = true;
}

const visibleReport = ref(false);

const showReport = (id) => {
    visibleReport.value = true;
}

const handleCancel = () => {
    visibleBack.value = false;
    visibleDelay.value = false;
    visibleReport.value = false;
};


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
                <a-descriptions-item label="固定资产编号">{{ item.fixed_assets_num }}</a-descriptions-item>
                <a-descriptions-item label="设备名称">{{ item.name }}</a-descriptions-item>
                <a-descriptions-item label="设备型号">{{ item.model }}</a-descriptions-item>
                <a-descriptions-item label="申请时间">{{ item.created_at }}</a-descriptions-item>
                <a-descriptions-item label="承诺归还时间">{{ item.apply_time }}</a-descriptions-item>
                <a-descriptions-item label="状态">{{ item.status }}</a-descriptions-item>
                <a-descriptions-item label="操作" v-if="item.status === '出借'">
                        <a-row style="gap: 5px;">
                                <a-col>
                                    <a-button type="primary" style="padding-top: 5px; box-sizing: border-box;" @click="showBack(1)">归还申报</a-button>
                                </a-col>
                                <a-col>
                                    <a-button type="primary" style="padding-top: 5px; box-sizing: border-box;">延期申报</a-button>
                                </a-col>
                                <a-col>
                                    <a-button danger style="padding-top: 5px; box-sizing: border-box;">异常申报</a-button>
                                </a-col>
                        </a-row>
                </a-descriptions-item>

            </a-descriptions>
            <a-pagination align="center" style="margin-top: 8px;" v-model:current="current" simple pageSize="5"
                          :total="data.length" v-if="data.length !== 0"/>
        </a-space>
        <a-modal v-model:visible="visibleBack" title="归还实物照片登记">
            <a-form
                    :model="formState"
                    name="validate_other"
                    v-bind="formItemLayout"
                    @finishFailed="onFinishFailed"
                    @finish="onFinish"
            >

                <a-form-item name="upload" label="Upload" extra="至少上传一张图片，最多两张">
                    <a-upload
                            v-model:fileList="formState.upload"
                            name="logo"
                            action="/upload.do"
                            list-type="picture"
                            before-upload="false" max-count="2"
                    >
                        <a-button>
                            <template #icon><UploadOutlined /></template>
                            Click to upload
                        </a-button>
                    </a-upload>
                </a-form-item>

                <a-form-item :wrapper-col="{ span: 12, offset: 6 }">
                    <a-button type="primary" html-type="submit">上传并归还</a-button>
                </a-form-item>
            </a-form>
            <template #footer>
                <a-button type="primary" danger @click="handleCancel">取消</a-button>
            </template>
        </a-modal>
    </a-layout-content>
</template>

<style scoped>

</style>