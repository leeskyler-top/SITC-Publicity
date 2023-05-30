<script setup>
import {reactive, ref} from "vue";
import {MinusCircleOutlined, PlusOutlined} from "@ant-design/icons-vue";


const formItemLayout = {
    labelCol: {
        span: 6,
    },
    wrapperCol: {
        span: 14,
    },
};

const editUser = () => {

};

const formState = reactive({
    activity: {
        title: '',
        notes: '',
        type: 'self-enrollment',
        place: '',
        datetime: '',
    },
    checkIn: {
        name: "",
        datetime: ""
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

const checkInSwitcher = ref(false);
</script>

<template>
    <a-layout-content
            :style="{margin: '16px'}"
    >
        <h2>
            活动添加
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
                    <a-form-item :name="['activity', 'title']" label="活动标题" :rules="[{ required: true }]">
                        <a-input v-model:value="formState.activity.title"/>
                    </a-form-item>
                    <a-form-item :name="['activity', 'notes']" label="需求" :rules="[{ required: true }]">
                        <a-textarea v-model:value="formState.activity.notes"/>
                    </a-form-item>
                    <a-form-item
                            :name="['activity', 'type']"
                            label="发布类型"
                            has-feedback
                            :rules="[{ required: true, message: '请选择发布方式' }]"
                    >
                        <a-select v-model:value="formState.activity.type" placeholder="选择一个发布类型">
                            <a-select-option value="assignment">指派</a-select-option>
                            <a-select-option value="self-enrollment">自主报名</a-select-option>
                            <a-select-option value="ase">自主报名与指派</a-select-option>
                        </a-select>
                    </a-form-item>
                    <div v-if="formState.activity.type === 'assignment' || formState.activity.type === 'ase'">
                        <a-form-item style="display: flex; align-items: center; justify-content: center;">
                            <a-button type="dashed" block @click="editUser">
                                <PlusOutlined/>
                                Add user
                            </a-button>
                        </a-form-item>
                    </div>
                    <a-form-item has-feedback
                                 :rules="[{ required: true, message: '请选择日期' }]"
                                 v-model:value="formState.activity.datetime" name="date-time-picker" label="时间">
                        <a-date-picker
                                v-model:value="formState['date-time-picker']"
                                show-time
                                format="YYYY-MM-DD HH:mm:ss"
                                value-format="YYYY-MM-DD HH:mm:ss"
                                placeholder="不得早于当前时间"
                        />
                    </a-form-item>
                    <a-form-item name="switch" label="生成签到">
                        <a-switch v-model:checked="checkInSwitcher"/>
                    </a-form-item>
                    <div v-if="checkInSwitcher">
                        <a-form-item :name="['checkIn', 'name']" label="签到名称" :rules="[{ required: true }]">
                            <a-input v-model:value="formState.activity.title"/>
                        </a-form-item>
                        <a-form-item has-feedback
                                     :rules="[{ required: true, message: '请选择日期' }]"
                                     v-model:value="formState.checkIn.datetime" name="date-time-picker"
                                     label="签到时间">
                            <a-date-picker
                                    v-model:value="formState['date-time-picker']"
                                    show-time
                                    format="YYYY-MM-DD HH:mm:ss"
                                    value-format="YYYY-MM-DD HH:mm:ss"
                                    placeholder="不得早于当前时间"
                            />
                        </a-form-item>
                    </div>

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