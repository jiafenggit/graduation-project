<template>
<section  class="goodsdetail">
		<!--工具条查询框-->
		<el-col :span="24" class="toolbar">
			<el-form :inline="true" :model="filters">
				<el-form-item>
					<el-input v-model="filters.name" placeholder="姓名"></el-input>
				</el-form-item>
				<el-form-item>
					<el-button type="primary" @click="getUsers">查询</el-button>
				</el-form-item>
				<el-form-item>
					<el-button type="primary" @click="handleAdd">新增</el-button>
				</el-form-item>
			</el-form>
		</el-col>

<!--列表-->
<template>
		<el-table :data="users" highlight-current-row v-loading="listLoading" style="width: 100%;">
    <el-table-column type="index" width="60">
    </el-table-column>
    <el-table-column prop="name" label="姓名" width="120" sortable>
    </el-table-column>
    <el-table-column prop="sex" label="性别" width="100" :formatter="formatSex" sortable>
    </el-table-column>
    <el-table-column prop="age" label="年龄" width="100" sortable>
    </el-table-column>
    <el-table-column prop="birth" label="生日" width="120" sortable>
    </el-table-column>
    <el-table-column prop="addr" label="地址" min-width="180" sortable>
    </el-table-column>
    <el-table-column inline-template :context="_self" label="操作" width="150">
    	<span>
      	<el-button size="small" @click="handleEdit(row)">编辑</el-button>
      	<el-button type="danger" size="small" @click="handleDel(row)">删除</el-button>
    	</span>
    </el-table-column>
    </el-table>
</template>

<!--分页-->
<el-col :span="24" class="toolbar" style="padding-bottom:10px;">
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="currentPage"
      :page-sizes="[20, 40, 60, 100]"
      :page-size="20"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
    </el-pagination>
</el-col>


<!--编辑界面-->
<el-dialog :title="editFormTtile" v-model="editFormVisible" :close-on-click-modal="false">
	<el-form :model="editForm" label-width="80px" :rules="editFormRules" ref="editForm">
		<el-form-item label="姓名" prop="name">
			<el-input v-model="editForm.name" auto-complete="off"></el-input>
		</el-form-item>
		<el-form-item label="性别">
			<!--<el-select v-model="editForm.sex" placeholder="请选择性别">
						<el-option label="男" :value="1"></el-option>
						<el-option label="女" :value="0"></el-option>
					</el-select>-->
			<el-radio-group v-model="editForm.sex">
				<el-radio class="radio" :label="1">男</el-radio>
				<el-radio class="radio" :label="0">女</el-radio>
			</el-radio-group>
		</el-form-item>
		<el-form-item label="年龄">
			<el-input-number v-model="editForm.age" :min="0" :max="200"></el-input-number>
		</el-form-item>
		<el-form-item label="生日">
			<el-date-picker type="date" placeholder="选择日期" v-model="editForm.birth"></el-date-picker>
		</el-form-item>
		<el-form-item label="地址">
			<el-input type="textarea" v-model="editForm.addr"></el-input>
		</el-form-item>
	</el-form>
	<div slot="footer" class="dialog-footer">
		<el-button @click.native="editFormVisible = false">取 消</el-button>
		<el-button type="primary" @click.native="editSubmit" :loading="editLoading">{{btnEditText}}</el-button>
	</div>
</el-dialog>

</section>

</template>

<script>
import Top from '../include/Top'
import {getUserListPage} from '../../services/api/api'
export default {
  name: 'goodsdetail',
  components: {
     Top
  },
  data: function() {
    	return {
				filters: {
					name: ''
				},
				users: [],
				total: 1,
				page: 1,
				currentPage: 1,
				listLoading: false,
				editFormVisible: false,//编辑界面显是否显示
				editFormTtile: '编辑',//编辑界面标题
				//编辑界面数据
				editForm: {
					id: 0,
					name: '',
					sex: -1,
					age: 0,
					birth: '',
					addr: ''
				},
				editLoading: false,
				btnEditText: '提 交',
				editFormRules: {
					name: [
						{ required: true, message: '请输入姓名', trigger: 'blur' }
					]
				}

			}
  },
   methods: {
    handleClick: function() {
      this.$toast('Hello world!')
    },
    formatSex: function(row,column) {
      	return row.sex == 1 ? '男' : row.sex == 0 ? '女' : '未知';
    },
    //CRUD
    getUsers:function() {
      console.log("user"+this.filters.name);
      let para = {
					page: this.page,
					name: this.filters.name
				};
				this.listLoading = true;
				getUserListPage(para).then((res) => {
					this.total = res.data.total;
					this.users = res.data.users;
					this.listLoading = false;
				});
      return 
    },
    handleAdd : function() {
      this.editFormVisible= true;
      this.editFormTtile = '新增';
    	this.editForm.id = 0;//重置参数
			this.editForm.name = '';
			this.editForm.sex = 1;
			this.editForm.age = 0;
			this.editForm.birth = '';
			this.editForm.addr = '';
    },
    handleDel: function(row) {
      console.log(row+"del")
    },
    handleEdit: function(row) {
        this.editFormVisible= true;
        this.editFormTtile = '编辑';
				this.editForm.id = row.id;
				this.editForm.name = row.name;
				this.editForm.sex = row.sex;
				this.editForm.age = row.age;
				this.editForm.birth = row.birth;
				this.editForm.addr = row.addr;
    },
    editSubmit: function(){
      
    },
    
    //分页有关的两个方法
    handleCurrentChange: function(val){
      console.log(val);
    },
    handleSizeChange: function(val) {
      console.log(val);
    },
    mounted: function() {
			this.getUsers();
		}
    
  }
}

</script>

<style scoped>

</style>
