<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-tickets"></i> 关闭通告信息及影响航线列表</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <!--<div class="handle-box">-->
            <!--<el-button type="primary" icon="delete" class="handle-del mr10" @click="delAll">批量删除</el-button>-->
            <!--<el-select v-model="select_cate" placeholder="筛选省份" class="handle-select mr10">-->
            <!--<el-option key="1" label="广东省" value="广东省"></el-option>-->
            <!--<el-option key="2" label="湖南省" value="湖南省"></el-option>-->
            <!--</el-select>-->
            <!--<el-input v-model="select_word" placeholder="筛选关键词" class="handle-input mr10"></el-input>-->
            <!--<el-button type="primary" icon="search" @click="search">搜索</el-button>-->
            <!--</div>-->


            <el-table
                :data="tableData"
                style="width: 100%">
                <el-table-column type="expand">
                    <template slot-scope="props">
                        <el-table
                            :data="props.row.planePlansEntityList"
                            style="width: 100%"
                        >
                            <el-table-column
                                prop="airRoute"
                                label="航班号"
                                sortable
                                width="180">
                            </el-table-column>
                            <el-table-column
                                prop="enAirportId"
                                label="起飞飞机编号"
                                sortable
                                width="180">
                            </el-table-column>
                            <el-table-column
                                prop="stAirportName"
                                label="起飞机场名称"
                            >
                            </el-table-column>
                            <el-table-column
                                prop="startAt"
                                label="计划起飞时间"
                            >
                            </el-table-column>
                            <el-table-column
                                prop="enAirportId"
                                label="降落机场编号"
                            >
                            </el-table-column>

                            <el-table-column
                                prop="enAirportName"
                                label="降落机场名称"
                            >
                            </el-table-column>
                            <el-table-column
                                prop="endAt"
                                label="计划落地时间"
                            >
                            </el-table-column>
                            <el-table-column
                                prop="airRoute"
                                label="航路"
                            >
                            </el-table-column>
                        </el-table>
                    </template>
                </el-table-column>
                <el-table-column prop="planeNoticeEntity.announceId" label="通告编号" width="100">
                </el-table-column>
                <el-table-column prop="planeNoticeEntity.airportId" label="机场编码" width="150">
                </el-table-column>
                <el-table-column prop="planeNoticeEntity.airportName" label="机场名称" width="160">
                </el-table-column>
                <el-table-column prop="planeNoticeEntity.startAt" label="生效时间" sortable width="120">
                </el-table-column>
                <el-table-column prop="planeNoticeEntity.endAt" label="失效时间" sortable width="120">
                </el-table-column>
                <el-table-column prop="planeNoticeEntity.airRoute" label="航路" width="100">
                </el-table-column>
                <el-table-column prop="planeNoticeEntity.isForbid" label="限航状态" width="100">
                </el-table-column>
                <el-table-column prop="planeNoticeEntity.content" label="具体内容" >
                </el-table-column>
                <!--<el-table-column label="操作" >-->
                    <!--<template slot-scope="scope">-->
                        <!--<el-button size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>-->
                        <!--<el-button size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>-->
                    <!--</template>-->
                <!--</el-table-column>-->
            </el-table>
            <div class="pagination">
                <el-pagination @current-change="handleCurrentChange" layout="prev, pager, next" :total="1000">
                </el-pagination>
            </div>
        </div>

        <!-- 编辑弹出框 -->
        <el-dialog title="编辑" :visible.sync="editVisible" width="30%">
            <el-form ref="form" :model="form" label-width="50px">
                <el-form-item label="日期">
                    <el-date-picker type="date" placeholder="选择日期" v-model="form.date" value-format="yyyy-MM-dd" style="width: 100%;"></el-date-picker>
                </el-form-item>
                <el-form-item label="姓名">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="地址">
                    <el-input v-model="form.address"></el-input>
                </el-form-item>

            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false">取 消</el-button>
                <el-button type="primary" @click="saveEdit">确 定</el-button>
            </span>
        </el-dialog>

        <!-- 删除提示框 -->
        <el-dialog title="提示" :visible.sync="delVisible" width="300px" center>
            <div class="del-dialog-cnt">删除不可恢复，是否确定删除？</div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="delVisible = false">取 消</el-button>
                <el-button type="primary" @click="deleteRow">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
    export default {
        name: 'basetable',
        data() {
            return {
                url: './static/vuetable.json',
                tableData: [],
                cur_page: 1,
                multipleSelection: [],
                select_cate: '',
                select_word: '',
                del_list: [],
                is_search: false,
                editVisible: false,
                delVisible: false,
                form: {
                    name: '',
                    date: '',
                    address: ''
                },
                idx: -1,
                pageInfo:{
                    pageNum:0
                }
            }
        },
        created() {
            this.getData();
        },
        computed: {
            data() {

            }
        },
        methods: {
            // 分页导航
            handleCurrentChange(val) {
                this.pageInfo.pageNum = val-1;
                this.getData();
            },
            // 获取 easy-mock 的模拟数据
            getData() {
                var pageNum = this.pageInfo.pageNum
                this.$axios.get('/api/airport/getBansNoticeList?pageNum='+pageNum).then((res) => {
                    this.tableData = res.data.data;
                })
            },
            search() {
                this.is_search = true;
            },
            formatter(row, column) {
                return row.address;
            },
            filterTag(value, row) {
                return row.tag === value;
            },
            handleEdit(index, row) {
                this.idx = index;
                const item = this.tableData[index];
                this.form = {
                    name: item.name,
                    date: item.date,
                    address: item.address
                }
                this.editVisible = true;
            },
            handleDelete(index, row) {
                this.idx = index;
                this.delVisible = true;
            },
            delAll() {
                const length = this.multipleSelection.length;
                let str = '';
                this.del_list = this.del_list.concat(this.multipleSelection);
                for (let i = 0; i < length; i++) {
                    str += this.multipleSelection[i].name + ' ';
                }
                this.$message.error('删除了' + str);
                this.multipleSelection = [];
            },
            handleSelectionChange(val) {
                this.multipleSelection = val;
            },
            // 保存编辑
            saveEdit() {
                this.$set(this.tableData, this.idx, this.form);
                this.editVisible = false;
                this.$message.success(`修改第 ${this.idx+1} 行成功`);
            },
            // 确定删除
            deleteRow(){
                this.tableData.splice(this.idx, 1);
                this.$message.success('删除成功');
                this.delVisible = false;
            }
        }
    }

</script>

<style scoped>
    .handle-box {
        margin-bottom: 20px;
    }

    .handle-select {
        width: 120px;
    }

    .handle-input {
        width: 300px;
        display: inline-block;
    }
    .del-dialog-cnt{
        font-size: 16px;
        text-align: center
    }
</style>
