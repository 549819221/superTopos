<!--项目表(project)-->
<template>
    <div :style="{height:(screenSize.height)+'px'}">
        <!-- 搜索框开始 -->
        <el-row  >
            <el-col :span="18">
                <el-form  class="demo-form-inline">
                    <el-row>
                        <el-col :span="8">
                            <el-form-item  label="名称" :label-width="formLabelWidth">
                                <el-input v-model="pageData.name_eq" placeholder="请输入名称" autocomplete="off"></el-input>
                            </el-form-item>
                        </el-col>
                        <el-col :span="8">
                            <el-form-item label="编号" :label-width="formLabelWidth">
                                <el-input v-model="pageData.number_eq" placeholder="请输入编号"></el-input>
                            </el-form-item>
                        </el-col>
                        <el-col :span="8">
                            <el-form-item label="创建人" :label-width="formLabelWidth">
                                <el-input v-model="pageData.create_eq" placeholder="请输入创建人" ></el-input>
                            </el-form-item>
                        </el-col>
                    </el-row>
                    <el-row>
                        <el-col :span="8">
                            <el-form-item label="创建时间" :label-width="formLabelWidth" >
                                <el-date-picker value-format="yyyy-MM-dd" v-model="pageData.creationTime" type="daterange" range-separator="至" start-placeholder="开始日期" end-placeholder="结束日期" style="width:100%"></el-date-picker>
                            </el-form-item>
                        </el-col>
                    </el-row>
                </el-form>
            </el-col>
            <el-col :span="4" :offset="2">
                <el-button type="primary"  @click.prevent="search()">搜索</el-button>
            </el-col>
        </el-row>
        <!-- 搜索框结束 -->
        <!-- 按钮框开始 -->
        <el-row class="spacing">
            <el-button type="primary" @click.prevent="goAdd()">新增</el-button>
            <el-button type="danger" @click.prevent="batchDel()">删除</el-button>
        </el-row>
        <!-- 按钮框结束 -->
        <!-- 列表框开始 -->
        <el-table :data="listData.content" ref="listTable" fixed v-loading="listData.loading" :height="screenSize.height - 190" fit border >
            <el-table-column  type="selection"></el-table-column>
            <el-table-column width="50" type="index" label="序号"></el-table-column>
            <el-table-column  property="name" label="名称" width="250" ></el-table-column>
            <el-table-column  property="number" label="编号" ></el-table-column>
            <el-table-column  property="remark" label="备注" ></el-table-column>
            <el-table-column  property="tablePrefix" label="表名前缀" ></el-table-column>
            <el-table-column  property="prefix" label="字段前缀" ></el-table-column>
            <el-table-column  property="dataBaseType" label="数据库类型" ></el-table-column>
            <el-table-column  property="dataBaseName" label="数据库名称" ></el-table-column>
            <el-table-column  property="insertIndex" label="继承字段插入列" ></el-table-column>
            <el-table-column  prop="projectType" label="项目类型" >
                  <template scope="scope">
                    {{ scope.row.projectType | paramsFmt('projectType') }}
                </template>
            </el-table-column>
            <!-- <el-table-column  property="create" label="创建人" ></el-table-column> -->
            <el-table-column  property="creationTime" label="创建时间" ></el-table-column>
            <!-- <el-table-column  property="lastUpdateUser" label="最后修改人" ></el-table-column> -->
            <!-- <el-table-column  property="lastUpdateTime" label="最后修改时间" ></el-table-column> -->
            <!-- <el-table-column  property="audit" label="审核人" ></el-table-column> -->
            <!-- <el-table-column  property="auditTime" label="审核时间" ></el-table-column> -->
            <el-table-column label="操作"  fixed="right">
                <template slot-scope="scope">
                    <el-button type="success" plain size="small" @click="goView(scope.row.id)">查看</el-button>
                    <el-button type="primary" plain size="small" @click="goEdit(scope.row.id)">编辑</el-button>
                </template>
            </el-table-column>
        </el-table>
        <!-- 列表框结束 -->
        <el-row :gutter="10" class="pagination">
            <el-pagination background  @size-change="handleSizeChange"  @current-change="handleCurrentChange" :page-size="listData.size" layout="total,prev, pager, next" :total="listData.totalElements"></el-pagination>
        </el-row>
        <!-- 新增/编辑开始 -->
        <el-dialog :title="viewDialog.isView ? '查看' : viewDialog.isView == null ? '新增' : '编辑'" :visible.sync="viewDialog.isShow" customClass="view-dialog" :close-on-click-modal= "false"  :fullscreen = "true">
            <el-form :model="data" v-loading="viewDialog.butIsLoading" :rules="rules" ref="ruleForm" >
                <el-row>
                    <el-col :span="12">
                        <el-form-item label="名称" clearable :label-width="formLabelWidth" prop="name">
                            <el-input :disabled="viewDialog.isView" v-model="data.name" placeholder="请输入名称" autocomplete="off"></el-input>
                        </el-form-item>
                    </el-col>
                    <el-col :span="12">
                        <el-form-item label="编号" clearable :label-width="formLabelWidth" prop="number">
                            <el-input :disabled="viewDialog.isView" v-model="data.number" placeholder="请输入编号" autocomplete="off"></el-input>
                        </el-form-item>
                    </el-col>
                </el-row>
                <el-row>
                     <el-col :span="12">
                        <el-form-item label="表名前缀" clearable :label-width="formLabelWidth" prop="tablePrefix">
                            <el-input :disabled="viewDialog.isView" v-model="data.tablePrefix" placeholder="请输入表名前缀" autocomplete="off"></el-input>
                        </el-form-item>
                    </el-col>
                    <el-col :span="12">
                        <el-form-item label="字段前缀" clearable :label-width="formLabelWidth" prop="prefix">
                            <el-input :disabled="viewDialog.isView" v-model="data.prefix" placeholder="请输入字段前缀" autocomplete="off"></el-input>
                        </el-form-item>
                    </el-col>
                </el-row>
                 <el-row>
                   
                    <el-col :span="12">
                        <el-form-item label="实体后缀" clearable :label-width="formLabelWidth" prop="prefix">
                            <el-input :disabled="viewDialog.isView" v-model="data.suffixEntity" placeholder="请输入实体后缀" autocomplete="off"></el-input>
                        </el-form-item>
                    </el-col>
                    <el-col :span="12">
                        <el-form-item label="数据库类型" clearable :label-width="formLabelWidth" prop="remark" >
                            <el-select v-model="data.dataBaseType" placeholder="请选择" :disabled="viewDialog.isView" style="width:100%">
                                <el-option v-for="item in getOptions('dataBaseType')" :key="item.label" :label="item.label" :value="item.value" ></el-option>
                            </el-select>                        
                        </el-form-item>
                    </el-col>
                </el-row>
                <el-row>
                    <el-col :span="12">
                        <el-form-item label="数据库名称" clearable :label-width="formLabelWidth" prop="number">
                            <el-input :disabled="viewDialog.isView" v-model="data.dataBaseName" placeholder="请输入数据库名称" autocomplete="off"></el-input>
                        </el-form-item>
                    </el-col>
                    <el-col :span="12">
                        <el-form-item label="继承字段插入列" clearable :label-width="formLabelWidth" prop="insertIndex" >
                            <el-select v-model="data.insertIndex" placeholder="请选择" :disabled="viewDialog.isView" style="width:100%">
                                <el-option v-for="item in data.detailEntitys.length" :key="item" :label="item" :value="item" ></el-option>
                            </el-select>                        
                        </el-form-item>
                    </el-col>
                </el-row>
                <el-row>
                    <el-col :span="12">
                        <el-form-item label="代码模板" clearable :label-width="formLabelWidth" prop="codeTemplate">
                             <el-select v-model="data.codeTemplate" placeholder="请选择代码模板" :disabled="viewDialog.isView" style="width:100%">
                                <el-option v-for="item in getOptions('codeTemplate')" :key="item.label" :label="item.label" :value="item.value" ></el-option>
                            </el-select> 
                        </el-form-item>
                    </el-col>
                     <el-col :span="12">
                        <el-form-item label="项目前端路径" clearable :label-width="formLabelWidth" prop="projectBeforePath">
                            <el-input :disabled="viewDialog.isView" v-model="data.projectBeforePath" placeholder="请输入项目前端路径" autocomplete="off"></el-input>
                        </el-form-item>
                    </el-col>
                </el-row>
                <el-row>
                    <el-col :span="12">
                        <el-form-item label="项目后端路径" clearable :label-width="formLabelWidth" prop="projectAfterPath">
                            <el-input :disabled="viewDialog.isView" v-model="data.projectAfterPath" placeholder="请输入项目后端路径" autocomplete="off"></el-input>
                        </el-form-item>
                    </el-col>
                    <el-col :span="12">
                        <el-form-item label="备注" clearable :label-width="formLabelWidth" prop="remark">
                            <el-input :disabled="viewDialog.isView" v-model="data.remark" placeholder="请输入备注" autocomplete="off"></el-input>
                        </el-form-item>
                    </el-col>
                </el-row>
                 <el-row>
                    <el-col :span="12">
                        <el-form-item label="项目类型" clearable :label-width="formLabelWidth" prop="projectType" >
                            <el-select v-model="data.projectType" placeholder="请选择" :disabled="viewDialog.isView" style="width:100%">
                                <el-option v-for="item in getOptions('projectType')" :key="item.label" :label="item.label" :value="item.value" ></el-option>
                            </el-select>                        
                        </el-form-item>
                    </el-col>
                </el-row>
            </el-form>
            <!-- 新增明细开始 -->
            <el-row class="spacing" v-show="!viewDialog.isView">
                <el-button type="primary" @click.prevent="addDetails()" :loading="viewDialog.butIsLoading">新增</el-button>
                <el-button type="warning"  @click.prevent="up()" :loading="viewDialog.butIsLoading" plain>上移</el-button>
                <el-button type="primary"  @click.prevent="down()" :loading="viewDialog.butIsLoading" plain>下移</el-button>
            </el-row>
            <el-table :data="data.detailEntitys"  border :height="screenSize.height - 400"  v-loading="viewDialog.butIsLoading" @current-change="currentChange" :row-class-name="tableRowClassName"  :highlight-current-row="!viewDialog.isView" class="tb-edit" >
                <el-table-column width="50" type="index" label="序号"></el-table-column>
                <el-table-column label="名称">
                    <template scope="scope">
                        <el-input v-model="scope.row.name" placeholder="请输入名称" clearable></el-input>
                        <span>{{scope.row.name}}</span>
                    </template>
                </el-table-column>
                <el-table-column label="属性名" width="150" >
                    <template scope="scope">
                        <el-input v-model="scope.row.number" placeholder="请输入属性名" clearable></el-input>
                        <span>{{scope.row.number}}</span>
                    </template>
                </el-table-column>
                <el-table-column label="字段属性" width="150" >
                    <template scope="scope">
                        <el-select v-model="scope.row.columnProperties" placeholder="请选择字段属性" @change="columnPropertiesChange(scope.row)">
                            <el-option v-for="item in getOptions('columnProperties')" :key="item.label" :label="item.label" :value="item.value"> </el-option>
                        </el-select>
                        <span>{{scope.row.columnProperties | paramsFmt('columnProperties')}}</span>
                    </template>
                </el-table-column>
                <el-table-column label="字段类型" width="200" >
                    <template scope="scope">
                        <el-select v-model="scope.row.type" placeholder="请选择字段类型" :disabled="scope.row.columnProperties != 'baseColumn'" @change="typeChange(scope.row)">
                            <el-option v-for="item in getOptions('columnType')" :key="item.label" :label="item.label" :value="item.value"> </el-option>
                        </el-select>
                        <span>{{scope.row.type | paramsFmt('columnType')}}</span>
                    </template>
                </el-table-column>
                <el-table-column label="关联表" width="150" >
                    <template scope="scope">
                        <el-select v-model="scope.row.linkTable" placeholder="请选择关联表" :disabled="scope.row.columnProperties != 'linkColumn'">
                            <el-option v-for="item in tableOptions" :key="item.label" :label="item.label" :value="item.value"> </el-option>
                        </el-select>
                        <span>{{scope.row.linkTable | optionsFmt(tableOptions)}}</span>
                    </template>
                </el-table-column>
                <el-table-column label="关联参数" >
                    <template scope="scope">
                        <el-input  v-model="scope.row.linkParam" placeholder="请输入关联参数" clearable :disabled="scope.row.columnProperties != 'paramColumn'"></el-input> <span>{{scope.row.linkParam}}</span>
                    </template>
                </el-table-column>
                <el-table-column label="是否主键">
                    <template scope="scope">
                        <el-select v-model="scope.row.isKey" placeholder="请选择" >
                            <el-option v-for="item in getOptions('yesOrNo')" :key="item.label" :label="item.label" :value="item.value" ></el-option>
                        </el-select>
                        <span>{{scope.row.allowEmpty | paramsFmt('yesOrNo')}}</span>
                    </template>
                </el-table-column>
                <el-table-column label="允许为空">
                    <template scope="scope">
                        <el-select v-model="scope.row.allowEmpty" placeholder="请选择" >
                            <el-option v-for="item in getOptions('yesOrNo')" :key="item.label" :label="item.label" :value="item.value" ></el-option>
                        </el-select>
                        <span>{{scope.row.allowEmpty | paramsFmt('yesOrNo')}}</span>
                    </template>
                </el-table-column>
                <el-table-column label="长度">
                    <template scope="scope">
                        <el-input v-model="scope.row.length" placeholder="请输入长度" clearable :disabled="scope.row.columnProperties != 'baseColumn'"></el-input>
                        <span>{{scope.row.length}}</span>
                    </template>
                </el-table-column>
                <el-table-column label="小数位">
                    <template scope="scope">
                        <el-input v-model="scope.row.places" placeholder="请输入小数位" clearable :disabled="scope.row.columnProperties != 'baseColumn'"></el-input>
                        <span>{{scope.row.places}}</span>
                    </template>
                </el-table-column>
                <el-table-column label="默认值">
                    <template scope="scope">
                        <el-input v-model="scope.row.defaultValue" placeholder="请输入默认值" clearable :disabled="scope.row.columnProperties != 'baseColumn'"></el-input>
                        <span>{{scope.row.defaultValue}}</span>
                    </template>
                </el-table-column>
                <el-table-column label="操作" v-if="!viewDialog.isView"  fixed="right">
                    <template slot-scope="scope">
                        <el-button type="danger" plain size="small" @click="delDetails(scope.$index)">删除</el-button>
                    </template>
                </el-table-column>
            </el-table>
            <!-- 新增明细结束 -->
            <span slot="footer" class="dialog-footer" v-show="!viewDialog.isView">
                <el-button @click="viewDialog.isShow = false" :loading="viewDialog.butIsLoading">取 消</el-button>
                <el-button type="primary" @click="save()" :loading="viewDialog.butIsLoading">保 存</el-button>
            </span>
        </el-dialog>
        <!-- 新增/编辑结束 -->
    </div>
