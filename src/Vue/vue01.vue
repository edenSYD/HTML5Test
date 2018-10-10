<template>
    <div>
        <div class="content-body">
            <!-- 头部 -->
            <div class="bd-top">
                <div class="md clearfix">
                    <!-- 1、左边标题 -->
                    <div class="md-left">
                        <h5>角色管理
                        </h5>
                    </div>
                    <!-- 2、右边操作按钮 -->
                    <div class="md-right">
                        <el-button size="mini" type="primary" class="button-submit">创建角色</el-button>
                    </div>
                </div>
            </div>
            <div class="bd-line"></div>
            <div class="bd-main">
                <my-table ref="multipleTable" :loading="loading" :tb="tb" :height="tb"></my-table>
                <!-- 分页 -->
                <pagination :total="totalData" :currentPage="conditionForm.currentPage" :pageSize="conditionForm.pageSize" @changeSize="handleSizeChange"
                            @handleCurrentPage="handleCurrentChange"></pagination>
            </div>
        </div>
    </div>
</template>
<script>
    import myTable from '../public_component/my-table'
    import pagination from '../public_component/pagination'
    const fields = [{
        key: "roleCode",
        label: "角色编号",
        width: "auto",
        type: '0'
    },
        {
            key: "roleName",
            label: "角色",
            width: "auto",
            type: '0'

        },
        {
            key: "createTime",
            label: "创建时间",
            width: "auto",
            type: '0',
            showOverflow:true,
        },
        {
            key: "updateTime",
            label: "修改时间",
            width: "auto",
            type: '0'
        },
    ]
    export default {
        data() {
            return {
                loading: true,
                // userlist:[],
                conditionForm: {
                    currentPage: 1,
                    pageSize: 10,
                },
                totalData: 0,
                pagesizeList: [10, 15, 20, 25, 30, 50],
                tb: {
                    fields: fields,
                    data: [],
                    height: 240,
                    operationName: '操作',
                    OperationBtn: [
                        {
                            type: 'text',
                            size: 'mini',
                            key: '1',
                            name: '编辑',
                            isShow:true,
                            icon: 'el-icon-edit',

                        }
                    ]
                },
            }
        },
        methods: {
            getList(data) {
                var queryParams;
                if (data) {
                    queryParams = data
                } else {
                    queryParams = this.conditionForm
                }
                this.$axios.post('/api/robot/role/getRoleList', queryParams).then(res => {
                    this.loading = false;
                    if (res.data.code == 0) {
                        console.log(res.data.data.items);
                        this.tb.data = res.data.data.items;
                        // 数据总条数
                        this.totalData = res.data.data.totalNum
                    } else {
                        this.$util.failCallback(res.data, this);
                    }
                }).catch(err => {
                    console.log(err);
                })
            },
            //    改变每页显示数据条数
            handleSizeChange(val) {
                this.conditionForm.pageSize = val;
                this.loading = true;
                this.getList(this.conditionForm);
            },
            //    改变当前页
            handleCurrentChange(val) {
                this.conditionForm.currentPage = val;
                console.log(`当前页: ${val}`);
                this.loading = true;
                this.getList(this.conditionForm);
            },

        },
        components: {
            "pagination": pagination,
            "my-table": myTable
        },
        created() {
            // 获取历史批次数据
            this.getList(this.conditionForm);
            this.formData = new FormData();
        }
    }

</script>
