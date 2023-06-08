<script setup>
import {cloneDeep} from 'lodash-es';
import {reactive, ref, onMounted, computed} from 'vue';

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
for (let i = 0; i < 100; i++) {
    data.push({
        fixed_assets_num: 'TY20220800001',
        name: '惠普Z2工作站',
        model: 'Tower G5 ( Intel i9 10900K, Nvidia Quadro P2200, 32G, 1T SSD )',
        apply_uid: 22100000 + i,
        apply_name: "Demo",
        apply_time: "2023-05-25 21:30:00",
        audit_uid: 22100000 + i,
        audit_name: "Demo",
        audit_time: "2023-05-25 21:30:00",
        status: "已归还",
        return_status: "正常",
    });
}

const dataSource = ref(data);
const editableData = reactive({});
const state = reactive({
    searchText: '',
    searchedColumn: '',
});

const searchInput = ref('');
const columns = [
    {
        title: '固定资产编号',
        dataIndex: 'fixed_assets_num',
        width: '10%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.fixed_assets_num.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '设备名称',
        dataIndex: 'name',
        width: '10%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.name.toString().toLowerCase().includes(value.toLowerCase()),
    },

    {
        title: '设备型号',
        dataIndex: 'model',
        width: '10%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.name.toString().toLowerCase().includes(value.toLowerCase()),
    },

    {
        title: '申请人学籍号',
        dataIndex: 'apply_uid',
        width: '5%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.apply_uid.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '申请人姓名',
        dataIndex: 'apply_name',
        width: '5%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.apply_name.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '审批人学籍号',
        dataIndex: 'audit_uid',
        width: '5%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.audit_uid.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '审批人姓名',
        dataIndex: 'audit_name',
        width: '5%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.audit_name.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '要求时间',
        dataIndex: 'apply_time',
        width: '10%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.apply_time.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '审批时间',
        dataIndex: 'audit_time',
        width: '10%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.audit_time.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '条目状态',
        dataIndex: 'status',
        width: '5%',
        onFilter: (value, record) =>
            record.status.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '归还状态',
        dataIndex: 'return_status',
        width: '5%',
        onFilter: (value, record) =>
            record.return_status.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '操作',
        dataIndex: 'operation',
        width: '8%',
    },
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
const count = computed(() => dataSource.value.length + 1);
const edit = key => {
    editableData[key] = cloneDeep(dataSource.value.filter(item => key === item.key)[0]);
};
const save = key => {
    Object.assign(dataSource.value.filter(item => key === item.key)[0], editableData[key]);
    delete editableData[key];
};
const onDelete = key => {
    dataSource.value = dataSource.value.filter(item => item.key !== key);
};
const handleAdd = () => {
    const newData = {
        key: `${count.value}`,
        name: `Edward King ${count.value}`,
        age: 32,
        address: `London, Park Lane no. ${count.value}`,
    };
    dataSource.value.push(newData);
};

import { SearchOutlined  } from '@ant-design/icons-vue';

const visiblePhotos = ref(false)
const showPhotos = id => {
    visiblePhotos.value = true;
};

const handleCancel = () => {
    visiblePhotos.value = false;
};

</script>
<template>
    <a-layout-content
        :style="{margin: '16px'}"
    >
        <h2>所有设备出借历史</h2>
        <div style="padding: 8px; background-color: #FFFFFF" v-if="isShow" >
            <a-table bordered :data-source="dataSource" :columns="columns">
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
                    <template v-if="column.dataIndex === 'name'">
                        <div class="editable-cell">
                            <div class="editable-cell-text-wrapper">
                                {{ text || ' ' }}
                            </div>
                        </div>
                    </template>
                    <template v-else-if="column.dataIndex === 'operation'">
                        <div class="editable-row-operations">
                             <span>
                               <a @click="showPhotos">查看图片</a>
                            </span>
                        </div>
                    </template>
                </template>
            </a-table>
        </div>
        <div style="padding: 8px; background-color: #FFFFFF" v-if="isShow === false" >
            管理员相关功能不支持宽度小于525px的设备显示，建议使用电脑端操作。
        </div>
        <a-modal  v-model:visible="visiblePhotos" >
            <a-image-preview-group >
                <a-image :width="200" src="https://aliyuncdn.antdv.com/vue.png" />
                <a-image :width="200" src="https://aliyuncdn.antdv.com/logo.png" />
            </a-image-preview-group>
            <template #footer>
                <a-button type="primary" @click="handleCancel">OK</a-button>
            </template>
        </a-modal>
    </a-layout-content>

</template>

<style scoped>
.editable-row-operations a {
    margin-right: 8px;
}
</style>