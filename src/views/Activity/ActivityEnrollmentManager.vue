<script setup>
import {reactive, ref, onMounted, createVNode} from 'vue';
import {cloneDeep} from 'lodash-es';
import {ExclamationCircleOutlined, SearchOutlined} from '@ant-design/icons-vue';
import {Modal} from "ant-design-vue";

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

const visible = ref(false);
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

const showModal = id => {
    visible.value = true;
};
const handleCancel = () => {
    visible.value = false;
};

const countDown = () => {
    let secondsToGo = 5;
    const modal = Modal.success({
        title: '操作成功',
        content: `提示框将在 ${secondsToGo}s 后关闭`,
    });
    const interval = setInterval(() => {
        secondsToGo -= 1;
        modal.update({
            content: `提示框将在 ${secondsToGo}s 后关闭`,
        });
    }, 1000);
    setTimeout(() => {
        clearInterval(interval);
        modal.destroy();
    }, secondsToGo * 1000);
};

</script>
<template>
    <a-layout-content
            :style="{margin: '16px'}"
    >
        <h2>审核列表</h2>
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
                          <a @click="showModal(record.id)">详情</a>
                        </span>
                        </div>
                    </template>
                </template>
            </a-table>
        </div>
        <div style="padding: 8px; background-color: #FFFFFF" v-if="isShow === false" >
            管理员相关功能不支持宽度小于525px的设备显示，建议使用电脑端操作。
        </div>
        <a-modal v-model:visible="visible" title="报名管理">
            <template #footer>
                <a-button type="primary" @click="handleCancel">关闭</a-button>
            </template>
            <a-card>
                <p>活动开始时间: 2023-06-02 21:42</p>
                <div>
                    <a-button type="primary" style="padding-top: 5px; box-sizing: border-box;" danger v-if="true" >关闭报名</a-button>
                    <a-button type="primary" style="padding-top: 5px; box-sizing: border-box;" v-if="false">打开报名</a-button>
                </div>
            </a-card>
            <a-descriptions v-for="item in data" title="学籍号 系部 姓名"
                            layout="vertical" style="margin-top: 4px;">

                <a-descriptions-item label="报名时间">2023-06-03 21:09</a-descriptions-item>
                <a-descriptions-item label="报名状态">待审核</a-descriptions-item>
                <a-descriptions-item label="操作" style="display:flex; gap: 4px;">
                    <a-button type="primary" style="padding-top: 5px; box-sizing: border-box;" @click="showConfirm('refuse')" danger>驳回</a-button>
                    <a-button type="primary" style="padding-top: 5px; box-sizing: border-box; margin-left: 5px;" @click="showConfirm('agree')">同意</a-button>
                </a-descriptions-item>
            </a-descriptions>
        </a-modal>
    </a-layout-content>

</template>

<style scoped>
.editable-row-operations a {
    margin-right: 8px;
}
</style>