<template>
    <el-card class="">
        <template #header>
            <div class="nexus-table-header">
                <div class="left">

                </div>
                <div class="right">
                    <el-button type="primary" size="small" icon="Plus" @click="handleAdd">Add</el-button>
                </div>
            </div>
        </template>
        <el-table
            v-loading="loading"
            ref="multipleTable"
            :data="tableData"
            tooltip-effect="dark"
            @selection-change="handleSelectionChange">
            <el-table-column
                type="selection"
                width="55">
            </el-table-column>
            <el-table-column
                prop="id"
                label="Id"
                width="50"
            >
            </el-table-column>
            <el-table-column
                prop="name"
                label="Name"
            >
            </el-table-column>
            <el-table-column
                label="Indexes"
                width="250px"
            >
                <template #default="scope" >
                    <p style="white-space: pre-line" v-html="scope.row.indexes_formatted"></p>
                </template>
            </el-table-column>
            <el-table-column
                prop="begin"
                label="Begin"
                width="160"
            >
            </el-table-column>
            <el-table-column
                prop="end"
                label="End"
                width="160"
            >
            </el-table-column>

            <el-table-column
                prop="duration_text"
                label="Duration"
            ></el-table-column>

            <el-table-column
                label="Target users"
                width="350px"
            >
                <template #default="scope" >
                    <p style="white-space: pre-line" v-html="scope.row.filters_formatted"></p>
                </template>
            </el-table-column>

            <el-table-column
                prop="is_discovered_text"
                label="Discovered"
            >
            </el-table-column>

            <el-table-column
                prop="status_text"
                label="Status"
            >
            </el-table-column>

            <el-table-column
                label="Action"
                width="100"
            >
                <template #default="scope">
                    <a style="cursor: pointer; margin-right: 10px" @click="handleEdit(scope.row.id)">Edit</a>
                    <el-popconfirm
                        title="Confirm Delete ?"
                        @confirm="handleDelete(scope.row.id)"
                    >
                        <template #reference>
                            <a style="cursor: pointer">Delete</a>
                        </template>
                    </el-popconfirm>
                </template>
            </el-table-column>
        </el-table>
        <!--总数超过一页，再展示分页器-->
        <el-pagination
            background
            layout="prev, pager, next"
            :total="total"
            :page-size="perPage"
            :current-page="currentPage"
            @current-change="changePage"
        />
    </el-card>
</template>

<script>
import { onMounted, reactive, ref, toRefs } from 'vue'
import { ElMessage } from 'element-plus'
import { useRouter } from 'vue-router'
import api from '../../utils/api'
import { useTable, renderTableData } from '../../utils/table'

export default {
    name: 'ExamTable',
    setup() {
        const multipleTable = ref(null)
        const router = useRouter()

        const state = useTable()

        onMounted(() => {
            console.log('ExamTable onMounted')
            fetchTableData()
        })
        const fetchTableData = async () => {
            state.loading = true
            let res = await api.listExam(state.query)
            renderTableData(res, state)
            state.loading = false
        }
        const handleAdd = () => {
            router.push({ name: 'exam-form' })
        }
        const handleEdit = (id) => {
            router.push({ path: '/exam-form', query: { id } })
        }
        const handleDelete = async (id) => {
            let res = await api.deleteExam(id)
            ElMessage.success(res.msg)
            state.query.page = 1;
            await fetchTableData()
        }
        const handleSelectionChange = (val) => {
            state.multipleSelection = val
        }
        const changePage = (val) => {
            state.query.page = val
            fetchTableData()
        }
        return {
            ...toRefs(state),
            multipleTable,
            handleSelectionChange,
            handleAdd,
            handleEdit,
            handleDelete,
            fetchTableData,
            changePage,
        }
    }
}
</script>

<style scoped>
.swiper-container {
    min-height: 100%;
}
.el-card.is-always-shadow {
    min-height: 100%!important;
}
</style>
