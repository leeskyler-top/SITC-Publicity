<script setup>
import {reactive, ref, watch} from "vue";
import {MinusCircleOutlined, PlusOutlined} from "@ant-design/icons-vue";
import {debounce} from "lodash-es";


const formItemLayout = {
    labelCol: {
        span: 6,
    },
    wrapperCol: {
        span: 14,
    },
};

const visibleUsers = ref(false);

const editUser = () => {
    visibleUsers.value = true;
};

const formState = reactive({
    activity: {
        title: '',
        notes: '',
        type: 'self-enrollment',
        place: '',
        start_datetime: '',
        end_datetime: '',
        checkin_name: '',
        checkin_start_datetime: '',
        checkin_end_datetime: '',
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

const state = reactive({
    searchText: '',
    searchedColumn: '',
    data: [],
    value: [],
    fetching: false,
});

let lastFetchId = 0;

const fetchUser = debounce(value => {
    console.log('fetching user', value);
    lastFetchId += 1;
    const fetchId = lastFetchId;
    state.data = [];
    state.fetching = true;
    fetch('https://randomuser.me/api/?results=5').then(response => response.json()).then(body => {
        if (fetchId !== lastFetchId) {
            // for fetch callback order
            return;
        }
        const data = body.results.map(user => ({
            label: `${user.name.first} ${user.name.last}`,
            value: user.login.username,
        }));
        state.data = data;
        state.fetching = false;
    });
}, 300);
watch(state.value, () => {
    state.data = [];
    state.fetching = false;
});

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
                        :model="formState.activity"
                        name="validate_other"
                        v-bind="formItemLayout"
                        :validate-messages="validateMessages"
                        @finishFailed="onFinishFailed"
                        @finish="onFinish"
                        style="max-width: 500px;"

                >
                    <a-form-item name="title" label="活动标题" :rules="[{ required: true }]">
                        <a-input v-model:value="formState.activity.title"/>
                    </a-form-item>
                    <a-form-item name="notes" label="需求" :rules="[{ required: true }]">
                        <a-textarea v-model:value="formState.activity.notes"/>
                    </a-form-item>
                    <a-form-item name="place" label="地点" :rules="[{ required: true }]">
                        <a-input v-model:value="formState.activity.place"/>
                    </a-form-item>
                    <a-form-item
                            name="type"
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
                                 :rules="[{ required: true, message: '请选择日期' }]" name="start_datetime" label="开始时间">
                        <a-date-picker
                                v-model:value="formState.activity.start_datetime"
                                show-time
                                format="YYYY-MM-DD HH:mm:ss"
                                value-format="YYYY-MM-DD HH:mm:ss"
                                placeholder="不得早于当前时间"
                        />
                    </a-form-item>
                    <a-form-item has-feedback
                                 :rules="[{ required: true, message: '请选择日期' }]" name="end_datetime" label="结束时间">
                        <a-date-picker
                            v-model:value="formState.activity.end_datetime"
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
                        <a-form-item name="name" label="签到名称" :rules="[{ required: true }]">
                            <a-input v-model:value="formState.activity.checkin_name"/>
                        </a-form-item>
                        <a-form-item has-feedback
                                     :rules="[{ required: true, message: '请选择日期' }]"
                                     label="签到开始时间">
                            <a-date-picker
                                    v-model:value="formState.activity.checkIn.checkin_start_datetime"
                                    show-time
                                    format="YYYY-MM-DD HH:mm:ss"
                                    value-format="YYYY-MM-DD HH:mm:ss"
                                    placeholder="不得早于当前时间"
                            />
                        </a-form-item>
                        <a-form-item has-feedback
                                     :rules="[{ required: true, message: '请选择日期' }]"
                                     label="签到结束时间">
                            <a-date-picker
                                v-model:value="formState.activity.checkin_end_datetime"
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
        <a-modal v-model:visible="visibleUsers" title="添加用户">
            <a-select
                v-model:value="state.value"
                mode="multiple"
                label-in-value
                placeholder="Select users"
                style="width: 100%"
                :filter-option="false"
                :not-found-content="state.fetching ? undefined : null"
                :options="state.data"
                @search="fetchUser"
            >
                <template v-if="state.fetching" #notFoundContent>
                    <a-spin size="small" />
                </template>
            </a-select>
        </a-modal>
    </a-layout-content>
</template>

<style scoped>

</style>