<template>
    <a-layout-content
            :style="{margin: '16px'}"
    >
        <h2>用户管理</h2>
        <div style="padding: 8px; background-color: #FFFFFF" v-if="isShow" >
            <a-row justify="end">
                <router-link to="/user/add">
                    <a-button type="primary" style="margin: 8px; " ghost>添加用户</a-button>
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
                    <template v-if="['uid', 'name', 'className', 'department'].includes(column.dataIndex)">
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
                      <span v-if="editableData[record.key]">
                        <a-popconfirm title="确定保存?" @click="save(record.key)">
                            <a>保存</a>
                        </a-popconfirm>
                        <a-typography-link @click="cancel(record.key)">取消</a-typography-link>
                      </span>
                      <span v-else>
                         <a @click="edit(record.key)">编辑</a>
                      </span>
                      <span>
                          <a :disabled="editableData[record.key]" @click="showModal(record.id)">修改角色</a>
                      </span>
                      <span>
                          <a :disabled="editableData[record.key]" @click="showConfirm(record.id)">重置密码</a>
                      </span>
                      <span>
                        <a-popconfirm title="Sure to delete?" @confirm="deleteUser(record.key)"><a
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
        <a-modal v-model:visible="visible" title="修改用户角色">
          <a-card>
              <p>用户id：<span>1</span></p>
              <p>学籍号：<span>22100000</span></p>
              <p>姓名：<span>Demo</span></p>
              <p>班级：<span>215T01</span></p>
              <p>系部：<span>信息技术系</span></p>
          </a-card>
            <a-form
                    :model="formState"
                    name="validate_other"
                    v-bind="formItemLayout"
                    @finishFailed="onFinishFailed"
                    @finish="onFinish"
            >
                <a-form-item
                        name="select"
                        label="角色"
                        has-feedback
                        :rules="[{ required: true, message: 'Please select your country!' }]"
                        style="padding-top: 8px;"
                >
                    <a-select v-model:value="formState.select" placeholder="选择角色">
                        <a-select-option value="User">普通用户</a-select-option>
                        <a-select-option value="Admin">管理员</a-select-option>
                    </a-select>
                </a-form-item>
            </a-form>
            <template #footer>
                <a-button type="primary" @click="handleCancel">关闭</a-button>
                <a-button type="primary" @click="changeNote" danger>变更</a-button>
            </template>
        </a-modal>
        <a-modal v-model:visible="visiblePassword" title="重置密码">
            <a-card>
                <p>用户id：<span>1</span></p>
                <p>学籍号：<span>22100000</span></p>
                <p>姓名：<span>Demo</span></p>
                <p>班级：<span>215T01</span></p>
                <p>系部：<span>信息技术系</span></p>
            </a-card>
            <a-card>
                <p>密码已重置，密码为: a0PPomsa</p>
            </a-card>
            <template #footer>
                <a-button type="primary" @click="handleCancel">关闭</a-button>
            </template>
        </a-modal>
    </a-layout-content>

</template>
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
for (let i = 0; i < 100; i++) {
    data.push({
        key: i.toString(),
        name: `Edrward ${i}`,
        uid: 22100000 + i,
        role: "User",
        department: "信息技术系",
        className: "215T01",
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
        title: '学籍号',
        dataIndex: 'uid',
        width: '20%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.uid.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '姓名',
        dataIndex: 'name',
        width: '20%',
        customFilterDropdown: true,
        onFilter: (value, record) =>
            record.name.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
        title: '班级',
        dataIndex: 'className',
        width: '10%',
    },
    {
        title: '系部',
        dataIndex: 'department',
        width: '10%',
    },
    {
        title: '角色',
        dataIndex: 'role',
        width: '20%',
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
const deleteUser = key => {
    alert(key);
};
const save = key => {
    Object.assign(dataSource.value.filter(item => key === item.key)[0], editableData[key]);
    delete editableData[key];
};
const cancel = key => {
    delete editableData[key];
};

const visible = ref(false);
const visiblePassword = ref(false);

const showModal = id => {
    visible.value = true;
}

const showPassword = id => {
    visiblePassword.value = true;
}

const showConfirm = (id) => {
    Modal.confirm({
        title: '确认操作',
        icon: createVNode(ExclamationCircleOutlined),
        content: '确定重置密码？',
        okText: '确认',
        cancelText: '取消',
        onOk() {
            showPassword(id)
        }
    });
}
const formItemLayout = {
    labelCol: {
        span: 6,
    },
    wrapperCol: {
        span: 14,
    },
};
const formState = reactive({
    select: "User"
});
const onFinish = values => {
    console.log('Success:', values);
};
const onFinishFailed = errorInfo => {
    console.log('Failed:', errorInfo);
};

const handleCancel = () => {
    visible.value = false;
    visiblePassword.value = false;
};

</script>
<style scoped>
.editable-row-operations a {
    margin-right: 8px;
}
</style>