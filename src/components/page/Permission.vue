<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-tickets"></i> 计划信息列表</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
                <el-button type="primary" icon="delete" class="handle-del mr10" @click="delAll">删除计划</el-button>
                <el-button type="primary" icon="delete" class="handle-del mr10" @click="add">添加计划</el-button>

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
                    width="55">
                </el-table-column>
                <el-table-column
                    prop="flightNum"
                    label="航班号"
                    width="80">
                </el-table-column>
                <el-table-column
                    prop="stAirportId"
                    label="起飞机场编号"
                    width="150">
                </el-table-column>
                <el-table-column
                    prop="stAirportName"
                    label="起飞机场名称"
                    width="160"
                >
                </el-table-column>                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

                <el-table-column prop="startAt" label="计划起飞时间" sortable width="160">
                </el-table-column>
                <el-table-column
                    prop="enAirportId"
                    label="降落机场编号"
                    width="150">
                </el-table-column>
                <el-table-column
                    prop="enAirportName"
                    label="降落机场名称"
                    width="160"
                ></el-table-column>                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

                <el-table-column prop="endAt" label="计划落地时间" sortable width="160">
                </el-table-column>

                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

                <el-table-column
                    prop="airRoute"
                    label="航  路"
                >
                </el-table-column>


            </el-table>
            <div class="pagination">
                <el-pagination @current-change="handleCurrentChange" layout="prev, pager, next" :total="1000">
                </el-pagination>
            </div>
        </div>

        <!-- 编辑弹出框 -->
        <el-dialog title="添加计划" :visible.sync="addVisible" width="73%">
            <el-form :inline="true" :model="formInline" class="demo-form-inline">
                <el-form-item label="起飞机场编号">
                    <el-input v-model="formInline.stAirportId" placeholder="起飞机场编号"></el-input>
                </el-form-item>
                &nbsp;&nbsp;
                <el-form-item label="起飞机场名称">
                    <el-input v-model="formInline.stAirportName" placeholder="起飞机场名称"></el-input>
                </el-form-item>

                <el-form-item label="起飞时间">

                    <el-col :span="21">
                        <el-time-picker placeholder="选择时间" v-model="formInline.startAt" style="width: 100%;"></el-time-picker>
                    </el-col>
                </el-form-item>

                <el-form-item label="降落机场编号">
                    <el-input v-model="formInline.enAirportId" placeholder="降落机场编号"></el-input>
                </el-form-item>
                &nbsp;&nbsp;
                <el-form-item label="降落机场名称">
                    <el-input v-model="formInline.enAirportName" placeholder="降落机场名称"></el-input>
                </el-form-item>

                <el-form-item label="降落时间">

                    <el-col :span="21">
                        <el-time-picker placeholder="选择时间" v-model="formInline.endAt" style="width: 100%;"></el-time-picker>
                    </el-col>
                </el-form-item>

                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

                <el-form-item label="航    路 ">
                    <el-input v-model="formInline.airRoute" placeholder="航    路" width="100%"></el-input>
                </el-form-item>

                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

                <el-form-item label="航班号">
                    <el-input    v-model="formInline.flightNum"
                              placeholder="航班号" width="100%"></el-input>
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
                radio: 0,
                close: 0,
                tableData: [],
                cur_page: 1,
                multipleSelection: [],
                select_cate: '',
                select_word: '',
                del_list: [],
                is_search: false,
                addVisible: false,
                delVisible: false,

                formInline: {
                    stAirportId: '',
                    stAirportName: '',
                    startAt: '',
                    enAirportId:'',
                    enAirportName:'',
                    endAt:'',
                    airRoute:'',
                    flightNum:''
                },
                pageInfo:{
                  pageNum:0
                },
                idx: -1
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
                this.$axios.get('/api/airport/getPlanePlansList?pageNum='+pageNum).then((res) => {
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
                if(this.multipleSelection.length>0){
                    this.delVisible = true;
                }else{
                    this.$message.success('请选择删除的数据');
                }
            },
            handleSelectionChange(val) {
                this.multipleSelection = val;
            },
            // 保存编辑
            saveEdit() {
                var d1 = new Date(this.formInline.startAt);
                var d2 = new Date(this.formInline.endAt);

                this.formInline.startAt=d1.getHours()+':'+d1.getMinutes()+":"+d1.getSeconds();
                this.formInline.endAt=d2.getHours()+':'+d2.getMinutes()+":"+d2.getSeconds();
                console.log(this.formInline)
                this.$axios.post('/api/airport/insertOrUpdatePlane',this.formInline).then((res) => {
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
            deleteRow() {
                this.$axios.post('/api/airport/deletePlane',this.multipleSelection).then((res) => {
                    console.log(res)
                    if (res.data.code == 0){
                        this.$message.success(res.data.data);
                        this.getData()
                    }else {
                        this.$message.error(res.data.message);
                    }
                })
                this.multipleSelection=[];
                this.delVisible = false;
            },
            add() {
                this.formInline.airRoute='';
                this.formInline.enAirportId='';
                this.formInline.enAirportName='';
                this.formInline.flightNum='';
                this.formInline.stAirportId='';
                this.formInline.endAt='';
                this.formInline.startAt='';
                this.formInline.stAirportName='';
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

    .del-dialog-cnt {
        font-size: 16px;
        text-align: center
    }
</style>
