<template>
        <!--地图容器-->
       <div class="box">
            <div id="boxMap"></div>
        </div>
</template>
<script>

    import { routeUrl } from '../../api/api';
    export default {
        name:'',
        data () {
            return {
                
            }
        },
        mounted:function(){
             
                
                    var map = new BMap.Map("boxMap"); // 创建Map实例  
                    var point = new BMap.Point(113.312213, 23.147267); //地图中心点，广州市  
                    map.centerAndZoom(point, 9); // 初始化地图,设置中心点坐标和地图级别。  
                    map.enableScrollWheelZoom(true); //启用滚轮放大缩小  
                    //向地图中添加缩放控件  
                    var ctrlNav = new window.BMap.NavigationControl({  
                        anchor: BMAP_ANCHOR_TOP_LEFT,  
                        type: BMAP_NAVIGATION_CONTROL_LARGE  
                    });  
                    map.addControl(ctrlNav);  
  
                    //向地图中添加缩略图控件  
                    var ctrlOve = new window.BMap.OverviewMapControl({  
                        anchor: BMAP_ANCHOR_BOTTOM_RIGHT,  
                        isOpen: 1  
                    });  
                    map.addControl(ctrlOve);  
  
                    //向地图中添加比例尺控件  
                    var ctrlSca = new window.BMap.ScaleControl({  
                        anchor: BMAP_ANCHOR_BOTTOM_LEFT  
                    });  
                    map.addControl(ctrlSca);  
  
                    var point = new Array(); //存放标注点经纬信息的数组  
                    var marker = new Array(); //存放标注点对象的数组  
                    var info = new Array(); //存放提示信息窗口对象的数组  
                   
                
                
                    map.addEventListener('click', function (e) {
                        // _this.location.lng = parseFloat(e.point.lng).toFixed(3);
                        // _this.location.lat = parseFloat(e.point.lat).toFixed(3);
                        alert(parseFloat(e.point.lng).toFixed(3)+","+parseFloat(e.point.lat).toFixed(3));
                        window.opener=null;
                        window.open('','_self');
                        window.close();
                                                
                    })

              

                       
              
    

        },
        methods: {
               hreftwo(){
                   
                    this.$router.push({ path:'/equipmentMap'  })

                }
                
        },  
        created:function(){
		//3
		          
		
	
	    },
   
    
    }
</script>


<style scoped>
    html,body,.XSDFXPage{
            width: 100%;
            height: 100%;
            overflow: hidden;
            margin: 0;
            font-family: "微软雅黑";
        }
        .box{
            width: 100%;
            min-height: 800px;
        }
        #boxMap {
            width: 100%;
            min-height: 800px;
        }
</style>