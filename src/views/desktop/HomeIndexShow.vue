<template>
        <!--地图容器-->
       <div class="box">
            <div id="boxMap"></div>
        </div>
</template>
<script>
    import { RequestPost } from '../../api/api';
    import { RequestGet } from '../../api/api';
    import { routeUrl } from '../../api/api';
    export default {
        name:'',
        data () {
            return {
                
            }
        },
        mounted:function(){


             	RequestGet("/equipment/findCommunityAll",{}).then(response => {
                    var markerArr = response.data;
                
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
                    for (var i = 0; i < markerArr.length; i++) {  
                        var bb = i;
                        var p0 = markerArr[i].point.split(",")[0]; //  
                        var p1 = markerArr[i].point.split(",")[1]; //按照原数组的point格式将地图点坐标的经纬度分别提出来  
                        point[i] = new window.BMap.Point(p0, p1); //循环生成新的地图点  
                        marker[i] = new window.BMap.Marker(point[i]); //按照地图点坐标生成标记  
                        map.addOverlay(marker[i]);  
                        marker[i].setAnimation(BMAP_ANIMATION_BOUNCE); //跳动的动画  
                        var label = new window.BMap.Label(markerArr[i].communityName, { offset: new window.BMap.Size(20, -10) });  
                        marker[i].setLabel(label);  
                       
                        info[bb] = new window.BMap.InfoWindow("<p  style=’font-size:12px;lineheight:1.8em;’>" + markerArr[bb].communityName + "</br>地址：" + markerArr[bb].address + "</br> 设备列表： <a href='"+routeUrl+"/#/equipmentMap?id="+markerArr[bb].id+"'   style='color:red'>点击查看</a></br></p>"); // 创建信息窗口对象  

                   
                   }  
                  
                    for(let j = 0;j<markerArr.length;j++){
                  
                         marker[j].addEventListener("click", function () {  
                          
                           console.log(j);
                           this.openInfoWindow(info[j]);  
                        });  
                        
                       
                    }
                   
                    // marker[1].addEventListener("mouseover", function () {  
                    //     this.openInfoWindow(info[1]);  
                    // });  
                    // marker[2].addEventListener("mouseover", function () {  
                    //     this.openInfoWindow(info[2]);  
                    // });  

                    //  marker[3].addEventListener("mouseover", function () {  
                    //     this.openInfoWindow(info[3]);  
                    // });  
                
                
                    map.addEventListener('click', function (e) {
                        // _this.location.lng = parseFloat(e.point.lng).toFixed(3);
                        // _this.location.lat = parseFloat(e.point.lat).toFixed(3);
                      //  alert(parseFloat(e.point.lng).toFixed(3)+","+parseFloat(e.point.lat).toFixed(3));
                    })

              
                                
                }).catch(error => {
                         this.$router.push({ path: '/login' });
                                    
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