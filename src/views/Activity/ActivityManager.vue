<template>
    <a-layout-content
            :style="{margin: '16px'}"
    >
        <h2>活动管理</h2>
        <div style="padding: 8px; background-color: #FFFFFF" v-if="isShow" >
            <a-row justify="end">
                <router-link to="/activity/add">
                    <a-button type="primary" style="margin: 8px; " ghost>添加活动</a-button>
                </router-link>
            </a-row>
            <a-table :columns="columns" :data-source="dataSource" bordered>
                <template
                        #customFilterDropdown="{ setSelectedKeys, selectedKeys, confirm, clearFilters, column }"
                >
                    <div style="padding: 8px">
                        <a-input
                                ref="searchInput"
                                :placeholder="`Search ${column.dataIndex}`"
                                :value="selectedKeys[0]"
                                style="width: 188px; margin-bottom: 8px; display: block"
                                @change="e => setSelectedKeys(e.target.value ? [e.target.value] : [])"
                                @pressEnter="handleSearch(selectedKeys, confirm, column.dataIndex)"
                        />
                        <a-button
                                type="primary"
                                size="small"
                                style="width: 90px; margin-right: 8px"
                                @click="handleSearch(selectedKeys, confirm, column.dataIndex)"
                        >
                            <template #icon><search-outlined /></template>
                            Search
                        </a-button>
                        <a-button size="small" style="width: 90px" @click="handleReset(clearFilters)">
                            Reset
                        </a-button>
                    </div>
                </template>
                <template #bodyCell="{ column, text, record }">
                    <template v-if="['title', 'type', 'place', 'start_datetime', 'end_datetime'].includes(column.dataIndex)">
                        <div>
                            <a-input
                                    v-if="editableData[record.key]"
                                    v-model:value="editableData[record.key][column.dataIndex]"
                                    style="margin: -5px 0"
                            />
                            <template v-else>
                                {{ text }}
                            </template>
                        </div>
                    </template>
                    <template v-else-if="column.dataIndex === 'operation'">
                        <div class="editable-row-operations">
                      <span>
                          <a :disabled="editableData[record.key]" @click="showInfo(record.id)">编辑</a>
                      </span>
                      <span>
                          <a :disabled="editableData[record.key]" @click="showPeople(record.id)">活动人员</a>
                      </span>
                      <span>
                          <a :disabled="editableData[record.key]" @click="showModal(record.id)">签到管理</a>
                      </span>
                      <span>
                        <a-popconfirm title="Sure to delete?" @confirm="deleteActivity(record.id)"><a
                                style="color: red">删除</a></a-popconfirm>
                      </span>
                        </div>
                    </template>
                </template>
            </a-table>
        </div>
        <div style="padding: 8px; background-color: #FFFFFF" v-if="isShow === false" >
            管理员相关功能不支持宽度小于525px的设备显示，建议使用电脑端操作。
        </div>
        <a-modal v-model:visible="visible" title="签到管理">
            <template #footer>
                <a-button type="primary" @click="handleCancel">关闭</a-button>
            </template>
            <a-collapse v-model:activeKey="activeKey" accordion>
                <a-collapse-panel key="1" header="demo demo">
                    <a-card>
                        <p>签到开始时间: 2023-06-02 21:42</p>
                        <p>签到结束时间: 2023-06-02 21:50</p>
                        <div>
                            <a-button type="primary" style="padding-top: 5px; box-sizing: border-box;">变更结束时间</a-button>
                            <a-button type="primary" style="padding-top: 5px; box-sizing: border-box; margin-left: 4px;" danger>删除签到</a-button>
                        </div>
                    </a-card>
                    <a-card>
                        <a-descriptions v-for="item in data" title="姓名"
                                         layout="vertical">

                                <a-descriptions-item label="签到时间">2023-06-03 21:09</a-descriptions-item>
                                <a-descriptions-item label="签到状态">demo</a-descriptions-item>
                                <a-descriptions-item label="操作" style="display:flex; gap: 4px;">
                                            <a-button type="primary" style="padding-top: 5px; box-sizing: border-box;">驳回</a-button>
                                            <a-button type="primary" style="padding-top: 5px; box-sizing: border-box; margin-left: 5px;" @click="showPhotos(item.id)">查看照片</a-button>
                                </a-descriptions-item>
                        </a-descriptions>
                    </a-card>
                </a-collapse-panel>
            </a-collapse>
        </a-modal>
        <a-modal v-model:visible="visibleInfo" title="变更活动信息">

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
                <a-form-item :name="['activity', 'place']" label="地点" :rules="[{ required: true }]">
                    <a-input v-model:value="formState.activity.place"/>
                </a-form-item>
                <a-form-item
                    :name="['activity', 'type']"
                    label="发布类型"
                    has-feedback
                    :rules="[{ required: true, message: '请选择发布方式' }]"
                >
                    <a-select placeholder="选择一个发布类型" value="self-enrollment" disabled>
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
                             v-model:value="formState.activity.start_datetime" name="date-time-picker" label="开始时间">
                    <a-date-picker
                        v-model:value="formState['date-time-picker']"
                        show-time
                        format="YYYY-MM-DD HH:mm:ss"
                        value-format="YYYY-MM-DD HH:mm:ss"
                        placeholder="不得早于当前时间"
                    />
                </a-form-item>
                <a-form-item has-feedback
                             :rules="[{ required: true, message: '请选择日期' }]"
                             v-model:value="formState.activity.end_datetime" name="date-time-picker" label="结束时间">
                    <a-date-picker
                        v-model:value="formState['date-time-picker']"
                        show-time
                        format="YYYY-MM-DD HH:mm:ss"
                        value-format="YYYY-MM-DD HH:mm:ss"
                        placeholder="不得早于当前时间"
                    />
                </a-form-item>

                <a-form-item :wrapper-col="{ span: 12, offset: 6 }">
                    <template #footer>
                        <a-button type="primary" @click="handleCancel">关闭</a-button>
                        <a-button type="primary" @click="changeNote" html-type="submit" danger>变更</a-button>
                    </template>
                </a-form-item>
            </a-form>
        </a-modal>
        <a-modal v-model:visible="visiblePeople" title="活动人员">
            <template #footer>
                <a-button type="primary" @click="handleCancel">关闭</a-button>
            </template>
            <a-card>
                <div>
                    <a-button type="primary" style="padding-top: 5px; box-sizing: border-box;" v-if="true">新增人员</a-button>
                    <a-button type="primary" style="padding-top: 5px; box-sizing: border-box; margin-left: 4px;" v-if="true" danger>关闭报名</a-button>
                </div>
            </a-card>
            <a-descriptions v-for="item in data" title="学籍号 系部 姓名"
                            layout="vertical" style="margin-top: 4px;">

                <a-descriptions-item label="报名（指派）时间">2023-06-03 21:09</a-descriptions-item>
                <a-descriptions-item label="操作" style="display:flex; gap: 4px;">
                    <a-button type="primary" style="padding-top: 5px; box-sizing: border-box;" danger>移除并通知</a-button>
                </a-descriptions-item>
            </a-descriptions>
        </a-modal>
        <a-modal  v-model:visible="visiblePhotos">
            <a-image-preview-group>
                <a-image :width="200" src="https://aliyuncdn.antdv.com/vue.png" />
                <a-image :width="200" src="https://aliyuncdn.antdv.com/logo.png" />
            </a-image-preview-group>
            <template #footer>
                <a-button type="primary" @click="hidePhotos">关闭</a-button>
            </template>
        </a-modal>

    </a-layout-content>

