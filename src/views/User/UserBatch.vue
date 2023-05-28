<script setup>
import {ref} from "vue";
import {InboxOutlined} from "@ant-design/icons-vue";

const fileList = ref([]);
const uploading = ref(false);
const handleRemove = file => {
    const index = fileList.value.indexOf(file);
    const newFileList = fileList.value.slice();
    newFileList.splice(index, 1);
    fileList.value = newFileList;
};
const beforeUpload = file => {
    fileList.value = [...fileList.value, file];
    return false;
};
const handleUpload = () => {
    const formData = new FormData();
    fileList.value.forEach(file => {
        formData.append('csv_file', file);
    });
    uploading.value = true;

    // You can use any AJAX library you like
};

const formItemLayout = {
    labelCol: {
        span: 6,
    },
    wrapperCol: {
        span: 14,
    },
};

const onFinish = values => {
    console.log('Success:', values);
};


const onFinishFailed = errorInfo => {
    console.log('Failed:', errorInfo);
};

</script>

<template>
    <a-layout-content
            :style="{margin: '16px'}"
    >
        <h2>
            用户批量添加
        </h2>
        <a-row>
            <a-col :span="24" style="padding: 24px; background-color: #FFFFFF">
                <a-form
                        name="validate_other"
                        v-bind="formItemLayout"
                        @finishFailed="onFinishFailed"
                        @finish="onFinish"
                        style="max-width: 500px;"
                        @submit="handleUpload"

                >
                    <a-form-item label="Dragger">
                        <a-form-item name="csv-file" no-style>
                            <a-upload-dragger accept=".csv" v-model:fileList="fileList" name="csv_file" :multiple="false" before-upload="false" max-count="2" @remove="handleRemove">
                                <p class="ant-upload-drag-icon">
                                    <InboxOutlined />
                                </p>
                                <p class="ant-upload-text">单击上传或将文件拖动到此区域</p>
                                <p class="ant-upload-hint">仅CSV文件</p>
                            </a-upload-dragger>
                        </a-form-item>
                    </a-form-item>
                    <a-form-item :wrapper-col="{ span: 12, offset: 6 }">
                        <a-button type="primary" html-type="submit" :disabled="fileList.length === 0">Submit</a-button>
                        <a-button type="primary" ghost style="margin-left: 8px;">下载模板</a-button>
                    </a-form-item>
                </a-form>
            </a-col>
        </a-row>

    </a-layout-content>
</template>

<style scoped>

</style>