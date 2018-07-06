<template>
    <div>
        <span>弹性布局实现左右固定，中间自适应</span>
        <div class="flexLayout">
            <div class="center">center</div>
            <div class="left">left</div>
            <div class="right">right</div>
        </div>
        <span>绝对定位实现左右固定，中间自适应</span>
        <div class="absolutLayout">
            <div class="center1">center</div>
            <div class="left1">left</div>
            <div class="right1">right</div>
        </div>
        <span>圣杯布局实现左右固定，中间自适应</span>
        <div class="shengbeiLayout clear">
            <div class="center2">center</div>
            <div class="left2">left</div>
            <div class="right2">right</div>
        </div>  
         <span>双飞翼布局实现左右固定，中间自适应</span>
        <div class="shuangfeiyiLayout clear1">
            <div class="out-center3">
                <div class="center3">center</div>
            </div>         
            <div class="left3">left</div>
            <div class="right3">right</div>
        </div>
    </div>
</template>
<script>
export default {
    
}
</script>
<style scoped>
.flexLayout{
    display: flex;
    min-height: 130px
}
.center{
    flex-grow:1;
    background-color: aqua
}
.left{
    order:-1;
    flex-basis: 200px;
    background-color: blue
}
.right{
    flex-basis: 300px;
    background-color: rebeccapurple
}

.absolutLayout{
    position: relative;
}
.center1, .right1, .left1{
    top:0;
    height: 130px
}
.center1{
    background-color: aqua;
    margin:0px 300px 0px 200px
}
.left1{
    position:absolute;
    left: 0;
    background-color: blue;
    width:200px
}
.right1{
    position:absolute;
    right: 0;
    background-color: rebeccapurple;
    width:300px
}

.shengbeiLayout{
    /* 如果这里没有给父元素设置高度而且又没有清除浮动的话父元素会出现高度塌陷 */
    min-height: 130px;
    padding:0px 300px 0px 200px
}
.center2, .right2, .left2{
    float: left;
    position: relative;
    min-height: 130px
}
.center2{
    min-height: 130px;
    width:100%;
    background-color: aqua;
}
.left2{
    margin-left: -100%;
    left:-200px;
    background-color: blue;
    width:200px
}
.right2{
    margin-left: -300px;
    right: -300px;
    background-color: rebeccapurple;
    width:300px
}
.clear:after{
    content: "." ;
    display: block;
    height: 0;
    visibility: hidden;
    clear: both;
}

.out-center3{
    width:100%;
    background-color: aqua;
}   
.out-center3, .right3, .left3{
    float: left;
    min-height: 130px
}
.center3{
    padding:0px 300px 0px 200px;
}
.left3{
    margin-left: -100%;
    background-color: blue;
    width:200px
}
.right3{
    margin-left: -300px;
    background-color: rebeccapurple;
    width:300px
}
.clear1:after{
    content: "." ;
    display: block;
    height: 0;
    visibility: hidden;
    clear: both;
}
</style>
