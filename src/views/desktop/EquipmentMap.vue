<template> 
 <div class="block">   
	 	<el-col :span="20" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true">
				<el-form-item style="width:300px">
					<el-input v-model="page.criteria"  @keyup.enter.native="query"  placeholder="请输入设备[编号|名称]" style="width:300px"></el-input>
				</el-form-item>
				<el-form-item  >
					<el-button type="primary" v-on:click="query">查询</el-button>
				</el-form-item>
                <el-form-item  >
					<el-button type="success" v-on:click="query">刷新</el-button>
				</el-form-item>
				
				
			</el-form>
		</el-col>
        <el-col :span="4" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true">
				
				<el-form-item  style="text-align:right">
					<el-button type="primary" @click="add()">新增</el-button>
				</el-form-item>
			</el-form>
		</el-col>
      
        
		<el-table :data="datalist" highlight-current-row v-loading="listLoading" style="width: 100%;">
		
			
			<el-table-column prop="equNo" label="设备编号" width="200" sortable>
			</el-table-column>
			<el-table-column prop="equipmentName" label="设备名称" width="200" sortable>
			</el-table-column>
			<el-table-column prop="communityName" label="所属小区" width="200" sortable>
			</el-table-column>
			<!-- <el-table-column prop="buildingName" label="楼栋" width="150" sortable>
			</el-table-column> -->
            <el-table-column prop="equId" label="序列号" width="200" sortable>
			</el-table-column>
			<!-- <el-table-column  label="单元" min-width="150" sortable>
                            <template slot-scope="scope">{{scope.row.unitName}} <span v-if="scope.row.unitName!='' && scope.row.unitName!=null ">单元</span></template>    
			</el-table-column> -->
			<el-table-column  label="状态" min-width="120">
				<template slot-scope="scope">{{ state(scope.row.state)}}</template>
			</el-table-column>
            <el-table-column  label="最新设备状态" min-width="150">
                
				<template slot-scope="scope"><span class="span" v-if="scope.row.nowDateStatus=='离线'"><i class="tip"></i></span> <span class="span" v-if="scope.row.nowDateStatus=='在线'"><i class="tip" style="background:green;"></i></span>{{scope.row.nowDateStatus}}</template>
			</el-table-column>
            <!-- <el-table-column  label="最新设备状态" min-width="150">
                
				<template slot-scope="scope"><span class="span" v-if="scope.row.nowDateStatus=='离线'"><i class="tip"></i></span> <span class="span" v-if="scope.row.nowDateStatus=='在线'"><i class="tip" style="background:green;"></i></span>{{scope.row.nowDateStatus}}</template>
			</el-table-column>
            <el-table-column  label="最新门磁状态" min-width="150">
				<template slot-scope="scope"><span class="span" v-if="scope.row.nowState=='异常'"><i class="tip"></i></span> <span class="span" v-if="scope.row.nowState=='正常'"><i class="tip" style="background:green;"></i></span>{{scope.row.nowState}}</template>
			</el-table-column> -->
			
			
			<el-table-column label="操作" min-width="250">
				<template scope="scope">
				<!-- <el-button size="small" type="primary"  @click="edit(scope.$index,scope.row)">编辑</el-button>
				<el-button size="small" type="primary"  v-if='scope.row.sysUserId=="" ||  scope.row.sysUserId ==null' @click="addAdmin(scope.$index,scope.row)">新增物业</el-button>
				<el-button size="small" type="warning"  v-if='scope.row.sysUserId!="" ||  scope.row.sysUserId !=null' @click="editAdmin(scope.$index,scope.row)">修改物业</el-button> -->
                <el-button size="small" type="danger" v-if="false" @click="deleteRow(scope.$index, scope.row)">删除</el-button>
                <el-button size="small" type="primary" @click="updateRow(scope.$index, scope.row)">设备详情</el-button>
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
			<el-form ref="subData" :model="subData" label-width="100px" @submit.prevent="onSubmit" style="margin:20px;">
                    
                    <!-- <el-form-item label="设备类型">
                       <el-select  v-model="subData.equipmentType" placeholder="请选择设备类型" @change="selectEquType(subData.equipmentType)">
                           <el-option :label="item.label" :key="item.value" :value="item.value" v-for="item in options">{{item.label}}</el-option>
					    </el-select>
                    </el-form-item>  -->

                       

                      <el-form-item label="*设备编号">
                        <el-input v-model="subData.equNo" type="number"  placeholder="请输入6位设备编号"></el-input>
                    </el-form-item>
                    <el-form-item label="*设备名称">
                        <el-input v-model="subData.equipmentName"  placeholder="请输入设备名称"></el-input>
                    </el-form-item>
                    
                    <el-form-item label="所属小区">
                        <el-select  v-model="subData.communityId" placeholder="请选择设备类型"    @change="selectCommunity(subData.communityId)">
                           <el-option :label="item.name" :key="item.value" :value="item.value" v-for="item in communitys">{{item.name}}</el-option>
					    </el-select>
                    </el-form-item>
                   
                    <!-- <el-form-item label="*设备序列号">
                        <el-input  v-model="subData.equId"  placeholder="请输入设备序列号后8位"></el-input>
                    </el-form-item> -->
                    <el-form-item label="状态">
					<el-radio-group v-model="subData.state">
						<el-radio label="10">正常</el-radio>
						<el-radio label="20">禁用</el-radio>
					</el-radio-group>
				</el-form-item>
			</el-form>	
			<div slot="footer" class="dialog-footer">
				<el-button @click="dialogFormVisible = false">取 消</el-button>
				<el-button type="primary" @click="open()">确 定</el-button>
			</div>
        </el-dialog>


        <el-dialog   title="设备详情" :visible.sync="updateDialogFormVisible" >
                <table border="1" style="border:0px">
				<tr>
				  <th  style="width:200px">参数名</th>
				  <th style="width:300px">参数值</th>
                  <th  style="width:200px">参数名</th>
				  <th style="width:300px">参数值</th>
				</tr>
				<tr style="text-align: left;" >
					<td>供电单元</td>
					<td id="value1">{{cc[0]}}</td>
				
					<td>主电</td>
					<td id="value2">{{cc[1]}}</td>
				</tr>
				<tr style="text-align: left;" >
					<td>备电</td>
					<td id="value3">{{cc[2]}}</td>
					<td>充电</td>
					<td id="value4">{{cc[3]}}</td>
				</tr>
				<tr style="text-align: left;" >
					<td>电池1</td>
					<td id="value5">{{cc[4]}}</td>
				
					<td>电池2</td>
					<td id="value6">{{cc[5]}}</td>
				</tr>
				<tr style="text-align: left;" >
					<td>电池3</td>
					<td id="value7">{{cc[6]}}</td>
				
					<td>电池4</td>
					<td id="value8">{{cc[7]}}</td>
				</tr>
				<tr style="text-align: left;" >
					<td>电流</td>
					<td id="value9">{{cc[8]}}</td>
				
					<td>环境指数</td>
					<td id="value10">{{cc[9]}}</td>
				</tr>
				<tr style="text-align: left;" >
					<td>氧气</td>
					<td id="value11">{{cc[10]}}</td>
				
					<td>CO</td>
					<td id="value12">{{cc[11]}}</td>
				</tr>
				<tr style="text-align: left;" >
					<td>CO2</td>
					<td id="value13">{{cc[12]}}</td>
				
					<td>SO2</td>
					<td id="value14">{{cc[13]}}</td>
				</tr>
				<tr style="text-align: left;" >
					<td>温度</td>
					<td id="value15">{{cc[14]}}</td>
				
					<td>湿度</td>
					<td id="value16">{{cc[15]}}</td>
				</tr>
				<tr style="text-align: left;" >
					<td>风压</td>
					<td id="value17">{{cc[16]}}</td>
				
					<td>设备状态</td>
					<td id="value18">{{cc[17]}}</td>
				</tr>
				<tr style="text-align: left;" >
					<td>烟感报警</td>
					<td id="value19">{{cc[18]}}</td>
				
					<td>风机</td>
					<td id="value20">{{cc[19]}}</td>
				</tr>
				<tr style="text-align: left;" >
					<td>喷淋电磁阀</td>
					<td id="value21">{{cc[20]}}</td>
				
					<td>报警灯</td>
					<td id="value22">{{cc[21]}}</td>
				</tr>
				<tr style="text-align: left;" >
					<td>报警提示</td>
					<td id="value23">{{cc[22]}}</td>
				
					<td>开舱开关</td>
					<td id="value24">{{cc[23]}}</td>
				</tr>
				<tr style="text-align: left;" >
					<td>关舱开关</td>
					<td id="value25">{{cc[24]}}</td>
				
					<td>系统状态提示栏</td>
					<td id="value26">{{cc[25]}}</td>
				</tr>	
			</table>

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
       /**
        * 查询
        */
        query(){
            this.loadData();
        },

      add(){
           this.dialogFormVisible = true;
		   this.formtitle ="新增设备";   
		   this.subData = {}; 
           this.communityId = ''; 
           this.buildingId = '';
           this.unitNo = '';
            this.loadCommunitys();
            this.isEdit = true;
            this.subData = {
                equipmentName: '',
                equipmentType: '10',
                state: '10',
                communityId:this.communityId,
                buildingId:this.buildingId,
                unitName:this.unitNo,
                equCode:"",
                equId:"",
                equNo:""
                
            };


      },
      open(){


          if(this.validate() == false){
              //this.subData.equId=="";
              return;
          }
          this.subData.equCode = this.subData.equipmentType =="10"?"0000000000":this.subData.equipmentType =="20"?"0000000000":"FFFF000000";
          this.subData.equId = this.communityNo+this.subData.equId;
          
          RequestPost("/equipment/add",this.subData).then(response => {
						
						//this.logining = false; 
						if(response.code=='0000'){
							this.$message({
								message: response.message,
								type: 'success'
							});  
                            this.dialogFormVisible = false;
                            //this.subData = {};
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
          
      },


      open1(){

          if(this.subData1.equipmentName == ""){
            this.$message({
                type: 'error',
                message: '请输入设备名称'
            });
              return;
          }
          if(this.subData1.newequId == "" || this.subData1.newequId.length>8){
            this.$message({
                type: 'error',
                message: '请输入设备序列号后8位'
            });
              return;
          }
         
          this.communityNo = (Array(4).join(0) +sessionStorage.getItem("code")).slice(-4);
         // alert(this.communityNo+this.subData1.equCode);  
          this.subData1.equId =this.communityNo+this.subData1.newequId;
          this.subData1.equCode = this.subData.equipmentType =="10"?"0000000000":this.subData.equipmentType =="20"?"0000000000":"FFFF000000";
          //this.subData1.equId = this.communityNo+this.subData1.equId;
          this.subData1.equCardState = "20"; //修改设备房卡关联数据状态
          RequestPost("/equipment/update",this.subData1).then(response => {
						
						
						if(response.code=='0000'){
							this.$message({
								message: response.message,
								type: 'success'
							});  
                            this.updateDialogFormVisible = false;
                        
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
          
      },
      getUsers(){},

     updateRow(index,rows){
            this.subData1 = rows;
            this.updateDialogFormVisible = true;
            this.subData1.equipmentName = rows.equipmentName;
            this.subData1.oldequId = rows.equId.substr(4,12);
            this.subData1.newequId = "";

            var a1=['供电单元','主电','备电','充电','电池1','电池2','电池3','电池4','电流','环境指数','氧气','CO','CO2','SO2','温度','湿度','风压','设备状态','烟感报警','风机','喷淋电磁阀','报警灯','报警提示','开舱开关','关舱开关','系统状态提示栏'];
            
            this.cc = [];
            this.equNo = rows.equNo;
            // RequestGet("/equipment/getRedisEqu",{equNo:rows.equNo}).then(response => {
			// 		var equParam = response.data;
            //         if(equParam!=null){
            //             if(equParam.length>0){
            //                 var aa = equParam.split(",");
            //                 for(var i= 0;i<a1.length;i++){
            //                     this.cc.push({key1:a1[i],value1:aa[i]});
            //                 }
            //             }	
            //         }
					
                        
            // }).catch(error => {
            //                 this.$router.push({ path: '/login' });
                            
            // }) 
            this.time11 = setInterval(()=>{
            this.aaa()
            },3000)
            this.aaa();
            
            
            
           


          

      },

      aaa(){


            if(this.updateDialogFormVisible==false){
                //alert("true");
                clearInterval(this.time11); 
              
            }
           var a1=['供电单元','主电','备电','充电','电池1','电池2','电池3','电池4','电流','环境指数','氧气','CO','CO2','SO2','温度','湿度','风压','设备状态','烟感报警','风机','喷淋电磁阀','报警灯','报警提示','开舱开关','关舱开关','系统状态提示栏'];
            
            this.cc = [];
            RequestGet("/equipment/getRedisEqu",{equNo:this.equNo}).then(response => {
					var equParam = response.data;
                    if(equParam!=null){
                        if(equParam.length>0){
                            var aa = equParam.split(",");
                             this.cc = aa;
                            // for(var i= 0;i<a1.length;i++){
                            //     this.cc.push({key1:a1[i],value1:aa[i]});
                               
                            // }
                        }	
                    }
					
                        
            }).catch(error => {
                            this.$router.push({ path: '/login' });
                            
            })  



      },
 
	  deleteRow(index, rows) {
       this.$confirm('确认删除, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
            this.$message({
                type: 'success',
                message: '删除功能暂时未开放!'
            });


            //删除功能暂时不开放
            // RequestPost("/equipment/delete",rows).then(response => {
						
			// 			//this.logining = false; 
            //     if(response.code=='0000'){
            //         this.$message({
            //             message: response.message,
            //             type: 'success'
            //         });  
            //         this.dialogFormVisible = false;
            //     }else{
            //         this.$message({
            //             message: response.message,
            //             type: 'error'
            //         });
            //     }
            //     this.loadData();
            // }).catch(error => {
            // this.$router.push({ path: '/login' });
            // })


            
            this.dialogFormVisible = false;
            
            }).catch(() => {
            this.$message({
                type: 'info',
                message: '已取消删除'
            });          
            });
      },
     

	
	loadData(){
        
        this.page.communityId = this.$route.query.id;
		RequestGet("/equipment/findCommunityIdAll",this.page).then(response => {
						if(response.code == '0000'){
								 this.datalist = response.data;
								 this.total = response.page.totalCount; 
								 this.totalsize  = response.page.pageSize;
						 }
					
		}).catch(error => {
						 this.$router.push({ path: '/login' });
						
		})  
        
						
	
	},

  
    
    /**
    * 选择设备类型
    */
    selectEquType(){
        if(this.subData.equipmentType === '30'){
            this.isEdit = false;
        }else{
            this.isEdit = true;
        }
    },

    // /**
    // * 选择小区
    // */
    selectCommunity(selectIdx){
        
        this.communityId = selectIdx;
        this.subData.communityId = selectIdx;
        this.buildingId = '';
        this.subData.buildingId = "";
        this.buildings = [];
        this.unitNo = '';
        this.loadBuildings();

        this.findOne(selectIdx);
    },

    //
    findOne(one){
        RequestGet("/equipment/findOne",{
                id:one
            }).then(response => {
          
						if(response.code == '0000'){
                              
                                this.communityNo = (Array(4).join(0) + response.data.code).slice(-4);
                              
    
						 }
					
        }).catch(error => {
               // this.$router.push({ path: '/login' });
						
		})  
    },

    // /**
    // * 选择楼栋
    // */
    selectBuild(selectIdx){
        //this.buildingId = this.buildings[selectIdx].value;
        this.buildingId = selectIdx;
        this.subData.buildingId = this.buildingId;
        this.subData.unitName = '';
        this.units = [];
        this.unitNo = '';
        this.loadUnits();
    },

    // /**
    // * 选择单元
    // */
    selectUnit(selectIdx){
        this.unitNo = selectIdx;
        this.subData.unitName = this.unitNo;
    },

   
    /**
    * 加载小区
    */
    loadCommunitys(){

        RequestGet("/equipment/findCommunitys").then(response => {
          
						if(response.code == '0000'){
								this.communitys = response.data;
                                
                                
						 }
					
        }).catch(error => {
                this.$router.push({ path: '/login' });
						
		})  
      
    },

    /**
    * 加载楼栋
    */
    loadBuildings(){

        RequestGet("/equipment/findBuildings",{
                communityId:this.communityId
            }).then(response => {
          
						if(response.code == '0000'){
								 this.buildings = response.data;
                                 if(this.buildings.length>0){
                                     this.subData.buildingId = this.buildings[0].value;
                                 }
                                 
                                // for(let obj in this.buildings){
                                //     this.selectBuild(0);
                                //     break;
                                // }
						 }
					
        }).catch(error => {
                this.$router.push({ path: '/login' });
						
		})  
       
    },

    // /**
    // * 加载单元
    // */
    loadUnits(){

         RequestGet("/equipment/findUnits",{
                buildingId:this.buildingId
            }).then(response => {
          
						if(response.code == '0000'){
								  this.units = response.data;
                                   if(this.units.length>0){
                                     this.subData.unitName = this.units[0].value;
                                 }
                                // for(let obj in this.buildings){
                                //     this.selectBuild(0);
                                //     break;
                                // }
						 }
					
        }).catch(error => {
                this.$router.push({ path: '/login' });
						
		})  
        
    },
    validate(){
        if(this.subData.equNo.trim()=="" || this.subData.equNo == null){
            this.$message({
                type: 'error',
                message: '设备编号不能为空'
            });         
            return false;
        }
        
        if(this.subData.equipmentName.trim()=="" || this.subData.equipmentName == null){
            this.$message({
                type: 'error',
                message: '设备名称不能为空'
            });         
            return false;
        }

        if(this.subData.communityId.trim()=="" || this.subData.communityId == null){
            this.$message({
                type: 'error',
                message: '所属小区不能为空'
            });         
            return false;
        }

        if(this.subData.equNo.trim().length>6){
            this.$message({
                type: 'error',
                message: '请输入6位设备序列号'
            });         
            return false;
        }

        


    },




	},
	//1
	created:function(){
		//3
		this.loadData();
		
	
	},
    data() {
      return {
		total:0,     //数据的总数量
		totalsize:0,  //总的页数 = 总数量/每页显示的条数
		currentPage:1,
		page:{
			pageSize:PageSize,   //一页显示的条数
            criteria:''
		},
		datalist:[],
		cc:[],
		listLoading: false,
        form:{},
        communityNo:"",
        subData:{},
        isEdit:true, //是否禁用
        dialogFormVisible:false,
        communitys:[],
        buildings:[],
        units:[],
        communityId:"",
        buildingId:"",
        unitNo:"",
        formtitle:"",
         options: [{
          value: '10',
          label: '其它'
        }, {
          value: '20',
          label: '围墙机'
        }, {
          value: '30',
          label: '单元门口机'
        }],
        updateDialogFormVisible:false,
        subData1:{newequId:""},
        time11:"",

      };
    }
  }
</script>
<style>
	.el-dialog--small {
		 width: 30%; 
	}
    .span {
        position:relative;
        padding:8px;
    }
    .tip {
        display:block;
        background:#f00;
        border-radius:50%;
        width:8px;
        height:8px;
        top:12px;
        right:0px;
        position:absolute;
    }
     .tableText{
			height: 50px;
			
    }
    
    .tableText tr td{
        text-align: center;
        border:1px solid red;
    }
</style>
