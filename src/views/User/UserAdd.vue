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
    user: {
        name: '',
        role: 'User',
        email: '',
        department: '',
        className: '',
        notes: '',
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
            用户添加
        </h2>
        <a-row>
            <a-col :span="24" style="padding: 24px; background-color: #FFFFFF">
                <a-form
                        :model="formState"
                        name="validate_other"
                        v-bind="formItemLayout"
                        :validate-messages="validateMessages"
                        @finishFailed="onFinishFailed"
                        @finish="onFinish"
                        style="max-width: 500px;"

                >
                    <a-form-item
                            :name="['user', 'role']"
                            label="选择一个角色"
                            has-feedback
                            :rules="[{ required: true, message: '请选择角色' }]"
                    >
                        <a-select v-model:value="formState.user.role" placeholder="选择用户角色" >
                            <a-select-option value="User">用户(User)</a-select-option>
                            <a-select-option value="Admin">管理员(Admin)</a-select-option>
                        </a-select>
                    </a-form-item>
                    <a-form-item :name="['user', 'uid']" label="学籍号" :rules="[{ required: true }]">
                        <a-input v-model:value="formState.user.uid" />
                    </a-form-item>
                    <a-form-item :name="['user', 'name']" label="姓名" :rules="[{ required: true }]">
                        <a-input v-model:value="formState.user.name" />
                    </a-form-item>
                    <a-form-item :name="['user', 'email']" label="账户(邮箱)" :rules="[{ required: true, type: 'email' }]">
                        <a-input v-model:value="formState.user.email" />
                    </a-form-item>
                    <a-form-item :name="['user', 'department']" label="系部" :rules="[{ required: true }]">
                        <a-input v-model:value="formState.user.department" />
                    </a-form-item>
                    <a-form-item :name="['user', 'className']" label="班级" :rules="[{ required: true }]">
                        <a-input v-model:value="formState.user.className" />
                    </a-form-item>
                    <a-form-item :name="['user', 'notes']" label="备注" :rules="[{ required: false }]">
                        <a-input v-model:value="formState.user.note" />
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