</template>
<script>
    export default {
        created() {
            this.search();
            this.getParams();
            this.refreshScreenSize();
        },
        data() {
            return {
                formLabelWidth: "110px",
                pageData:{
                    page:1,
                    size:20,
                    sort:'creationTime,DESC',
                },
                listData: {
                    loading:false,
                },
                viewDialog:{
                    isShow: false,
                    isView: false,
                    butIsLoading: false,
                },
                tableOptions:[],
                data: {
                    detailEntitys: []
                },
                currentIndex:null,
                rules: {
                    name : [
                        { required: false, message: '请输入名称', trigger: 'blur' },
                    ],
                    number : [
                        { required: false, message: '请输入编号', trigger: 'blur' },
                    ],
                    remark : [
                        { required: false, message: '请输入备注', trigger: 'blur' },
                    ],
                }
            };
        },
        methods: {
            //执行搜索
            search() {
                
                this.listData.loading = true;
                if(!this.isEmpty(this.pageData.creationTime)){
                    this.pageData['creationTime_ge'] = this.pageData.creationTime[0]+'T00:00:00';
                    this.pageData['creationTime_le'] = this.pageData.creationTime[1]+'T00:00:00';
                }
                this.pageData.projectId_isNull = null;
                this.getHttp("/api/project/findAllPageByParams?"+this.jsonToUrl(this.pageData)).then(result => {
                    this.listData = result;
                    this.listData.loading = false;
                });
            },
            //批量删除
            batchDel(id) {
                if (this.isEmpty(id) && this.$refs.listTable.selection.length == 0) {
                    this.$message.warning("请先勾选删除列");
                    return;
                }
                this.$confirm('确定要进行删除操作吗?', '提示', {confirmButtonText: '确定',cancelButtonText: '取消',type: 'warning'}).then(() => {
                    if(this.isNotEmpty(id)){
                        this.getHttp("/api/project/delete?id="+id).then(result => {
                            this.search();
                        });
                    }else{
                        var ids = this.listExtract(this.$refs.listTable.selection,'id');
                        this.postHttp("/api/project/batchDelete",ids).then(result => {
                            this.search();
                        });
                    }
                })
            },
            //页码更新
            handleCurrentChange(val) {
                this.pageData.page = val;
                this.search();
            },
            //打开新增
            goAdd() {
                this.tableOptions = [];
                this.viewDialog.isShow = true;
                this.viewDialog.isView = null;
                this.data = {
                    level: 1,
                    detailEntitys: []
                };
            },
            //打开详情
            goView(id) {
                this.getTableOptions(id);
                this.viewDialog.isView = true;
                this.getHttp("/api/project/view?id="+id,{}).then(result => {
                    this.viewDialog.isShow = true;
                    this.data = result;
                });
            },
            //打开编辑
            goEdit(id) {
                this.getTableOptions(id);
                this.viewDialog.isView = false;
                this.getHttp("/api/project/view?id="+id).then(result => {
                    this.viewDialog.isShow = true;
                    this.data = result;
                });
            },
            //添加明细
            addDetails() {
                var length = this.data.detailEntitys.length;
                this.data.detailEntitys.push({
                    index : length,
                    allowEmpty: "Y",
                    columnProperties : 'baseColumn',
                    type : 'varchar',
                    length : '200',
                    isKey: "N"
                });
            },
            //删除明细
            delDetails(index) {
                this.data.detailEntitys.splice(index,1);
            },
            //保存
            save(){
                this.$refs.ruleForm.validate((valid) => {
                    if (valid) {
                        this.viewDialog.butIsLoading = true;
                        for(var i = 0; i < this.data.detailEntitys.length; i++){
                            this.data.detailEntitys[i].id = null;
                        }
                        this.postHttp("/api/project/save",this.data).then(result => {
                            this.viewDialog.butIsLoading = false;
                            if(result.code == 200){
                                this.viewDialog.isShow = false;
                                this.search();
                            }
                        });
                    } else {
                        return false;
                    }
                });
            },
            //获取table 下拉参数
            getTableOptions(id) {
                this.getHttp("/api/project/getTableOptions?id="+id,{}).then(result => {
                    this.tableOptions = result;
                });
            },
            //字段属性改变
            columnPropertiesChange(data){
                var columnProperties = data.columnProperties;
                if(columnProperties == 'baseColumn'){
                    data.type = 'varchar'; 
                    data.length = '200'; 
                }else if(columnProperties == 'linkColumn'){
                    data.type = ''; 
                    data.length = ''; 
                }else if(columnProperties == 'paramColumn'){
                    data.type = ''; 
                    data.length = ''; 
                }
                data.linkTable = ''; 
                data.linkParam = ''; 
                data.allowEmpty = 'Y';
            },
             //字段类型改变
            typeChange(data){
                var type = data.type;
                if(type == 'int'){
                    data.length = '11'; 
                    data.places = '0'; 
                }else if(type == 'varchar'){
                    data.length = '200'; 
                    data.places = ''; 
                }else if(type == 'decimal'){
                    data.length = '11'; 
                    data.places = '2'; 
                }else if(type == 'datetime'){
                    data.length = ''; 
                    data.places = ''; 
                }else if(type == 'char'){
                    data.length = '1'; 
                    data.places = ''; 
                }
                data.defaultValue = ''; 
            },
            //记录下标
            currentChange(row) {
                this.currentIndex = row.index;
            },
            //动态修改类名
            tableRowClassName({row, rowIndex}) {
                row.index = rowIndex;
                if(this.isView){
                    return 'warning-row';
                } else { 
                    return 'edit-row';
                }
            },
            //上移
            up(){
                if(this.currentIndex == 0){
                    return;
                }
                this.data.detailEntitys = Object.assign([],this.upIndex(this.data.detailEntitys,this.currentIndex));
                this.data.detailEntitys[this.currentIndex].index = this.currentIndex-1;
                this.data.detailEntitys[this.currentIndex-1].index = this.currentIndex+1;
                this.currentIndex = this.currentIndex-1;
            },
            //下移
            down(){
                if(this.currentIndex == this.data.detailEntitys.length -1){
                    return;
                }
                this.data.detailEntitys = Object.assign([],this.downIndex(this.data.detailEntitys,this.currentIndex)); 
                this.data.detailEntitys[this.currentIndex].index = this.currentIndex+1;
                this.data.detailEntitys[this.currentIndex+1].index = this.currentIndex-1;
                this.currentIndex = this.currentIndex+1;
            },
        }
    };
</script>
<style scoped>

    

</style>