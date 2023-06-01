<template>
    <a-layout-content
            :style="{margin: '16px'}"
    >
        <h2>签到管理</h2>
        <div style="padding: 8px; background-color: #FFFFFF" v-if="isShow" >
            <a-row justify="end">
                <router-link to="/activity/add">
                    <a-button type="primary" style="margin: 8px; " ghost>新建签到</a-button>
                </router-link>
            </a-row>
            <a-table bordered :data-source="dataSource" :columns="columns">
                <template #bodyCell="{ column, text, record }">
                    <template v-if="column.dataIndex === 'name'">
                        <div class="editable-cell">
                            <div v-if="editableData[record.key]" class="editable-cell-input-wrapper">
                                <a-input v-model:value="editableData[record.key].name" @pressEnter="save(record.key)"/>
                                <check-outlined class="editable-cell-icon-check" @click="save(record.key)"/>
                            </div>
                            <div v-else class="editable-cell-text-wrapper">
                                {{ text || ' ' }}
                                <edit-outlined class="editable-cell-icon" @click="edit(record.key)"/>
                            </div>
                        </div>
                    </template>
                    <template v-else-if="column.dataIndex === 'operation'">
                        <div class="editable-row-operations">
                            <span>
                              <a>编辑</a>
                            </span>
                            <span>
                              <a>详情</a>
                            </span>
                            <span>
                              <a-popconfirm
                                      v-if="dataSource.length"
                                      title="是否删除?"
                                      @confirm="onDelete(record.key)"
                              >
                                <a style="color:red;">删除</a>
                              </a-popconfirm>
                        </span>
                        </div>
                    </template>
                </template>
            </a-table>        </div>
        <div style="padding: 8px; background-color: #FFFFFF" v-if="isShow === false" >
            管理员相关功能不支持宽度小于525px的设备显示，建议使用电脑端操作。
        </div>

    </a-layout-content>

</template>
<script setup>
import {reactive, ref, onMounted} from 'vue';
import {cloneDeep} from 'lodash-es';
import {CheckOutlined, EditOutlined, SearchOutlined} from '@ant-design/icons-vue';

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
        name: "demo",
        activity_title: `demo demo`,
        start_datetime: "2023-05-31 11:11:30",
        end_datetime: "2023-05-31 11:11:30",
        status: "正在进行"
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
        dataIndex: 'activity_title',
        width: '25%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.uid.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '签到名称',
        dataIndex: 'name',
        width: '25%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.name.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '状态',
        dataIndex: 'status',
        width: '8%',
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

</script>
<style scoped>
.editable-row-operations a {
    margin-right: 8px;
}
</style>