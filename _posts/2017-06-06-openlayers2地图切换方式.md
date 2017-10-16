---
layout: post
title: "openlayers2地图切换方式"
date: 2017-06-03 12:00:00 +0800
description: "openlayers2地图切换方式"
categories: 其他
tag: [天地图]

---   

- 代码：
```
function init(){

    map = new OpenLayers.Map( 'map' );
    var baseLayers = GetBaseLayers();
    map.addLayers(baseLayers);

    stateBordersText = //geojson ...
    stateBordersLayer = new OpenLayers.Layer.Vector('State borders'); 
    map.addLayer(stateBordersLayer);
    stateBordersLayer.addFeatures(geojson_format.read(stateBordersText));

    // etc ...

}

function GetBaseLayers(){

    emptyBaselayer = new OpenLayers.Layer("Empty BaseLayer", {isBaseLayer: true, displayInLayerSwitcher: true}); 
    defaultBaselayer = new OpenLayers.Layer.WMS("OpenLayers Basic", "http://vmap0.tiles.osgeo.org/wms/vmap0", {layers: 'basic'});

    var mapnik = new OpenLayers.Layer.OSM();
    mapnik.transitionEffect = "resize";

    //Source: http://openlayers.org/blog/2010/07/10/google-maps-v3-for-openlayers/
    var gphy = new OpenLayers.Layer.Google("Google Physical",{type: google.maps.MapTypeId.TERRAIN});
    var gmap = new OpenLayers.Layer.Google("Google Streets",{numZoomLevels: 22});
    var gsat = new OpenLayers.Layer.Google("Google Satellite",{type: google.maps.MapTypeId.SATELLITE,sphericalMercator:true,numZoomLevels:22,layers: 'basic'});

    var baseLayers = [emptyBaselayer,defaultBaselayer,mapnik,gphy,gmap,gsat];

    return baseLayers;

}
```

## openlayers2地址
[OpenLayers 2 Library Documentation](http://docs.openlayers.org/library/index.html)

## 添加其他底图方式
openlayers2提供了各种地图的接口形式，天地图也自定义设置了属于自己的接口形式。
全国天地图可以通过藤壮函数来实现对不同类型底图的调用[Openlayers 2.X加载天地图](http://blog.csdn.net/gisshixisheng/article/details/44621923)，但是广西天地图却并没有提供更多的底图。

- [小图标切片](http://www.mapgx.com:80/ime-gx/images/ditu.jpg)
- [右键菜单](http://blog.csdn.net/yu13767514297/article/details/47125767)
- [唯一靠谱的右键菜单](https://stackoverflow.com/questions/24682024/right-click-on-feature-vector-javascript-in-open-layer)