</template>
<script setup>
import {reactive, ref, onMounted, h} from 'vue';
import {Modal} from "ant-design-vue";
import {cloneDeep} from 'lodash-es';
import {PlusOutlined, SearchOutlined} from '@ant-design/icons-vue';

const isShow = ref(true);
function handleResize (event) {
    // 页面宽度小于525px时，不显示表格
    if (document.documentElement.clientWidth < 525) {
        isShow.value = false;
    } else {
        isShow.value = true;
    }
}

onMounted(() => {
    handleResize();
});

window.addEventListener('resize', handleResize);

const activeKey = ref([]);
const text = `A dog is a type of domesticated animal.Known for its loyalty and faithfulness,it can be found as a welcome guest in many households across the world.`;

const data = [];
for (let i = 0; i < 5; i++) {
    data.push({
        id: i,
        title: `demo demo`,
        notes: "三名摄影，一名摄像，多准备CF卡",
        type: "自主报名",
        place: "8#影视中心",
        start_datetime: "2023-05-31 11:11:30",
        end_datetime: "2023-05-31 11:11:30",
    });
}

const dataSource = ref(data);
const editableData = reactive({});
const state = reactive({
    searchText: '',
    searchedColumn: '',
});

const searchInput = ref();
const edit = key => {
    editableData[key] = cloneDeep(dataSource.value.filter(item => key === item.key)[0]);
};

const columns = [
    {
        title: '活动标题',
        dataIndex: 'title',
        width: '30%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.uid.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '类型',
        dataIndex: 'type',
        width: '10%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.name.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '地点',
        dataIndex: 'place',
        width: '15%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.role.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '开始时间',
        dataIndex: 'start_datetime',
        width: '12%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.role.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '结束时间',
        dataIndex: 'end_datetime',
        width: '12%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.role.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '操作',
        dataIndex: 'operation',
    }
];

const handleSearch = (selectedKeys, confirm, dataIndex) => {
    confirm();
    state.searchText = selectedKeys[0];
    state.searchedColumn = dataIndex;
};

const handleReset = clearFilters => {
    clearFilters({ confirm: true });
    state.searchText = '';
};
const deleteActivity = id => {
    alert(id);
};
const save = key => {
    Object.assign(dataSource.value.filter(item => key === item.key)[0], editableData[key]);
    delete editableData[key];
};
const cancel = key => {
    delete editableData[key];
};

const visibleInfo = ref(false);
const showInfo = id => {
    visibleInfo.value = true;
};

const loading = ref(false);
const visible = ref(false);
const showModal = id => {
    visible.value = true;
};
const handleCancel = () => {
    visible.value = false;
    visibleInfo.value = false;
    visiblePhotos.value = false;
};

const changeNote = () => {

}

const visiblePhotos = ref(false)
const showPhotos = id => {
    visiblePhotos.value = true;
};
const visiblePeople = ref(false);

const showPeople = id => {
    visiblePeople.value = true;
}
const hidePhotos = () => {
    visiblePhotos.value = false;
}

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
        place: '',
        start_datetime: '',
        end_datetime: '',
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
<style scoped>
.editable-row-operations a {
    margin-right: 8px;
}
</style>