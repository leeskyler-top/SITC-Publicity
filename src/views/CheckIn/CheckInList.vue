<script setup>
import {ref, reactive} from "vue";
import {Empty} from "ant-design-vue";
import {InboxOutlined, UploadOutlined} from "@ant-design/icons-vue";

const current = ref(1);

const data = [];
data.push(1);

const visible = ref(false);

const checkin = id => {
    console.log(1);
    visible.value = true;
}

const handleCancel = () => {
    visible.value = false;
};

const formItemLayout = {
    labelCol: {
        span: 6,
    },
    wrapperCol: {
        span: 14,
    },
};
const formState = reactive({
    'input-number': 3,
    'checkbox-group': ['A', 'B'],
    rate: 3.5,
});
const onFinish = values => {
    console.log('Success:', values);
};
const onFinishFailed = errorInfo => {
    console.log('Failed:', errorInfo);
};



</script>

<template>
    <a-layout-content :style="{margin: '16px', height: '100%'}">
        <h2>
            正在进行的签到
        </h2>
        <a-descriptions-item v-if="data.length === 0">
            <div style="height: 100%; display: flex; flex-direction: column; align-items: center; justify-content: center;">
                <a-empty :image="Empty.PRESENTED_IMAGE_SIMPLE" style="width: 100%;  "/>
            </div>
        </a-descriptions-item>
        <a-space direction="vertical" :size="5" style="height: 100%">

            <a-descriptions v-for="item in data" title="签到名称"
                            style="background-color: #FFFFFF; padding: 16px; box-sizing: border-box;">
                <a-descriptions-item label="活动标题">demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo demo</a-descriptions-item>
                <a-descriptions-item label="开始时间">2023/05/14 14:45:00</a-descriptions-item>
                <a-descriptions-item label="结束时间">2023/05/14 14:45:00</a-descriptions-item>
                <a-descriptions-item label="操作">
                    <a-row>
                        <a-col>
                            <a-button type="primary" @click="checkin(item.id)">签到</a-button>
                        </a-col>
                    </a-row>
                </a-descriptions-item>

            </a-descriptions>
            <a-pagination align="center" style="margin-top: 8px;" v-model:current="current" simple pageSize="5"
                          :total="data.length" v-if="data.length !== 0"/>
        </a-space>
        <a-modal v-model:visible="visible" title="资料上传">
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
                    <a-button type="primary" html-type="submit">上传并签到</a-button>
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