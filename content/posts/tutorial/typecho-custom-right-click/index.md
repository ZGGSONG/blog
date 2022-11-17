---
title: Typecho 网站自定义右键
date: 2020-07-23T03:17:00+08:00
lastmod: 2020-07-25T09:38:48+08:00
description: ""
featuredImage: ""
categories:
- tutorial
tags:
- typecho
---

简单来说就是花里胡哨，没别的意思，看着喜欢就拿去

## 效果

![自定义右键](https://cdn.zggsong.cn/2020/07/23/3195f7441a050.png!webp)

## 使用

配合弹窗提醒食用更佳-引入`layer.js`，详情查看 [网站添加复制提醒效果一](https://www.zggsong.cn/archives/copy_reminder.html#menu_index_2)

依次进入`控制台 `-`外观`-`设置外观`-`主题自定义扩展`，将以下代码加入到`自定义 HTML 元素拓展`-`在 body 标签结束前`，部分内容自行修改即可

```
<!-- 自定义右键 -->
<style type="text/css">
    a {text-decoration: none;}
    div.usercm{background-repeat:no-repeat;background-position:center center;background-size:cover;background-color:#fff;font-size:13px!important;width:130px;-moz-box-shadow:1px 1px 3px rgba
(0,0,0,.3);box-shadow:0px 0px 15px #333;position:absolute;display:none;z-index:10000;opacity:0.9;border-radius: 5px;}
    div.usercm ul{list-style-type:none;list-style-position:outside;margin:0px;padding:0px;display:block}
    div.usercm ul li{margin:0px;padding:0px;line-height:35px;}
    div.usercm ul li a{color:#666;padding:0 15px;display:block}
    div.usercm ul li a:hover{color:#fff;background:rgba(9,145,113,0.88);border-radius: 5px}
    div.usercm ul li a i{margin-right:10px}
    a.disabled{color:#c8c8c8!important;cursor:not-allowed}
    a.disabled:hover{background-color:rgba(255,11,11,0)!important}
    div.usercm{background:#fff !important;}
    </style>
<div class="usercm" style="left: 199px; top: 5px; display: none;">
    <ul>
        <li><a href="https://www.zggsong.cn/"><i class="fa fa-home fa-fw"></i><span>首页</span></a></li>
        <li><a href="javascript:void(0);" onclick="getSelect();"><i class="fa fa-file fa-fw"></i><span>复制</span></a></li>
        <li><a href="javascript:void(0);" onclick="baiduSearch();"><i class="fa fa-search fa-fw"></i><span>百度</span></a></li>
        <li><a href="javascript:void(0);" onclick="googleSearch();"><i class="fa fa-google fa-fw"></i><span>谷歌</span></a></li>
        <li><a href="javascript:history.go(1);"><i class="fa fa-arrow-right fa-fw"></i><span>前进</span></a></li>
        <li><a href="javascript:history.go(-1);"><i class="fa fa-arrow-left fa-fw"></i><span>后退</span></a></li>
        <li style="border-bottom:1px solid gray"><a href="javascript:window.location.reload();"><i class="fa fa-refresh fa-fw"></i><span>刷新</span></a></li>
        <li><a href="https://www.zggsong.cn/links.html"><i class="fa fa-user fa-fw"></i><span>和我当邻居</span></a></li>  
           <li><a href="https://www.zggsong.cn/about.html"><i class="fa fa-pencil fa-fw"></i><span>给我留言吧</span></a></li>
    </ul>
</div>
<script type="text/javascript">
    (function(a) {
        a.extend({
            mouseMoveShow: function(b) {
                var d = 0,
                    c = 0,
                    h = 0,
                    k = 0,
                    e = 0,
                    f = 0;
                a(window).mousemove(function(g) {
                    d = a(window).width();
                    c = a(window).height();
                    h = g.clientX;
                    k = g.clientY;
                    e = g.pageX;
                    f = g.pageY;
                    h + a(b).width() >= d && (e = e - a(b).width() - 5);
                    k + a(b).height() >= c && (f = f - a(b).height() - 5);
                    a("html").on({
                        contextmenu: function(c) {
                            3 == c.which && a(b).css({
                                left: e,
                                top: f
                            }).show()
                        },
                        click: function() {
                            a(b).hide()
                        }
                    })
                })
            },
            disabledContextMenu: function() {
                window.oncontextmenu = function() {
                    return !1
                }
            }
        })
    })(jQuery);
     
    function getSelect() {
        "" == (window.getSelection ? window.getSelection() : document.selection.createRange().text) ? layer.msg("请选择需要复制的内容！") : document.execCommand("Copy")
    }
    function baiduSearch() {
        var a = window.getSelection ? window.getSelection() : document.selection.createRange().text;
        "" == a ? layer.msg("请选择需要百度的内容！") : window.open("https://www.baidu.com/s?wd=" + a)
    }
    function googleSearch() {
        var a = window.getSelection ? window.getSelection() : document.selection.createRange().text;
        "" == a ? layer.msg("请选择需要谷歌的内容！") : window.open("https://www.google.com/search?q=" + a)
    }
    $(function() {
        for (var a = navigator.userAgent, b = "Android;iPhone;SymbianOS;Windows Phone;iPad;iPod".split(";"), d = !0, c = 0; c < b.length; c++) if (0 < a.indexOf(b[c])) {
            d = !1;
            break
        }
        d && ($.mouseMoveShow(".usercm"), $.disabledContextMenu())
    });
    </script>
```