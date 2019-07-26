<template> 
 <div class="block">   
	 	<el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true" style="text-align:right" >
				<!-- <el-form-item>
					<el-input  placeholder="姓名"></el-input>
				</el-form-item>
				<el-form-item>
					<el-button type="primary" v-on:click="getUsers">查询</el-button>
				</el-form-item> -->
				
				<el-form-item>
					<el-button type="primary" @click="add()">新增</el-button>
				</el-form-item>
			</el-form>
		</el-col>
        
		<el-table :data="datalist" highlight-current-row v-loading="listLoading" style="width: 100%;">
		
			
			<el-table-column prop="title" label="标题" width="120" sortable>
			</el-table-column>
			<el-table-column prop="img" label="地址" width="400" sortable>
			</el-table-column>
			<!-- <el-table-column prop="province" label="省份" width="120" sortable>
			</el-table-column>
			<el-table-column prop="city" label="城市" width="120" sortable>
			</el-table-column>
			<el-table-column prop="area" label="区县" min-width="120" sortable>
			</el-table-column> -->
			
			<!-- <el-table-column prop="createTime" :formatter="dateFormat"  label="创建时间" min-width="120" sortable>
						
			</el-table-column> -->
		
			<!-- <el-table-column  label="顺序" min-width="120">
				<template slot-scope="scope">{{ scope.row.bannerIdx +1 }}</template>
			</el-table-column> -->
			<el-table-column  label="创建时间" min-width="120">
				<template slot-scope="scope">{{ scope.row.createTime | moment('YYYY-MM-DD') }}</template>
			</el-table-column>
			<el-table-column  label="有效时间" min-width="120">
				<template slot-scope="scope">{{ scope.row.stopTime | moment('YYYY-MM-DD') }}</template>
			</el-table-column>
			<el-table-column prop="startCount" label="播放起始时间" min-width="180" sortable>
			</el-table-column>
			<el-table-column prop="stopCount" label="播放结束时间" min-width="180" sortable>
			</el-table-column>
			<el-table-column  label="状态" min-width="80">
				<template slot-scope="scope">{{ state(scope.row.state)}}</template>
			</el-table-column>
			
			<!-- <el-table-column prop="userName" label="管理员用户名" min-width="150">
			</el-table-column>
			<el-table-column label="管理员状态" min-width="110">
				<template slot-scope="scope">{{ state(scope.row.userStatus)}}</template>
			</el-table-column> -->
			<!-- <el-table-column prop="code" label="厂商编号" min-width="110">
			</el-table-column> -->
			<!-- <el-table-column prop="userWorkName" label="工程商用户名" min-width="150" sortable>
			</el-table-column>
			<el-table-column label="工程商状态" min-width="120">
				<template slot-scope="scope">{{ state(scope.row.userWorkStatus)}}</template>
			</el-table-column> -->
			<el-table-column label="操作" min-width="240">
				<template scope="scope">
				<!-- <el-button size="small" type="primary"  @click="editWork(scope.$index,scope.row)">工程商</el-button> -->
				<el-button size="small" type="primary"  @click="edit(scope.$index,scope.row)">编辑</el-button>
				<!-- <el-button size="small" type="primary"  v-if='scope.row.sysUserId=="" ||  scope.row.sysUserId ==null' @click="addAdmin(scope.$index,scope.row)">新增管理</el-button>
				<el-button size="small" type="warning"  v-if='scope.row.sysUserId!="" &&  scope.row.sysUserId !=null' @click="editAdmin(scope.$index,scope.row)">修改管理</el-button> -->
                <el-button size="small" type="danger" @click="deleteRow(scope.$index, scope.row)">删除</el-button>
				</template>
			</el-table-column>
		</el-table>

		<el-pagination
		 	class="pull-right clearfix"
			@current-change="handleCurrentChange"
			:current-page="currentPage"
			:page-size="totalsize" 
			layout="total, prev, pager, next, jumper"
			:total="total" 
		>
		</el-pagination>

		 <el-dialog   :title="formtitle" :visible.sync="dialogFormVisible" >
			<el-form ref="form" :model="form" label-width="100px" @submit.prevent="onSubmit" style="margin:20px;">
				<!-- <el-form-item label="广告编号">
					<el-input v-model="form.communityNo"></el-input>
				</el-form-item> -->
				<el-form-item label="标题">
					<el-input v-model="form.title"></el-input>
				</el-form-item>
			
				<el-form-item label="地址">
					<el-input v-model="form.img"></el-input>
				</el-form-item>

				<el-form-item label="起始时间点">
					<!-- <el-input type="number" v-model="form.startCount"></el-input> -->

					 <el-select v-model="form.startCount" placeholder="请选择">
						<el-option
						v-for="item in options1"
						:key="item.value"
						:label="item.label"
						:value="item.value"
						>
						</el-option>
					</el-select>
				</el-form-item>
				<el-form-item label="结束时间点">
					<!-- <el-input type="number" v-model="form.stopCount"></el-input> -->
					<el-select v-model="form.stopCount" placeholder="请选择">
						<el-option
						v-for="item in options2"
						:key="item.value"
						:label="item.label"
						:value="item.value"
						>
						</el-option>
					</el-select>
				</el-form-item>

				<el-form-item label="有效时间" v-if='showUpdate =="0"'>
					<!-- <el-input type="date" v-model=""></el-input> -->
					<el-date-picker
						v-model="form.stopTime"
						align="right"
						type="date"
						placeholder="选择日期"
						>
					</el-date-picker>
				</el-form-item>

				<el-form-item label="状态">
					<el-radio-group v-model="form.state">
						<el-radio label="10">正常</el-radio>
						<el-radio label="20">禁用</el-radio>
					</el-radio-group>
				</el-form-item>

			</el-form>
			<div slot="footer" class="dialog-footer">
				<el-button @click="dialogFormVisible = false">取 消</el-button>
				<el-button type="primary" @click="open2()">确 定</el-button>
			</div>
        </el-dialog>
		
	
		

		


  </div>
