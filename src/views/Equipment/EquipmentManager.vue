<template>
    <a-layout-content
            :style="{margin: '16px'}"
    >
        <h2>设备管理</h2>
        <div style="padding: 8px; background-color: #FFFFFF" v-if="isShow">
            <a-row justify="end">
                <router-link to="/equipment/add">
                    <a-button type="primary" style="margin: 8px; " ghost>添加设备</a-button>
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
                              <a>出借历史</a>
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
            </a-table>
        </div>
        <div style="padding: 8px; background-color: #FFFFFF" v-if="isShow === false">
            管理员相关功能不支持宽度小于525px的设备显示，建议使用电脑端操作。
        </div>

    </a-layout-content>

</template>
<script>
import {computed, defineComponent, reactive, ref} from 'vue';
import {CheckOutlined, EditOutlined} from '@ant-design/icons-vue';
import {cloneDeep} from 'lodash-es';


/*
    屏幕宽度判断
 */
const isShow = ref(true);
function handleResize (event) {
    this.fullWidth = document.documentElement.clientWidth;
    // 页面宽度小于525px时，不显示地图
    if (this.fullWidth < 525) {
        isShow.value = false;
    } else {
        isShow.value = true;
    }
}
export default defineComponent({
    components: {
        CheckOutlined,
        EditOutlined,
    },
    setup() {
        const columns = [
            {
                title: '固定资产编号',
                dataIndex: 'fixed_assets_num',
                width: '20%',
            },
            {
                title: '设备名称',
                dataIndex: 'name',
                width: '20%',
            },
            {
                title: '设备型号',
                dataIndex: 'model',
                width: '20%',
            },
            {
                title: '状态',
                dataIndex: 'status',
                width: '20%',
            },
            {
                title: '操作',
                dataIndex: 'operation',
            }
        ];
        const dataSource = ref([
            {
                fixed_assets_num: 'TY20220800001',
                name: '惠普Z2工作站',
                model: 'Tower G5 ( Intel i9 10900K, Nvidia Quadro P2200, 32G, 1T SSD )',
                status: '空闲',
            },
            {
                fixed_assets_num: 'TY20220800002',
                name: '惠普Z2工作站',
                model: 'Tower G5 ( Intel i9 10900K, Nvidia Quadro P2200, 32G, 1T SSD )',
                status: '空闲',
            },
            {
                fixed_assets_numfixed_assets_num: 'TY20220800003',
                name: '惠普Z2工作站',
                model: 'Tower G5 ( Intel i9 10900K, Nvidia Quadro P2200, 32G, 1T SSD )',
                status: '空闲',
            },
            {
                fixed_assets_num: 'TY20220800004',
                name: '惠普Z2工作站',
                model: 'Tower G5 ( Intel i9 10900K, Nvidia Quadro P2200, 32G, 1T SSD )',
                status: '空闲',
            },
            {
                fixed_assets_num: 'TY20220800005',
                name: '惠普Z2工作站',
                model: 'Tower G5 ( Intel i9 10900K, Nvidia Quadro P2200, 32G, 1T SSD )',
                status: '空闲',
            },
            {
                fixed_assets_num: 'TY20220800006',
                name: '惠普Z2工作站',
                model: 'Tower G5 ( Intel i9 10900K, Nvidia Quadro P2200, 32G, 1T SSD )',
                status: '空闲',
            },

            {
                fixed_assets_num: 'TY20220800007',
                name: '惠普Z2工作站',
                model: 'Tower G5 ( Intel i9 10900K, Nvidia Quadro P2200, 32G, 1T SSD )',
                status: '空闲',
            },
            {
                fixed_assets_num: 'TY20220800008',
                name: '惠普Z2工作站',
                model: 'Tower G5 ( Intel i9 10900K, Nvidia Quadro P2200, 32G, 1T SSD )',
                status: '空闲',
            },
            {
                fixed_assets_num: 'TY20220800009',
                name: '惠普Z2工作站',
                model: 'Tower G5 ( Intel i9 10900K, Nvidia Quadro P2200, 32G, 1T SSD )',
                status: '空闲',
            },
            {
                fixed_assets_num: 'TY20220800010',
                name: '惠普Z2工作站',
                model: 'Tower G5 ( Intel i9 10900K, Nvidia Quadro P2200, 32G, 1T SSD )',
                status: '空闲',
            },
            {
                fixed_assets_num: 'TY20220800011',
                name: '惠普Z2工作站',
                model: 'Tower G5 ( Intel i9 10900K, Nvidia Quadro P2200, 32G, 1T SSD )',
                status: '空闲',
            },
            {
                fixed_assets_num: 'TY20220800012',
                name: '惠普Z2工作站',
                model: 'Tower G5 ( Intel i9 10900K, Nvidia Quadro P2200, 32G, 1T SSD )',
                status: '空闲',
            },
        ]);
        const count = computed(() => dataSource.value.length + 1);
        const editableData = reactive({});
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
        return {
            columns,
            isShow,
            onDelete,
            handleAdd,
            dataSource,
            editableData,
            count,
            edit,
            save,
        };
    },
});
</script>
<style lang="less">
.editable-row-operations a {
  margin-right: 8px;
}
.editable-cell {
  position: relative;

  .editable-cell-input-wrapper,
  .editable-cell-text-wrapper {
    padding-right: 24px;
  }

  .editable-cell-text-wrapper {
    padding: 5px 24px 5px 5px;
  }

  .editable-cell-icon,
  .editable-cell-icon-check {
    position: absolute;
    right: 0;
    width: 20px;
    cursor: pointer;
  }

  .editable-cell-icon {
    margin-top: 4px;
    display: none;
  }

  .editable-cell-icon-check {
    line-height: 28px;
  }

  .editable-cell-icon:hover,
  .editable-cell-icon-check:hover {
    color: #108ee9;
  }

  .editable-add-btn {
    margin-bottom: 8px;
  }
}

.editable-cell:hover .editable-cell-icon {
  display: inline-block;
}
</style>