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
    <a-layout-content
            :style="{margin: '16px'}"
    >
        <h2>
            设备借用
        </h2>
        <a-row>
            <a-col :span="24" style="padding: 24px; background-color: #FFFFFF">
                <a-form
                    :model="formState"
                    name="validate_other"
                    v-bind="formItemLayout"
                    @finishFailed="onFinishFailed"
                    @finish="onFinish"
                    style="max-width: 500px;"

                >
                    <a-form-item
                        name="select"
                        label="选择一个设备"
                        has-feedback
                        :rules="[{ required: true, message: '请选择设备' }]"
                    >
                        <a-select v-model:value="formState.select" placeholder="仅显示可用" >
                            <a-select-option value="77d">Canon EOS 77D</a-select-option>
                            <a-select-option value="80d">Canon EOS 80D</a-select-option>
                            <a-select-option value="400d">Canon EOS 400D</a-select-option>
                            <a-select-option value="9000d">Canon EOS 9000D</a-select-option>
                        </a-select>
                    </a-form-item>

                    <a-form-item has-feedback
                                 :rules="[{ required: true, message: '请选择日期' }]"   name="date-time-picker" label="承诺归还时间" v-bind="config">
                        <a-date-picker
                            v-model:value="formState['date-time-picker']"
                            show-time
                            format="YYYY-MM-DD HH:mm:ss"
                            value-format="YYYY-MM-DD HH:mm:ss"
                            placeholder="不得早于当前时间"
                        />
                    </a-form-item>

                    <a-form-item has-feedback
                                 :rules="[{ required: true, message: '至少上传一张图片' }]" name="upload" label="上传图片" extra="设备状态">
                        <a-upload
                            v-model:fileList="formState.upload"
                            name="pic1"
                            action="/upload.do"
                            list-type="picture"
                        >
                            <a-button>
                                <template #icon>
                                    <UploadOutlined/>
                                </template>
                                单击上传
                            </a-button>
                        </a-upload>
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