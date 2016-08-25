# MyJob
/**
 * Created by hxsd on 2016/6/28.
 */
var my_tools={
    //代替window.onload
    "documentReady":function(fn){
    if(document.addEventListener)document.addEventListener('DOMContentLoaded',fn,false);
    else{
        document.attachEvent('onreadystatechange',function(){
            if(document.readyState=='complete'){
                fn && fn();
                 }
             });
        }
},

    //鼠标拖拽
    'moveBox':function(elm,en){
    var handle;
    en? handle=en:handle=elm;//判断一级还是二级
    //鼠标点击拖拽
    handle.onmousedown=function(ev){
        var oEv=ev ||window.event;
        var disX=oEv.clientX-elm.offsetLeft;
        var disY=oEv.clientY-elm.offsetTop;
        //鼠标移动事件
        document.onmousemove=function(ev){
            var oEv=ev ||window.event;
            var b_w=document.documentElement.clientWidth;//读取屏幕宽度
            var b_h=document.documentElement.clientHeight;//读取屏幕高度
            var l=oEv.clientX-disX;
            var t=oEv.clientY-disY;
            if(l<0)l=0;
            if(t<0)t=0;
            if(l>b_w-elm.offsetWidth)l=b_w-elm.offsetWidth;
            if(t>b_h-elm.offsetHeight)t=b_h-elm.offsetHeight;
            elm.style.left=l+"px";
            elm.style.top=t+"px";
        };
        return false;
    };
    //鼠标放开事件
    document.onmouseup=function(){
        document.onmousemove=null;//事件为空
    }
},



    //事件定义
    "runMove":function(){
    modal_layer(oModal);//调用模态层事件
    var oModal=document.getElementById('modal_layer');//模态层

    boxMove(oDiv);//调用创建登录框事件
    var oDiv=document.getElementById('box');

    var closeBtn=document.getElementById('close_btn');//关闭按钮
    var tittle=oDiv.getElementsByTagName('h2')[0];//标题
    var input=document.getElementsByTagName('input');

    input[0].onmousedown=function(ev){//输入框阻止冒泡，可以输入文字
        var oEv=ev ||window.event;
        oEv.cancelBubble=true;
    };
    input[1].onmousedown=function(ev){//输入框阻止冒泡，可以输入文字
        var oEv=ev ||window.event;
        oEv.cancelBubble=true;
    };

    oModal.style.display='block';
    oModal.style.height=document.documentElement.clientHeight+"px";

    oppBox(oDiv);//调用居中事件
    moveBox(oDiv,tittle);//调用鼠标拖拽事件

    //关闭弹框
    closeBtn.onclick=function(){
        document.body.removeChild(oModal);
        document.body.removeChild(oDiv);
    }
},

    //逐字读出(要配合setInterval(move,1000);)
    "move":function(txt){
    if(n<=txt.length){
        var new_txt=txt.slice(0,n);//读出0-n个字符
        document.body.innerHTML=new_txt;//屏幕输出内容
    }
    n++;//逐字读出++
},



    //getStyle读取数据
    "getStyle":function(obj,name){
    var value=obj.currentStyle? obj.currentStyle[name]:getComputedStyle(obj,false)[name];
    if(name=='opacity'){
        value=Math.round(parseInt(value)*100);
    }
    else{
        value=parseInt(value);
    }
    return value;
},

    //运动框架 修改样式height、width等数据，逐步增加 配合【//getStyle读取数据】使用
    "nav_list":function hxsd_move(obj,modeJson,stopTime){
    var speed={
        'veryslow':5000,
        'slow':2000,
        'normal':1000,
        'fast':700,
        'veryfast':300
    };
    //如果不输入速度 按照默认速度，如果输入文字‘fast’ speed.fast
    if(stopTime){
        if(typeof stopTime=="string"){//是否输入的是字符串
            stopTime=speed[stopTime];
        }
    }else{
        stopTime=speed.normal;
    }

    var start={};
    var dis={};

    //循环读取modeJson的值
    for(var key in modeJson){
        //开始距离
        start[key]=getStyle(obj,key);
        //移动的距离
        dis[key]=modeJson[key]-start[key];
    }

    //把时间分成30份 取整
    var count=parseInt(stopTime/30);
    var n=0;
    var timer=null;
    clearInterval(obj.timer);
    obj.timer=setInterval(function(){
        n++;

        for(var key in modeJson){
            var a=1-n/count;//减速运动
            //步进长度  起点+ 距离/份数*n,每步的行走的长度
            var step_dis=start[key]+dis[key]*(1-a*a*a);

            if(key=='opacity'){
                obj.style.opacity=step_dis/100;
                obj.style.filter='alpha(opacity):'+step_dis+')';
            }
            else{
                obj.style[key]=step_dis+'px';
            }
        }

        if(n==count){
            clearInterval(obj.timer);
        }
    },30)
}
// 用法 move(oUL,{'height':500,'width':500},2000);
};
