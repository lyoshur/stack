---
title: Cesium加载图层
description: Cesium加载图层
slug: Cesium加载图层
date: 2022-03-06 00:00:00+0000
categories:
    - Other
tags:
    - Cesium
---

## Cesium加载图层

## 1.协议介绍

- tms 瓦片地图服务

  - 【大佬解释的】可以理解为一种特殊情况下的XYZ
  - 【大佬解释的】可以直接以文件的形式进行发布，用 tilemapresource.xml 来描述图层信息
  - TMS是 Tile Map Service 的简写，是一种瓦片服务。由开源地理空间信息基金会（OSGEO）开发的平铺web地图规范。这个定义通常需要一个URL结构来尝试REST原则，TMS协议填补了OpenStreetMap使用的非常简单的标准和Web地图服务标准飞复杂性之间的空白，提供了简单的URL到分幅，同事还支持备用空间引用系统。
  - 纯RESTFUL
  - 以左下角为原点
  - 瓦片是正方形的
- xyz

  - 【大佬解释的】不同服务提供商的XYZ值可能不一样
- x表示横坐标  y表示纵坐标  z表示地图层级
- wms 网络地图服务
  - 【大佬解释的】很少用，效率不高
  - 由开发地理信息联盟（OGC）指定
- wmts 网络地图瓦片服务

  - 【大佬解释的】必须用服务进行发布
  - 由开发地理信息联盟（OGC）指定。采用缓存技术，能够缓解WebGis服务器端数据处理的压力（对比WMS）
  - 可以有KVP、SOAP和RESTFUL三种
  - 瓦片是矩形的
  - 以左上角为原点
  - 对应不同比例尺瓦片尺寸可以不同
- flatgis

  - 【大佬解释的】恒歌的私有协议
  - 【大佬解释的】如果Cesium需要加载的话，要用服务对协议进行转换解析
- hgbump

  - 【大佬解释的】恒歌的私有协议
  - 【大佬解释的】如果Cesium需要加载的话，要用服务对协议进行转换解析

## 1.加载以文件形式存在的影像数据（TMS）

```javascript
let layers = viewer.scene.imageryLayers;
layers.addImageryProvider(
    new Cesium.TileMapServiceImageryProvider({
        url: <图片的路径>,
        fileExtension: <图片的文件拓展名>
    })
);
```

图片的路径指向到，包含 tilemapresource.xml 文件的文件夹，不需要指定到具体图层。

指向的结构应该类似于

```bash
0
1
2
tilemapresource.xml
```

## 2.加载以接口形式存在的影像数据（XYZ）

```javascript
let layers = viewer.scene.imageryLayers;
layers.addImageryProvider(
    new Cesium.UrlTemplateImageryProvider({
        url: 'http://127.0.0.1:8080/Map?x={x}&y={y}&z={z}'
    })
);
```

## 3.加载以接口形式存在的影像数据（WMS）未验证

```javascript
let layers = viewer.scene.imageryLayers;
layers.addImageryProvider(
    new Cesium.WebMapServiceImageryProvider({
        // 图层服务的地址
        url: 'http://127.0.0.1:8080/Map',
        // 自定义图层名称
        layers: <自定义图层名称>,
        parameters: {
        	"service": "WMS",
        	"format": "image/png",
        	"transparent": true
    	}
    })
);
```

## 4.加载以接口形式存在的影像数据（WMTS）Cesium 1.28

```javascript
var gevm = new Cesium.ProviderViewModel({
    name : 'VMap Earth',
    iconUrl : Cesium.buildModuleUrl('../images/earth.png'),
    tooltip : 'VMap Earth Image offline',
    creationFunction : function() {
        return new Cesium.WebMapTileServiceImageryProvider({
            url : 'http://' + document.domain + ':'
            + window.location.port + '/startlvlone/wmts',
            layer : 'geearth',
            style : 'default',
            format : 'image/jpeg',
            tileMatrixSetID : 'ge',
            tileMatrixLabels : [ '1', '2', '3', '4', '5', '6', '7',
                                '8', '9', '10', '11', '12', '13', '14', '15', '16',
                                '17', '18', '19', '20', '21', '22', '23', '24' ],
            minimumLevel : 1,
            maximumLevel : 24,
            tilingScheme : new Cesium.GeographicTilingScheme(),
            credit : new Cesium.Credit('VMap Earth')
        });
    }
});
var viewer = new Cesium.Viewer('cesiumContainer', {
    selectedImageryProviderViewModel : gevm
});
```

配置后，向外请求的接口地址应该类似于

```bash
http://10.171.136.172:3500/startlvlone/wmts?service=WMTS&version=1.0.0&request=GetTile&tilematrix=3&layer=geearth&style=default&tilerow=3&tilecol=4&tilematrixset=ge&format=image%2Fjpeg
```

参数列表

| 参数名称          | 示例参数值 | 描述         |
| ----------------- | ---------- | ------------ |
| **service**       | WMTS       | 不知道哪来的 |
| **version**       | 1.0.0      | 不知道哪来的 |
| **request**       | GetTile    | 不知道哪来的 |
| **tilematrix**    | 3          | 当前层级     |
| **layer**         | geearth    | 配置参数里的 |
| **style**         | default    | 配置参数里的 |
| **tilerow**       | 3          | 当前所在的行 |
| **tilecol**       | 4          | 当前所在的列 |
| **tilematrixset** | ge         | 配置参数里的 |
| **format**        | image/jpeg | 配置参数里的 |

## 5.加载以接口形式存在的影像数据（WMTS）Cesium 1.79

| 参数名称           | 参数描述         | 备注信息                      |
| ------------------ | ---------------- | ----------------------------- |
| {layer}            | 瓦片图层名称     |                               |
| {style}            | 瓦片风格         |                               |
| {format}           | 图像类别         |                               |
| {TileMatrixSetID}  | 瓦片矩阵集合名称 | 通常为 EPSG:4326 或 EPSG:3857 |
| {TileMatrixLabels} | 瓦片矩阵名称     | 一般为瓦片层级的名称          |
| {TileRow}          | 瓦片行编号       |                               |
| {TileCol}          | 瓦片列编号       |                               |

```javascript
let imageryLayers = viewer.scene.imageryLayers;
let wmtsImageryProvider = new Cesium.WebMapTileServiceImageryProvider({
    url: "http://10.171.136.172:3500/wmts",
    layer: 'geearth',
    style: 'default',
    format: 'image/jpeg',
    tileMatrixSetID: 'ge',
    tileMatrixLabels: [ '1', '2', '3', '4', '5', '6', '7', '8', '9', '10', '11', '12', '13', '14', '15', '16', '17', '18', '19', '20', '21', '22', '23', '24' ],
    minimumLevel : 0,
    maximumLevel : 24,
    tilingScheme : new Cesium.GeographicTilingScheme(),
    credit : new Cesium.Credit('VMap Earth')
});
imageryLayers.addImageryProvider(wmtsImageryProvider);
```

minimumLevel 这里特意写成了 0， 写成 1 在使用 new Cesium.GeographicTilingScheme()时，会导致

```bash
The imagery provider,s rectangle and minimumLevel indicate that there are 8 tiles at the minimum level. Imagery providers with more than four tiles at the minimum level are not supported.
```