</template>
<script>
	//2
	import { RequestPost } from '../../api/api';
    import { RequestGet } from '../../api/api';
	import { url } from '../../api/api';
	import {timeFormat} from '../../api/format';
	import {dateFormat} from '../../api/format';
	import {state} from '../../api/format';
	import { PageSize } from '../../api/api';
	import moment from 'moment';
  	export default {
	  
    methods: {
	  dateFormat,	
	  timeFormat,
	  state,	
      handleSizeChange(val) {
		console.log(`每页 ${val} 条`);
		

      },
      handleCurrentChange(val) {
		console.log(`当前页: ${val}`);
		this.pageNumber = val;

		
		this.page.pageNumber = val;
		/**
		 * 1.get 请求传参数，必须是json前面加一个params
		 * 2.post传参数不需要在json对象传值前面带params
		 */
		this.loadData();


	  },
	  deleteRow(index, rows) {
       this.$confirm('是否确认删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
           
						
						RequestPost("/banner/delete",rows).then(response => {
							
							if(response.code=='0000'){
								this.$message({
									message: response.message,
									type: 'success'
								});  
								this.dialogFormVisible = false;
							}else{
								this.$message({
									message: response.message,
									type: 'error'
								});
							}
							this.dialogFormVisible = false;   
							this.loadData();
						}).catch(error => {
						this.$router.push({ path: '/login' });
						})
            this.dialogFormVisible = false;
            
            }).catch(() => {
            this.$message({
                type: 'info',
                message: '已取消删除'
            });          
            });
      },
      edit(index,rows){
        this.dialogFormVisible = true;   
				this.form = rows;
				this.formtitle ="修改广告";
				this.showUpdate = 1;
				this.loadCity(this.form.provinceCode);
				this.loadArea(this.form.cityCode);
		//alert(this.form.province+"city"+this.form.city+"province"+this.form.area);
		// this.selectProvince(this.form.provinceCode);
		// this.selectCity(this.form.cityCode);
		// this.selectArea(this.form.areaCode);
		
		//console.log(this.form);
	  },
	  editWork(index,rows){
		
        this.dialogFormWorkVisible = true;   
		this.form2 = rows;
			
	  },
    add(){
		this.dialogFormVisible = true;
		this.showUpdate = 0;
		
		this.formtitle ="新增广告";   
		this.form = {
			state:"10",
			communityId:sessionStorage.getItem("communityId"),
			startCount:0,
			stopCount:0,
			stopTime: new Date(),
			title:"",
			img:""			
		};
        
      },
      getUsers(){

      },
      open2() {
		  	if(this.formtitle =="新增广告"){
					  
				 
				  if(this.validate1() == false){
					  return;
				  }
				  this.$confirm('新增广告, 是否继续?', '提示', {
					confirmButtonText: '确定',
					cancelButtonText: '取消',
					type: 'warning'
					}).then(() => {	

						
						//return;
						RequestPost("/banner/add",this.form).then(response => {
							
							if(response.code=='0000'){
								this.$message({
									message: response.message,
									type: 'success'
								});  
								this.dialogFormVisible = false;
							}else{
								this.$message({
									message: response.message,
									type: 'error'
								});
							}
							this.loadData();
						}).catch(error => {
						this.$router.push({ path: '/login' });
						})


					
					
					}).catch(() => {
							// this.$message({
							// 	type: 'info',
							// 	message: '已取消修改'
							// });          
					});

			}else{
				//  if(this.validate1() == false){
				// 	  return;
				//   }
				this.$confirm('修改广告, 是否继续?', '提示', {
				confirmButtonText: '确定',
				cancelButtonText: '取消',
				type: 'warning'
				}).then(() => {	
					RequestPost("/banner/update",this.form).then(response => {
						
						//this.logining = false; 
						if(response.code=='0000'){
							this.$message({
								message: response.message,
								type: 'success'
							});  
							this.dialogFormVisible = false;
						}else{
							this.$message({
								message: response.message,
								type: 'error'
							});
						}
						this.loadData();
					}).catch(error => {
					this.$router.push({ path: '/login' });
					})


				
				
				}).catch(() => {
						this.$message({
							type: 'info',
							message: '已取消修改'
						});          
				});

			}
			this.loadData();
		
            
	  },

	  	//新增管理保存 
	   openAdmin() {
		  	if(this.formAdmintitle =="添加管理账号"){
				  if(this.valuidate2() ==false){
					  return;
				  }
				  this.$confirm('添加管理账号, 是否继续?', '提示', {
					confirmButtonText: '确定',
					cancelButtonText: '取消',
					type: 'warning'
					}).then(() => {	
						RequestPost("/community/addUser",this.form1).then(response => {
							
							
							if(response.code=='0000'){
								this.$message({
									message: response.message,
									type: 'success'
								});  
								this.dialogFormAdminVisible = false;
							}else{
								this.$message({
									message: response.message,
									type: 'error'
								});
							}
							this.loadData();
						}).catch(error => {
						this.$router.push({ path: '/login' });
						})


					
						this.loadData();
					}).catch(() => {
							this.$message({
								type: 'info',
								message: '已取消修改'
							});          
					});

			}else{ 

				if(this.form1.password.trim()=="" ||this.form1.password==null){
					this.$message({
						type: 'error',
						message: '密码不能为空'
					}); 
					return;
				}	

				this.$confirm('修改管理账号, 是否继续?', '提示', {
				confirmButtonText: '确定',
				cancelButtonText: '取消',
				type: 'warning'
				}).then(() => {	
					RequestPost("/community/updatePw",this.form1).then(response => {
						
						//this.logining = false; 
						if(response.code=='0000'){
							this.$message({
								message: response.message,
								type: 'success'
							});  
							this.dialogFormAdminVisible = false;
							this.dialogFormAdminUpdateVisible =false; 
						}else{
							this.$message({
								message: response.message,
								type: 'error'
							});
						}
						this.loadData();
					}).catch(error => {
					this.$router.push({ path: '/login' });
					})


				
				
				}).catch(() => {
						this.$message({
							type: 'info',
							message: '已取消修改'
						});          
				});

			}
			this.loadData();
		
            
	  },

	/**
    * 加载省份
    */
   //二
    loadProvince(){
		var page = {
			 parentCode:'100000'
		}


		
		this.$axios.get(url+'/community/findCitys', {params:page}).then(response => {
			 			if(response.data.code == '0000'){
								this.provinces = response.data.data;
								//console.log(this.provinces);
								for(let opt in this.provinces){
									if(!this.form.provinceCode){
										this.form.provinceCode = this.provinces[opt].code;
										this.form.province = this.provinces[opt].name;
										//alert("province"+this.form.province +this.form.provinceCode);
										this.loadCity(this.form.provinceCode);
									}
									break;
								}
								
						 }
                 
                }).catch(error => {
                  console.log(error)
        })	


    },

    /**
    * 加载城市
    */
   //三
    loadCity(provinceCode){

		//alert("form"+ this.form.province+this.form.provinceCode);
		var data = {
		
                parentCode:provinceCode
            
		}
		this.$axios.get(url+'/community/findCitys', {params:data}).then(response => {
			 			if(response.data.code == '0000'){
								this.citys = response.data.data;
								//console.log(this.citys);	
								for(let opt in this.citys){
								if(!this.form.cityCode){
									this.form.cityCode = this.citys[opt].code;
									this.form.city = this.citys[opt].name;
									//alert("city"+this.form.province +this.form.city+this.form.area);
									this.loadArea(this.form.cityCode);
								}
								break;
							}							
						 }
                }).catch(error => {
                  console.log(error)
        })
       
    },

    /**
    * 加载区县
    */

    //四
    loadArea(cityCode){
		var data = {
			
                parentCode:cityCode
           
		}
		
		this.$axios.get(url+'/community/findCitys', {params:data}).then(response => {
			 			if(response.data.code == '0000'){
								this.areas = response.data.data;
								//console.log(this.areas);	
								 for(let opt in this.areas){
									if(!this.form.areaCode){
										this.form.areaCode = this.areas[opt].code;
										this.form.area = this.areas[opt].name;
										//alert("area"+this.form.province +this.form.city+this.form.area);
										
									}
									break;
								}							
						 }
                }).catch(error => {
                  console.log(error)
        })
	},
	/**
    * 改变省份
	* 五
    */
    selectProvince(obj){
	//	alert(obj);
	   //this.form.province = obj;
	 for(let opt in this.provinces){
		 if(obj  ==  this.provinces[opt].code){
				 this.form.province = this.provinces[opt].shortName;
				 break;
		 }

	 }
	   //alert(this.provinces[obj.selectedIndex].name);
        this.form.cityCode=null;
		this.form.areaCode=null;
        this.loadCity(obj);
    },

    /**
    * 改变城市
	* 六
    */
    selectCity(obj){
		//this.form.city = obj;
		 for(let opt in this.citys){
			if(obj  ==  this.citys[opt].code){
					this.form.city = this.citys[opt].shortName;
					break;
			}

		}
		this.form.areaCode=null;
		this.form.area=null;
        this.loadArea(obj);
    },

    /**
    * 改变区县
	* 七
    */
    selectArea(obj){
		
		for(let opt in this.areas){
			if(obj  ==  this.areas[opt].code){
					this.form.area = this.areas[opt].shortName;
					break;
			}

		}
		//alert(this.form.area);
	},
	
	loadData(){

		RequestGet("/banner/findAll",this.page).then(response => {
						if(response.code == '0000'){
								 this.datalist = response.data;
								 this.total = response.page.totalCount; 
								 this.totalsize  = response.page.pageSize;
						 }
					
					}).catch(error => {
						 this.$router.push({ path: '/login' });
						
		})  
						
		
		//  this.$axios.get(url+'/community/findAll', {params:this.page}).then(response => {
		// 	 			if(response.data.code == '0000'){
		// 						 this.datalist = response.data.data;
		// 						 this.total = response.data.page.totalCount; 
		// 						 this.totalsize  = response.data.page.pageSize;
		// 				 }
                 
        //         }).catch(error => {
        //           console.log(error)
		// });	
	},

	addAdmin(index,rows){
        this.dialogFormAdminVisible = true;   
		
		this.formAdmintitle ="添加管理账号";
		
		this.form1 = {
            userName: '',
            loginName: '',
            password: '1q2w3e',
            mobile: '',
            roleId: '7541f8cd47ca45edb046e06dc9bb2f1a',
            roleName: '管理管理员',
            state: '10',
			communityArea:rows.id,
			code:sessionStorage.getItem("code")
        };
    },
	editAdmin(index,rows){
        this.dialogFormAdminUpdateVisible = true; 
		
	
		this.form1 = {
            userName: rows.userName,
            password: '',
            state: '10',
            id:rows.sysUserId
        };
		this.formAdmintitle ="修改管理账号";
		
	  },

	  valuidate2(){
		  if(this.form1.userName =="" ||this.form1.userName.trim() =="" || this.form1.userName == null){
				this.$message({
					type: 'error',
					message: "用户名不能为空"
				});          
				return false;
		  }
		  if(this.form1.loginName =="" ||this.form1.loginName.trim() =="" || this.form1.loginName == null){
				this.$message({
					type: 'error',
					message: "登录名不能为空"
				});          
				return false;
		  }	

		  if(this.form1.password =="" ||this.form1.password.trim() =="" || this.form1.password == null){
				this.$message({
					type: 'error',
					message: "密码不能为空"
				});          
				return false;
		  }	

		   if(this.form1.mobile =="" ||this.form1.mobile.trim() =="" || this.form1.mobile == null){
				this.$message({
					type: 'error',
					message: "手机号不能为空"
				});          
				return false;
		  }	




	  },	
	  validate1(){  //新增（修改）广告
	
		
		if(this.form.title =="" || this.form.title == null){
				//this.warningText = ;
				this.$message({
					type: 'error',
					message: "广告标题不能为空"
				});          
				return false;
		}

		if(this.form.img.trim().length =="" ){
				//this.warningText = ;
				this.$message({
					type: 'error',
					message: "广告地址不能为空"
				});          
				return false;
		}

		if((this.form.startCount+"").trim().length =="" ){
		
				this.$message({
					type: 'error',
					message: "起始时间点不能为空"
				});          
				return false;
		}

		if((this.form.stopCount+"").trim().length =="" ){
				//this.warningText = ;
				this.$message({
					type: 'error',
					message: "结束时间点不能为空"
				});          
				return false;
		}

		if((this.form.stopTime+"").trim().length =="" ){
				//this.warningText = ;
				this.$message({
					type: 'error',
					message: "有效时间不能为空"
				});          
				return false;
		}


		if((this.form.stopTime+"") =="underfined"){
				//this.warningText = ;
				this.$message({
					type: 'error',
					message: "有效时间不能为空"
				});          
				return false;
		}
	

	},

	},
	
	//1
	created:function(){
		//3
		this.loadData();
		
		//一
		this.loadProvince();
	},
    data() {
      return {
		total:0,     //数据的总数量
		totalsize:0,  //总的页数 = 总数量/每页显示的条数
		currentPage:1,
		showUpdate:0,
		page:{
			pageSize:PageSize,   //一页显示的条数
			communityId:sessionStorage.getItem("communityId")
		},
		datalist:[],
		
		listLoading: false,
		form:{	state:"10",
			communityId:sessionStorage.getItem("communityId"),
			startCount:0,
			stopCount:0,
			stopTime: new Date(),
			title:"",
			img:""			},
		form1:{},
		form2:{},
		dialogFormVisible:false,
		provinces:[], //获取省份
		citys:[], //获取市
		areas:[], //获取区
		formtitle:"",
		formAdmintitle:"",
		dialogFormAdminVisible:false,//管理
		dialogFormAdminUpdateVisible:false,
		dialogFormWorkVisible:false,  //工程商
		isEditUser:false, //是否禁用
		warningText:"", //警告语
		options1: [{
          value: '0',
          label: '0'
        }, {
          value: '1',
          label: '1'
        }, {
          value: '2',
          label: '2'
        }, {
          value: '3',
          label: '3'
        }, {
          value: '4',
          label: '4'
        },{
          value: '5',
          label: '5'
        }, {
          value: '6',
          label: '6'
        }, {
          value: '7',
          label: '7'
        }, {
          value: '8',
          label: '8'
        }, {
          value: '9',
          label: '9'
        },{
          value: '10',
          label: '10'
        }, {
          value: '11',
          label: '11'
        }, {
          value: '12',
          label: '12'
        }, {
          value: '13',
          label: '13'
        }, {
          value: '14',
          label: '14'
        },{
          value: '15',
          label: '15'
        }, {
          value: '16',
          label: '16'
        }, {
          value: '17',
          label: '17'
        }, {
          value: '18',
          label: '18'
        }, {
          value: '19',
          label: '19'
        },{
          value: '20',
          label: '20'
        }, {
          value: '21',
          label: '21'
        }, {
          value: '22',
          label: '22'
        }, {
          value: '23',
          label: '23'
        }, {
          value: '24',
          label: '24'
		}],
		options2: [{
          value: '0',
          label: '0'
        }, {
          value: '1',
          label: '1'
        }, {
          value: '2',
          label: '2'
        }, {
          value: '3',
          label: '3'
        }, {
          value: '4',
          label: '4'
        },{
          value: '5',
          label: '5'
        }, {
          value: '6',
          label: '6'
        }, {
          value: '7',
          label: '7'
        }, {
          value: '8',
          label: '8'
        }, {
          value: '9',
          label: '9'
        },{
          value: '10',
          label: '10'
        }, {
          value: '11',
          label: '11'
        }, {
          value: '12',
          label: '12'
        }, {
          value: '13',
          label: '13'
        }, {
          value: '14',
          label: '14'
        },{
          value: '15',
          label: '15'
        }, {
          value: '16',
          label: '16'
        }, {
          value: '17',
          label: '17'
        }, {
          value: '18',
          label: '18'
        }, {
          value: '19',
          label: '19'
        },{
          value: '20',
          label: '20'
        }, {
          value: '21',
          label: '21'
        }, {
          value: '22',
          label: '22'
        }, {
          value: '23',
          label: '23'
        }, {
          value: '24',
          label: '24'
        }],


      };
    }
  }
</script>
<style>
	.el-dialog--small {
		 width: 30%; 
	}
</style>
