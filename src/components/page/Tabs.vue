<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-tickets"></i> 通告信息列表</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
            <el-button type="primary" icon="delete" class="handle-del mr10" @click="delAll">删除公告</el-button>
                <el-button type="primary" icon="delete" class="handle-del mr10" @click="add">添加公告</el-button>

                <!--<el-select v-model="select_cate" placeholder="筛选省份" class="handle-select mr10">-->
            <!--<el-option key="1" label="广东省" value="广东省"></el-option>-->
            <!--<el-option key="2" label="湖南省" value="湖南省"></el-option>-->
            <!--</el-select>-->
            <!--<el-input v-model="select_word" placeholder="筛选关键词" class="handle-input mr10"></el-input>-->
            <!--<el-button type="primary" icon="search" @click="search">搜索</el-button>-->
            </div>
            <el-table
                ref="multipleTable"
                :data="tableData"
                tooltip-effect="dark"
                style="width: 100%"
                @selection-change="handleSelectionChange">
                <el-table-column
                    type="selection"
                    width="33">
                </el-table-column>
                <el-table-column
                    prop="announceId"
                    label="通告编号"
                    width="80">
                </el-table-column>
                <el-table-column
                    prop="airportId"
                    label="机场编号"
                    width="130">
                </el-table-column>
                <el-table-column
                    prop="airportName"
                    label="机场名称"
                    width="100"
                >
                </el-table-column>
                <el-table-column prop="startAt" label="生效时间" sortable width="160">
                </el-table-column>
                <el-table-column prop="endAt" label="失效时间" sortable width="160">
                </el-table-column>



                <el-table-column
                    prop="airRoute"
                    label="航路"
                >
                </el-table-column>

                <el-table-column
                    prop="isForbid"
                    label="限航状态"
                >
                </el-table-column>

                <el-table-column
                    prop="content"
                    width="100px"
                    label="内容"
                >
                </el-table-column>

                <el-table-column
                    prop="isClose"
                    label="关闭状态"
                >
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
        <el-dialog title="添加通告" :visible.sync="addVisible" width="50%">
            <el-form :inline="true" :model="form" class="demo-form-inline">
                <el-form-item label="机场编号">
                    <el-input v-model="form.airportId" placeholder="机场编号"></el-input>
                </el-form-item>
              &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <el-form-item label="机场名称">
                    <el-input v-model="form.airportName" placeholder="机场名称"></el-input>
                </el-form-item>

                <el-form-item label="通告编号">
                    <el-input v-model="form.announceId" placeholder="机场编号"></el-input>
                </el-form-item>

                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <el-form-item label="生效时间">
                    <el-col :span="21">
                        <el-form-item prop="date1">
                            <el-date-picker type="datetime" placeholder="选择日期" v-model="form.startAt" style="width: 100%;"></el-date-picker>
                        </el-form-item>
                    </el-col>
                </el-form-item>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <el-form-item label="航    路 ">
                    <el-input v-model="form.airRoute" placeholder="机场编号" width="100%"></el-input>
                </el-form-item>



                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <el-form-item label="失效时间">
                    <el-col :span="21">
                        <el-form-item prop="date1">
                            <el-date-picker type="datetime" placeholder="选择日期" v-model="form.endAt" style="width: 100%;"></el-date-picker>
                        </el-form-item>
                    </el-col>

                </el-form-item>

                <el-form-item label="航路状态">
                    <template>
                        <el-radio-group v-model="form.isForbid">
                            <el-radio :label="0">限航</el-radio>
                            <el-radio :label="1">不限航</el-radio>
                        </el-radio-group>
                    </template>

                </el-form-item>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <el-form-item label="关闭状态">
                    <template>
                        <el-radio-group v-model="form.isClose">
                            <el-radio :label="0">关闭</el-radio>
                            <el-radio :label="1">未关闭</el-radio>
                        </el-radio-group>
                    </template>

                </el-form-item>

                <el-form-item label="内容"  >
                    <el-input type="textarea" style="width: 500px" autosize v-model="form.content" placeholder="机场编号" width="100%"></el-input>
                </el-form-item>

            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="addVisible = false">取 消</el-button>
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
                radio:0,
                close:0,
                tableData: [
                    {
                        "airRoute": "YLLH",
                        "airportId": "yinchuan",
                        "airportName": "银川",
                        "announceId": "C100",
                        "content": "低压",
                        "endAt": "2019-06-27 19:23:22",
                        "isClose": "未关闭",
                        "isForbid": "限航",
                        "startAt": "2019-06-27 15:23:22"
                    },
                    {
                        "airRoute": "YLLM",
                        "airportId": "yinchuan",
                        "airportName": "银川",
                        "announceId": "C101",
                        "content": "因施工",
                        "endAt": "2019-06-27 19:23:22",
                        "isClose": "已关闭",
                        "isForbid": "不限航",
                        "startAt": "2019-06-27 15:23:22"
                    },
                    {
                        "airRoute": "YLLZ",
                        "airportId": "changchun",
                        "airportName": "长春",
                        "announceId": "C102",
                        "content": "高压",
                        "endAt": "2019-06-27 19:23:22",
                        "isClose": "未关闭",
                        "isForbid": "限航",
                        "startAt": "2019-06-27 15:23:22"
                    },
                ],
                cur_page: 1,
                multipleSelection: [],
                select_cate: '',
                select_word: '',
                del_list: [],
                is_search: false,
                addVisible: false,
                delVisible: false,
                form: {
                    airRoute:'',
                    endAt: '',
                    content: '',
                    airportId:'',
                    airportName:'',
                    startAt: '',
                    isForbid: 0,
                    isClose:0,
                    announceId:''
                },
                formInline: {
                    user: '',
                    region: '',
                    content:'4000-5000米气压过高'
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

            onSubmit() {
                console.log('submit!');
            },
            // 获取 easy-mock 的模拟数据
            getData() {
                var pageNum = this.pageInfo.pageNum
                this.$axios.get('/api/airport/getPlaneNoticeList?pageNum='+pageNum).then((res) => {
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
                this.delVisible = true;
            },
            handleSelectionChange(val) {
                this.multipleSelection = val;
                console.log(val)
            },
            // 保存编辑
            saveEdit() {
                this.$axios.post('/api/airport/insertOrUpdateNotice',this.form).then((res) => {
                    console.log(res)
                    if (res.data.code == 0){
                        this.$message.success(res.data.data);
                        this.getData()
                    }else {
                        this.$message.error(res.data.message);
                    }
                })
                this.addVisible=false;
            },
            // 确定删除
            deleteRow(){
                this.$axios.post('/api/airport/deleteNotice',this.multipleSelection).then((res) => {
                    console.log(res)
                    if (res.data.code == 0){
                        this.$message.success(res.data.data);
                        this.getData()
                    }else {
                        this.$message.error(res.data.message);
                    }
                })

                this.delVisible = false;
            },
            add(){
                this.addVisible = true;
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
