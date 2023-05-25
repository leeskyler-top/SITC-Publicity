<script setup>
import {defineComponent} from "vue";
import {InboxOutlined, UploadOutlined} from "@ant-design/icons-vue";
import {reactive} from "vue";

const formItemLayout = {
    labelCol: {
        span: 6,
    },
    wrapperCol: {
        span: 14,
    },
};
const formState = reactive({
    equipment: {
        fixedAssetsNum: '',
        name: '',
        model: '',
        status: '',
        record_time: '',
    },
});
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
</script>

<template>
    <a-layout-content
            :style="{margin: '16px'}"
    >
        <h2>
            设备资产添加
        </h2>
        <a-row>
            <a-col :span="24" style="padding: 24px; background-color: #FFFFFF">
                <a-form
                        :model="formState"
                        name="validate_other"
                        v-bind="formItemLayout"
                        @finishFailed="onFinishFailed"
                        :validate-messages="validateMessages"
                        @finish="onFinish"
                        style="max-width: 500px;"

                >
                    <a-form-item :name="['equipment', 'fixedAssetsNum']" label="固定资产编号" :rules="[{ required: true }]">
                        <a-input v-model:value="formState.equipment.fixedAssetsNum" />
                    </a-form-item>
                    <a-form-item :name="['equipment', 'name']" label="设备名称" :rules="[{ required: true }]">
                        <a-input v-model:value="formState.equipment.name" />
                    </a-form-item>
                    <a-form-item :name="['equipment', 'model']" label="设备型号" :rules="[{ required: true, type: 'email' }]">
                        <a-input v-model:value="formState.equipment.model" />
                    </a-form-item>
                    <a-form-item :name="['equipment', 'status']" label="设备状态" :rules="[{ required: true }]">
                        <a-input v-model:value="formState.equipment.status" />
                    </a-form-item>
                    <a-form-item has-feedback
                                 :rules="[{ required: true, message: '请选择日期' }]"   name="date-time-picker" label="入库时间">
                        <a-date-picker
                                v-model:value="formState.equipment.record_time"
                                show-time
                                format="YYYY-MM-DD HH:mm:ss"
                                value-format="YYYY-MM-DD HH:mm:ss"
                                placeholder="不得晚于当前时间"
                        />
                    </a-form-item>
                    <a-form-item :wrapper-col="{ span: 12, offset: 6 }">
                        <a-button type="primary" html-type="submit">Submit</a-button>
                    </a-form-item>
                </a-form>
            </a-col>
        </a-row>

    </a-layout-content>
</template>

<style scoped>

</style>