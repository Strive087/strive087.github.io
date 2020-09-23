---
layout: post
title: html5陀螺仪
date: 2020-06-11 07:05:49
tags: [javascript, html, html5]
categories: [html]
---

deviceorientation : 设备的物理方向，表示为一系列的本地坐标系旋角。
devicemotion : 提供设备的重力加速信息。
compassneedscalibration : 罗盘校准。
{% codeblock lang:html %}

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>html5陀螺仪</title>
    <link rel="stylesheet" href="http://at.alicdn.com/t/font_1872916_9bxpvrr3gw.css">
    <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
</head>
<body>
    <h1 style="text-align: center;">摇一摇有惊喜！</h1>
    <div align = "center">
        <img src="https://pigbro.online:9608/images/surprise/1.jpg" alt="surprise"> 
    </div>
    <script>
        //获取手机屏幕宽度
        var deviceWidth = document.documentElement.clientWidth;
        var speed = 30;
        var picture = 1;
        var x=y=z=lastx=lasty=lastz=0;
        $('img').width(deviceWidth*0.9);
        var flag = true;
        if (window.DeviceOrientationEvent) {
            window.addEventListener('deviceorientation', (event) => {
                var x = event.beta;
                var y = event.gamma;
                var z = event.alpha;
                if((Math.abs(x-lastx)>speed || Math.abs(y-lasty)>speed || Math.abs(z-lastz)>speed) && flag){
                    if(++picture > 7){
                        picture = 1;
                    }
                    $('img').attr('src','https://pigbro.online:9608/images/surprise/'+picture+'.jpg');
                    flag = false;
                }
                lastx = x;
                lasty = y;
                lastz = z;
            });
            wi
        }
        $('img').on('load', function() {
            setTimeout(function(){
                flag = true;
            },1000);
        });
    </script>
</body>
</html>
{% endcodeblock %}
